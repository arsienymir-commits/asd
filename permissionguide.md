# Permissions Guide – Paano Ma-Access ang Mga Feature

Ito ang gabay kung **anong permission ang kailangan i-allow** para ma-access ng isang user/role ang isang feature (hal. PDF Signer, download, Update Status, atbp.).

---

## Paano Gumagana ang Permissions

- Bawat **route** sa app ay may **permission string** (hal. `procurementpending/update-status-form`).
- Ang **role** ng user (hal. BAC Secretariat, Admin) ay may listahan ng **permissions** sa table `permissions`.
- Kapag pumasok ang user sa isang page o nag-click ng action (download, sign PDF, etc.), chine-check ng **Rbac middleware** kung may permission ang role niya para sa **page_path** na iyon.
- Kung **wala**, lalabas ang "Forbidden" o "You do not have permission to download this document."

**Para ma-access ang isang feature:** kailangan naka-**Add** o naka-**assign** ang tamang **permission** sa **role** ng user (sa **Settings → Roles → [Role] → Permissions**, o sa **Permissions** list → Add/Edit).

---

## Paano Mag-Allow ng Permission sa Isang Role

1. **Login** bilang user na may access sa **Permissions** (hal. Admin).
2. Pumunta sa **Settings → User Settings → Roles** (o direct sa **Permissions** kung naka-link sa menu).
3. **Para mag-add ng bagong permission para sa isang role:**
   - Pumunta sa **Permissions** list at click **Add**, **o**
   - Pumunta sa **Roles → [Piliin ang Role] → Permissions** at i-add doon.
4. Sa form:
   - **Permission** – ilagay ang **exact permission string** (tingnan sa table sa ibaba). Hal: `procurementpending/update-status-form`
   - **Role Id** – piliin ang **role** (hal. BAC Secretariat).
5. **Save / Update**.
6. **Logout at login ulit** ang user (o mag-clear cache) para ma-apply ang bagong permission.

---

## Permission per Feature (Anong I-Allow para Ma-Access)

### Procurement Pending / Update Status / PDF Signer / Downloads

| Gustong ma-access              | Permission na i-allow                         | Notes |
|--------------------------------|-----------------------------------------------|--------|
| **Update Status** (form)      | `procurementpending/update-status-form`       | Kailangan ito para makapasok sa "Update Status - Procurement Pending" at makita ang form. |
| **PDF Signer** (Open PDF Signer)| *Automatic kapag may* `procurementpending/update-status-form` | Hindi na kailangan i-add ang `sign-pdf-form` kung may **Update Status** permission na. Kung wala kang update-status-form, i-allow: `procurementpending/sign-pdf-form`. |
| **Download attachment** (sa Update Status)| *Automatic kapag may* `procurementpending/update-status-form` | Kung wala, i-allow: `procurementpending/download-attachment`. |
| **Download RFQ**               | *Automatic kapag may* `procurementpending/update-status-form` | O i-allow: `procurementpending/download-rfq`. |
| **Download Award**            | *Automatic kapag may* `procurementpending/update-status-form` | O i-allow: `procurementpending/download-award`. |
| **Procurement Pending list**   | `procurementpending/index`                    | Para makita ang list at My Tasks. |
| **View / Add / Edit / Delete** | `procurementpending/view`, `procurementpending/add`, `procurementpending/edit`, `procurementpending/delete` | Ayon sa kung ano ang kailangan ng role. |

**Halimbawa (BAC Secretariat):**  
Para ma-access **lahat** ng nasa Update Status page (form, PDF Signer, download ng attachment/RFQ/award), **sapat na** ang isang permission:

- **`procurementpending/update-status-form`**

Hindi na kailangan i-add nang hiwalay ang:  
`procurementpending/sign-pdf-form`, `procurementpending/download-attachment`, `procurementpending/download-rfq`, `procurementpending/download-award` (unless gusto mo silang i-allow kahit walang Update Status).

---

### Ibang Modules (List / View / Add / Edit / Delete)

Format: `module/action`. Halimbawa:

| Module / feature   | Permission strings (i-allow depende sa kailangan) |
|--------------------|---------------------------------------------------|
| **Home**           | `home/index`                                      |
| **My Tasks**       | `procurementpending/my-tasks`                     |
| **Procurement Projects** | `procurementprojects/index`, `procurementprojects/view`, `procurementprojects/add`, `procurementprojects/edit`, `procurementprojects/delete` |
| **Procurement Steps**    | `procurementsteps/index`, `procurementsteps/view`, `procurementsteps/add`, `procurementsteps/edit`, `procurementsteps/delete` |
| **PPMP**           | `ppmp/index`, `ppmp/view`, `ppmp/add`, `ppmp/edit`, `ppmp/delete` |
| **APP**            | `app/index`, `app/view`, `app/add`, `app/edit`, `app/delete` |
| **Accounts (User)**| `usertbl/index`, `usertbl/view`, `usertbl/add`, `usertbl/edit`, `usertbl/delete` |
| **Roles**          | `roles/index`, `roles/view`, `roles/add`, `roles/edit`, `roles/delete` |
| **Permissions**    | `permissions/index`, `permissions/view`, `permissions/add`, `permissions/edit`, `permissions/delete` |
| **Division**       | `division/index`, `division/view`, `division/add`, `division/edit`, `division/delete` |
| **Designation**    | `designationtbl/index`, `designationtbl/view`, `designationtbl/add`, `designationtbl/edit`, `designationtbl/delete` |
| **BAC Teams**      | `bacteams/index`, `bacteams/view`, `bacteams/add`, `bacteams/edit`, `bacteams/delete` |
| **Activity Log**   | `activitylog/index`                               |
| **Account (profile)** | `account/index`, `account/edit`                 |
| **E-Signature (account)** | Walang Rbac; lahat ng naka-login ay maaaring mag-set ng e-signature. |

---

## Special Rules (Naka-program sa Rbac)

- **May `procurementpending/update-status-form` na:**  
  Pwede na ring ma-access nang **walang hiwalay na permission** ang:
  - PDF Signer: `procurementpending/sign-pdf-form`, `procurementpending/sign-pdf-preview`, `procurementpending/sign-pdf`
  - Downloads: `procurementpending/download-rfq`, `procurementpending/download-award`, `procurementpending/download-attachment`

- **Sign PDF preview (match image / position):**  
  Sinusunod ang permission ng **sign-pdf-form** (o, kung may update-status-form, automatic na allowed).

---

## Quick Reference: PDF Signer

**Tanong:** *Paano ma-open ang PDF Signer sa role na BAC Secretariat?*

**Sagot:**  
1. I-allow ang permission na **`procurementpending/update-status-form`** sa role na **BAC Secretariat** (kung wala pa).  
2. Logout at login ulit ang user.  
3. Pumasok sa **Update Status** ng isang pending item, tapos gamitin ang **Open PDF Signer**.  
Hindi na kailangan i-allow nang hiwalay ang `procurementpending/sign-pdf-form` kapag may **update-status-form** na.

---

## Kung Ayaw Pa Rin (Troubleshooting)

- **Logout at login** – permissions ay na-load sa session/cache.
- **Clear cache:** `php artisan cache:clear` at `php artisan config:clear`.
- **Double-check:** Sa **Permissions** list, filter by **Role** at tiyakin na naka-save talaga ang permission string (exact spelling at format: `module/action`, hal. `procurementpending/update-status-form`).
- **Check role ng user:** Sa **Accounts → User** → tiyakin na tamang **Role** (hal. BAC Secretariat) ang naka-assign.

---

*Huling update: batay sa Rbac at routes ng Procurement System.*
