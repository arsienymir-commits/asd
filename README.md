# PDF Signer – Search/Matches 500 Error (Online) – Code Checklist

Local: **working 100%**.  
Online: **500 Internal Server Error** kapag nag-search sa "Text to check in PDF" (sign-pdf-matches).  
User may permission naman. Goal: **i-verify sa online server** kung tugma ang code at config.

---

## 1. Request na nag-fa-fail

- **Method/URL:** `GET /procurementpending/sign-pdf-matches/{rec_id}?search_text=...`
- **Halimbawa:** `https://epms.primesys.dev/procurementpending/sign-pdf-matches/123?search_text=info`
- **Handler:** `ProcurementPdfSignController@signPdfMatches`

---

## 2. Route – dapat pareho sa online

**File:** `routes/web.php`

- **Line ~90:**  
  `Route::get('procurementpending/sign-pdf-matches/{rec_id}', 'ProcurementPdfSignController@signPdfMatches')->name('procurementpending.sign-pdf-matches');`

Tingnan sa online: same route definition at walang middleware na nag-block sa GET na ito.

---

## 3. Middleware (RBAC) – permission para sa sign-pdf-matches

**File:** `app/Http/Middleware/Rbac.php`

- **Line ~44–45:**  
  `$page_path = strtolower("$page/$action");`  
  Para sa URL na `.../procurementpending/sign-pdf-matches/123`, dapat `$page_path === 'procurementpending/sign-pdf-matches'`.

- **Line ~114:**  
  `} elseif (in_array($page_path, ['procurementpending/sign-pdf-match-image', 'procurementpending/sign-pdf-preview-position', 'procurementpending/sign-pdf-matches'], true)) {`

- **Line ~116–119:**  
  Allow kung may: `sign-pdf-form` **OR** `update-status-form` **OR** `sign-pdf`.

Sa online: i-check na may isa sa tatlong permission ang user at na same ang string na `procurementpending/sign-pdf-matches` (typo-free).

---

## 4. Controller – `signPdfMatches` at lahat ng tinatawag nito

**File:** `app/Http/Controllers/ProcurementPdfSignController.php`

### 4.1 Entry point: `signPdfMatches`

- **Lines 719–762** – buong method `signPdfMatches($rec_id)`:
  - Auth check
  - `userCanUseSignPdf($user)`
  - `ProcurementPending::findOrFail($rec_id)`
  - `getLatestPdf($pending)` → kung wala, JSON 404
  - `getLatestPdfAttachment($pending, true)` → `$pdfPathForText`
  - `request('search_text', '')`
  - `computePdfMatches($pdfPathForText, $searchText)`
  - `return response()->json(['matches' => $matches])`

Kung 500, may **exception** sa loob ng flow na ito (auth/permission na lang 401/403, kaya 500 = PHP error).

### 4.2 Helpers na direktang ginagamit ng `signPdfMatches`

| Method | Line(s) | Ginagawa |
|--------|--------|----------|
| `userCanUseSignPdf($user)` | 24–30 | Permission check (sign-pdf-form / update-status-form / sign-pdf) |
| `getLatestPdf($pending)` | 162–169 | Kunin latest PDF; tumatawag sa `getLatestPdfAttachment` |
| `getLatestPdfAttachment($pending, true)` | 129–155 | Latest PDF attachment, prefer unsigned; tumatawag sa `getAttachmentsFlatList`, `resolveAttachmentPath` |
| `computePdfMatches($pdfPathForText, $searchText)` | 588–627 | Text match logic; tumatawag sa `extractPdfWordsBbox`, `normalizeText`, `findOccurrencesInPdfLayout` |

### 4.3 Path at config (mahalaga sa online)

- **resolveAttachmentPath($pathFromDb)** – **Lines 74–99**  
  - Gumagamit ng `config('upload.attachments.upload_dir', 'uploads/attachments')`.  
  - Sa online: dapat may `config/upload.php` (o kung saan naka-define ang `upload.attachments.upload_dir`) at tama ang path sa server (e.g. `public_path($c)` na naa-access ng web app).

- **getLatestPdfAttachment** – **129–155**  
  - Umaasa sa `ProjectStepStatus`, `getAttachmentsFlatList`, `resolveAttachmentPath`.  
  - Kung ang path sa DB o filesystem ay mali sa server (e.g. Windows vs Linux, symlink, permissions), pwedeng mag-throw.

### 4.4 PDF text extraction (madalas sanhi ng 500 sa server)

- **getPdftotextPath()** – **Lines 174–193**  
  - `config('app.pdftotext_path')` o `env('PDFTOTEXT_PATH')`.  
  - Fallback: `storage_path('app/bin/pdftotext.exe')`, `base_path('storage/app/bin/pdftotext.exe')` (Windows).  
  - **Sa Linux server:** kadalasan walang `pdftotext.exe`. Dapat naka-set sa **.env** ang `PDFTOTEXT_PATH` (e.g. `/usr/bin/pdftotext`). Kung wala, `getPdftotextPath()` = null (hindi nag-throw).

- **extractPdfWordsBbox($pdfPath)** – **Lines 289–347**  
  - Tumatawag sa `getPdftotextPath()`, `runPdftotext()`, `parseBboxHtml()`.  
  - Kung `pdftotext` missing o failed: nagre-return ng `[]`, hindi 500.  
  - Pwedeng mag-500 kung may **proc_open** restriction sa server o path/permission sa temp file.

