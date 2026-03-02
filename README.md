# EPMS Critical Issues – Documentation of Changes

Dokumentasyon ng mga pagbabagong ginawa batay sa **Critical Issues.txt** at **EPMS-Critical Issues.docx**. Lahat ng pagbabago ay nasa `app/Http/Controllers/ProcurementPendingController.php` maliban kung nakasaad.

---

## 1. Path Traversal Risk sa `downloadAttachment()` (Critical)

**Problema:** Validation gamit ang `strpos()` ay pwedeng ma-bypass; posible ang directory traversal.

**Ano ang pinalitan / idinagdag:**

| Lokasyon | Dating code / behavior | Bagong code / behavior |
|----------|-------------------------|-------------------------|
| Attachment path verification (foreach sa attachments) | Pag-compare: `strpos($normalizedFile, $normalizedInput) !== false \|\| strpos($normalizedInput, $normalizedFile) !== false` | **Tinanggal** ang partial substring match. Ngayon **exact path o basename lang**: `$normalizedFile === $normalizedInput \|\| basename($normalizedFile) === basename($normalizedInput)` |
| Bago i-serve ang file | Walang realpath confinement; `public_path($attachmentPath)` lang | **Idinagdag:** `$realUploadDir = realpath(public_path(config('upload.attachments.upload_dir')))`, `$realFilePath = realpath($filePath)`. Kung `!$realFilePath` o path hindi nagsisimula sa `$realUploadDir` → `abort(403, 'Access denied.')` at log warning. |
| Response | `response()->file($filePath, ...)` at `response()->download($filePath, ...)` | Ginagamit na ang **`$realFilePath`** (resolved path) sa lahat ng file serve/download. |

---

## 2. `exec()` Calls na May User-Adjacent Data (Critical)

**Problema:** Paths na galing sa DB o user-uploaded filenames ay pwedeng maging command injection kung hindi properly escaped.

**Ano ang napalitan:**

- **Walang pinalitan sa structure** – ang code ay **naka-`escapeshellarg()` na** sa buong PowerShell command at may **`escapePathForShell()`** sa mga path bago i-interpolate sa script.
- **Dinokumenta / sinigurado:** Lahat ng `exec()` sa RFQ/Award flow (`generateRfqFileToPath()`, `injectRfqSignatureIntoDocx()`) ay:
  - Gumagamit ng `escapePathForShell($path)` para sa bawat path (PowerShell single-quote safe).
  - Ang full PowerShell command string ay binalot sa `escapeshellarg()` bago ipasa sa `exec()`.
- Kung may bagong path na idadagdag sa future, dapat gamitin pa rin ang `escapePathForShell()` at `escapeshellarg()`.

---

## 3. Double Permission Check sa `downloadRfqDoc()` at `downloadAwardDoc()` (Critical)

**Problema:** Dalawang beses ang `canAccess()` check; redundant DB/cache overhead at pwedeng race condition.

**Ano ang pinalitan:**

| Method | Dating code | Bagong code |
|--------|-------------|-------------|
| `downloadRfqDoc()` | Unang check: `if (!$user->canAccess('procurementpending/download-rfq')) { ... }` → load `$pending` → **Pangalawang check:** `if (!$user->canAccess('procurementpending/download-rfq')) { return redirect()->back()... }` | **Tinanggal** ang pangalawang `canAccess('procurementpending/download-rfq')` block. Isang check na lang bago `findOrFail($rec_id)` at bago lahat ng file logic. |
| `downloadAwardDoc()` | Same pattern: dalawang `canAccess('procurementpending/download-award')` | **Tinanggal** ang pangalawang check. Isang permission check na lang. |

---

## 4. Signature Injection Logic – Brittle / Exploitable (High)

**Problema:** Hardcoded markers (e.g. "Chairperson, Bids and Awards Committee II") – kung may user-controlled content na naglalaman ng ganitong string, pwedeng maling position ang signature.

**Ano ang idinagdag:**

| Lokasyon | Bagong logic |
|----------|--------------|
| `injectRfqSignatureIntoDocx()` (pagkatapos mahanap ang `$firstBlockPos`) | **Guard:** Kung ang unang occurrence ng marker ay nasa **unang 50%** ng dokumento (`$firstBlockPos < floor(strlen($documentXml) * 0.5)`), **hindi na mag-i-inject** – `return` agad. Ibig sabihin, kung may malicious/crafted text sa remarks/project title na nag-match sa marker sa itaas, hindi na gagamitin ang position na iyon. |

*Note:* Ang marker-based logic (hanap ng "Chairperson, Bids and Awards Committee II" atbp.) ay nandyan pa rin; na-hardening lang para hindi mag-inject kapag ang match ay nasa unang kalahati ng doc.