- **runPdftotext(array $args)** – **Lines 227–257**  
  - Gumagamit ng `proc_open()`.  
  - Sa online: i-check kung **disabled** ang `proc_open` sa `disable_functions` (php.ini). Kung disabled, dito mag-fail at 500.

- **computePdfMatches** – **588–627**  
  - Kung walang words mula sa bbox, tumatawag sa **findOccurrencesInPdfLayout**.

- **findOccurrencesInPdfLayout($pdfPath, $searchText)** – **Lines 535–577**  
  - Tumatawag sa **extractPdfTextWithPhp($pdfPath)** – **Lines 384–421**.  
  - Gumagamit ng **Smalot\PdfParser\Parser**.  
  - **Sa online:** kung wala ang package na `smalot/pdfparser` o naka-different version, dito pwedeng mag-500 (class not found o exception).

### 4.5 Imports / dependencies (top of controller)

- **Lines 1–17** – `use` statements at dependencies:  
  `ProcurementPending`, `ProjectStepStatus`, `UserTbl`, `Auth`, `Log`, `setasign\Fpdi\Fpdi`, `Intervention\Image\*`, etc.  
  Sa online: kung may missing class (e.g. Fpdi, Image, PdfParser), 500 sa early part ng request.

---

## 5. Frontend – sino tumatawag sa sign-pdf-matches

**File:** `public/js/page-scripts.js`

- **Lines 873–927** – debounced `input` sa `#search-text-input`:
  - `data-sign-pdf-matches-url` mula sa section (galing sa blade).
  - `$.get(matchesUrl, { search_text: searchText })` → GET sa `.../sign-pdf-matches/{rec_id}?search_text=...`

**File:** `resources/views/pages/procurementpending/sign-pdf.blade.php`

- **Line ~5** – `data-sign-pdf-matches-url="{{ route('procurementpending.sign-pdf-matches', $rec_id) }}"`  
  Dapat sa online same route name at `$rec_id` na naipapasa.

---

## 6. Checklist para i-check sa online (wag magpalit ng code, verify lang)

1. **Route**  
   - [ ] `routes/web.php` line ~90: same `sign-pdf-matches/{rec_id}` route at controller method.

2. **RBAC**  
   - [ ] `app/Http/Middleware/Rbac.php` line ~114: kasama ang `'procurementpending/sign-pdf-matches'` sa `in_array`.  
   - [ ] User may isa sa: `procurementpending/sign-pdf-form`, `procurementpending/update-status-form`, `procurementpending/sign-pdf`.

3. **Controller**  
   - [ ] `ProcurementPdfSignController.php` lines 719–762: same `signPdfMatches` logic.  
   - [ ] Same helpers: `getLatestPdf`, `getLatestPdfAttachment`, `resolveAttachmentPath`, `getAttachmentsFlatList` (lines na binanggit sa section 4).

4. **PDF tools (madalas sanhi ng 500)**  
   - [ ] **.env:** may `PDFTOTEXT_PATH` ba (sa Linux, e.g. `/usr/bin/pdftotext`)?  
   - [ ] **php.ini:** `disable_functions` – hindi kasama ang `proc_open` (para sa `runPdftotext`).  
   - [ ] **Composer:** naka-install ang `smalot/pdfparser` (ginagamit ng `findOccurrencesInPdfLayout` → `extractPdfTextWithPhp`).

5. **Paths at permissions**  
   - [ ] `config('upload.attachments.upload_dir')` – tama at naa-access ng app sa server.  
   - [ ] `storage_path('app/')` – writable (temp files ng pdftotext).  
   - [ ] Actual PDF file path na binabasa – may read permission at same path format (Linux vs Windows).

6. **Log para sa exact cause ng 500**  
   - [ ] Sa server: `storage/logs/laravel.log` (o kung saan naka-log ang PHP errors).  
   - Hanapin ang **stack trace** sa oras ng GET sa `sign-pdf-matches`.  
   - Makikita kung: missing class, `proc_open` disabled, file not found, o permission.

---

## 7. Summary – code locations (para tugma sa local)

| File | Location | Purpose |
|------|----------|---------|
| `routes/web.php` | ~line 90 | Route `sign-pdf-matches/{rec_id}` |
| `app/Http/Middleware/Rbac.php` | ~44–45, 114, 116–119 | page_path at allow list para sign-pdf-matches |
| `app/Http/Controllers/ProcurementPdfSignController.php` | 719–762 | `signPdfMatches($rec_id)` |
| Same controller | 24–30 | `userCanUseSignPdf` |
| Same controller | 74–99 | `resolveAttachmentPath` |
| Same controller | 105–124 | `getAttachmentsFlatList` |
| Same controller | 129–155 | `getLatestPdfAttachment` |
| Same controller | 162–169 | `getLatestPdf` |
| Same controller | 174–193 | `getPdftotextPath` |
| Same controller | 227–257 | `runPdftotext` (proc_open) |
| Same controller | 289–347 | `extractPdfWordsBbox` |
| Same controller | 384–421 | `extractPdfTextWithPhp` (Smalot) |
| Same controller | 535–577 | `findOccurrencesInPdfLayout` |
| Same controller | 588–627 | `computePdfMatches` |
| `public/js/page-scripts.js` | 873–927 | AJAX GET sign-pdf-matches |
| `resources/views/pages/procurementpending/sign-pdf.blade.php` | ~line 5 | `data-sign-pdf-matches-url` |

Kapag na-verify na tugma ang code at config sa online (lalo na route, RBAC, PDF path, proc_open, at smalot/pdfparser), gamitin ang **laravel.log** para sa exact exception at line number ng 500.