---

## 5. Unguarded `DB::raw()` Subquery sa `index()` at `myTasks()` (High)

**Problema:** `DB::raw('(SELECT attachments FROM project_step_status pss WHERE ...)')` – kung may future na user input na idadagdag sa loob, bypass ang query binding.

**Ano ang pinalitan:**

| Method | Dating code | Bagong code |
|--------|-------------|-------------|
| `index()` | Sa `->select([..., DB::raw('(SELECT attachments FROM project_step_status pss WHERE pss.project_id = ... AND pss.step_id = ... ORDER BY pss.status_id DESC LIMIT 1) as current_step_attachments'), ...])` | **Tinanggal** ang `DB::raw(...)` mula sa `select([...])`. **Idinagdag** pagkatapos: `$query->selectSub(function($sub) { $sub->from('project_step_status as pss')->select('attachments')->whereColumn(...)->whereNotNull('pss.attachments')->where('pss.attachments','!=','')->orderByDesc('pss.status_id')->limit(1); }, 'current_step_attachments');` |
| `myTasks()` | Same `DB::raw(...)` subquery | Same replacement: **selectSub()** na builder-based subquery. |

Result: Same column `current_step_attachments`; wala nang raw string, safe kung magdagdag ng bound parameters later.

---

## 6. Hardcoded User/Designation IDs sa Complete Action (High)

**Problema:** `'assigned_user' => 4`, `'assigned_desig_id' => 2` (BAC Sec) – kung mabago o ma-delete ang user ID 4, mali ang assignment.

**Ano ang pinalitan / idinagdag:**

| Item | Dating code | Bagong code |
|------|-------------|-------------|
| **Bagong helper** | Wala | **`resolveBacSecretariatAssignment(): array`** – nag-query sa `user_tbl` ng unang active user na `user_role_id = 4`, kunin ang `user_id` at `designation_id`. Fallback sa `4` at `2` kung walang mahanap o may exception; may `Log::warning` kapag fallback. |
| `updateStatus()` – payment step completion | `'assigned_user' => 4`, `'assigned_desig_id' => 2` sa pending item update at sa completion status | `$bacSecAssignment = $this->resolveBacSecretariatAssignment()` (called once sa simula ng try). Lahat ng `4` at `2` para sa BAC Sec ay pinalitan ng `$bacSecAssignment['user_id']` at `$bacSecAssignment['designation_id']`. |
| Completion step status (update/create) | `assigned_desig_id => 2`, `assigned_user => 4` | Same: gamit `$bacSecAssignment['designation_id']`, `$bacSecAssignment['user_id']`. |
| Movement records (payment completion at completion step) | `to_desig_id => 2`, `to_user => 4` | Same: `$bacSecAssignment['designation_id']`, `$bacSecAssignment['user_id']`. |
| Auto-create next step (step_id == 2 fallback) | `$nextStepDesigId = 2`, `$nextStepUserId = 4` | `$nextStepDesigId = $bacSecAssignment['designation_id']`, `$nextStepUserId = $bacSecAssignment['user_id']`. |
| Reversion (completion → payment) movement | `from_desig_id => 2`, `from_user => 4` | `$bacSecAssignment['designation_id']`, `$bacSecAssignment['user_id']`. |

---

## 7. Silent File Upload Failures (Medium)

**Problema:** Kapag nag-fail ang save ng attachment, naglo-log lang; user nakakakuha pa rin ng “success” message.

**Ano ang pinalitan / idinagdag:**

| Lokasyon | Dating code | Bagong code |
|----------|-------------|-------------|
| `updateStatus()` – file upload loop | `catch (Exception $e) { Log::error(...); // Continue with the process }` | **Idinagdag** `$attachmentErrors = []` bago ang loop. Sa catch: `$attachmentErrors[] = $file->getClientOriginalName();` (bukod sa existing `Log::error`). |
| Bago `return $this->redirect(...)` (success path) | Walang check para sa failed uploads | **Idinagdag:** `if (!empty($attachmentErrors)) { session()->flash('warning', 'Item successfully ' . $actionText . ', but some attachments failed to upload. Please check the activity log for details.'); }` |

---

## 8. Temp Files Left on Disk sa `injectRfqSignatureIntoDocx()` (Medium)

**Problema:** Kung may exception pagkatapos i-extract pero bago ma-delete ang temp dir, naiiwan ang extracted directory.

**Ano ang pinalitan:**

| Lokasyon | Dating code | Bagong code |
|----------|-------------|-------------|
| `injectRfqSignatureIntoDocx()` body (extract → edit → compress → delete) | Straight execution; `if (is_dir($extractDir)) { $this->deleteDirectory($extractDir); }` sa dulo | Buong block (extract, XML edit, compress) ay **binalot sa `try { ... } finally { ... }`**. Sa **`finally`**: `if (is_dir($extractDir)) { $this->deleteDirectory($extractDir); }`. Kahit mag-throw o maagang `return`, laging na-e-execute ang cleanup. |

---

## 9. `downloadAttachment()` Path Normalization (Medium)

**Problema:** Path tulad ng `uploads/../../etc/passwd` ay pwedeng makapasok sa check dahil sa strpos at prefix logic.

**Ano ang napalitan:**

- **Verification logic:** Na-document na sa **#1** – tinanggal ang `strpos`-based match; exact path o basename na lang.
- **Confinement:** Na-document na sa **#1** – `realpath()` at check na ang resolved path ay under `realpath(public_path(upload_dir))`. Kung hindi, 403 at log.

Walang dagdag file dito; kasama na sa changes ng #1.

---

## 10. Inconsistent Action Logging – Wrong Step Name (Low)

**Problema:** Pagkatapos i-update ang `$pendingItem`, ang `$pendingItem->step_id` ay naka-point na sa *next* step; ang activity log ay nagre-record ng maling step name para sa “complete” action.

**Ano ang pinalitan:**

| Lokasyon | Dating code | Bagong code |
|----------|-------------|-------------|
| `updateStatus()` – simula ng try | Wala | **Idinagdag:** `$originalStepId = $pendingItem->step_id;` |
| Pagkuha ng step para sa log (pagkatapos commit) | `$step = ProcurementSteps::find($pendingItem->step_id);` | **Pinalitan:** `$step = ProcurementSteps::find($originalStepId);` |
| `ActivityLogHelper::log(..., 'step_id' => $pendingItem->step_id, ...)` | `step_id` = current (next) step | **Pinalitan:** `'step_id' => $originalStepId` |

Result: Ang logged step ay yung step na na-complete / na-forward / na-return, hindi yung next step.

---

## 11. `debugUserFiltering()` Exposed sa Production (High)

**Problema:** Endpoint na nagre-return ng user data (including designation) na accessible sa kahit sinong naka-login; enumeration/data exposure risk.

**Ano ang pinalitan:**

| Lokasyon | Dating code | Bagong code |
|----------|-------------|-------------|
| `debugUserFiltering()` – simula ng method | Direktang query at return ng JSON | **Idinagdag sa pinaka-una:** `$user = Auth::user(); if (!$user \|\| $user->user_role_id != 1 \|\| !config('app.debug')) { abort(404); }` |

Result: Endpoint ay naka-gate na sa: naka-login **at** admin (`user_role_id == 1`) **at** `app.debug == true`. Kung hindi, 404 (walang leak na may debug endpoint).

---

## 12. ModSecurity / Apostrophe sa Filenames (Low)

**Problema:** Apostrophe sa filename (e.g. "WOMEN'S MONTH") ay pwedeng mag-cause ng issue sa shell paths kung hindi properly quoted.

**Ano ang naka-implement na (walang bagong file change para dito):**

- Paths na pumapasok sa PowerShell commands ay dinaan na sa `escapePathForShell()` (apostrophe → `''`) at ang buong command ay naka-`escapeshellarg()`. Ito ay na-document na sa **#2**. Walang karagdagang code change para sa item na ito sa doc.

---

## Buod – File at Method na Binago

| File | Methods / area na na-edit |
|------|----------------------------|
| `app/Http/Controllers/ProcurementPendingController.php` | `index()`, `myTasks()`, `downloadRfqDoc()`, `downloadAwardDoc()`, `downloadAttachment()`, `generateRfqFileToPath()` (fallback path), `injectRfqSignatureIntoDocx()`, `updateStatus()`, `debugUserFiltering()`; bagong helper: `resolveBacSecretariatAssignment()` |

---

## Hindi (pa) Binago

- **Refactor ng `updateStatus()` sa mas maliliit na private methods** – in-place improvements lang (BAC assignment, upload warnings, logging); walang extraction pa ng separate methods (e.g. `handleComplete`, `handleForward`) para iwas regression kung walang test suite.
- **Iba pang controllers** – `PPMPController::downloadAttachment()`, `AnnualProcurementPlanController::downloadAttachment()` – hindi pa na-apply ang same `realpath()` confinement; pwedeng gawin sa susunod na pass kung gusto.

---

*Generated: 2025-03-02. Sanggunian: Critical Issues.txt, EPMS-Critical Issues.docx.*
