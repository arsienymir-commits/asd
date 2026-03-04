# Permissions Guide – Per Route

Para ma-access ang isang page o action, kailangan ng **role** ng user na may **permission** na nakalagay sa table sa baba. Ang permission string ay **`segment1/segment2`** ng URL (hal. `procurementpending/sign-pdf-form`).

**Paano mag-add ng permission:** Settings → Permissions → Add (o Roles → [Role] → Permissions). Ilagay ang **Permission** (exact string) at piliin ang **Role Id**. Pagkatapos, **logout at login** ang user.

---

## Home

| Route (URL pattern) | Ginagawa | Permission na kailangan |
|---------------------|----------|-------------------------|
| `GET home` | Home / dashboard | `home/index` |
| `GET home/tracking-status/{id}` | Tracking status ng project | `home/tracking-status` |
| `GET home/assigned-tasks-ajax` | AJAX: assigned tasks | `home/assigned-tasks-ajax` |
| `GET home/member-dashboard-ajax` | AJAX: member dashboard | `home/member-dashboard-ajax` |

---

## Procurement Pending (at Update Status / PDF Signer / Download)

| Route (URL pattern) | Ginagawa | Permission na kailangan |
|---------------------|----------|-------------------------|
| `GET procurementpending` | List ng procurement pending | `procurementpending/index` |
| `GET procurementpending/my-tasks` | My Tasks page | `procurementpending/my-tasks` |
| `GET procurementpending/view/{id}` | View isang pending record | `procurementpending/view` |
| `GET procurementpending/add` | Add form | `procurementpending/add` |
| `POST procurementpending/add` | Save new pending | `procurementpending/add` |
| `GET procurementpending/edit/{id}` | Edit form | `procurementpending/edit` |
| `POST procurementpending/edit/{id}` | Save edit | `procurementpending/edit` |
| `GET procurementpending/delete/{id}` | Delete | `procurementpending/delete` |
| **`GET procurementpending/update-status-form/{id}`** | **Update Status form (page)** | **`procurementpending/update-status-form`** |
| **`POST procurementpending/update-status-form/{id}`** | **Submit update status** | **`procurementpending/update-status-form`** |
| `GET procurementpending/tracking-status-ajax/{id}` | AJAX tracking status | `procurementpending/tracking-status-ajax` |
| **`GET procurementpending/sign-pdf-form/{id}`** | **PDF Signer page (Open PDF Signer)** | **`procurementpending/sign-pdf-form`** *(o automatic kung may `procurementpending/update-status-form`)* |
| **`GET procurementpending/sign-pdf-preview/{id}`** | **PDF sign preview** | **`procurementpending/sign-pdf-preview`** *(o automatic kung may `update-status-form`)* |
| **`POST procurementpending/sign-pdf/{id}`** | **I-submit ang signature sa PDF** | **`procurementpending/sign-pdf`** *(o automatic kung may `update-status-form`)* |
| `GET procurementpending/sign-pdf-preview-position/{id}` | Preview position (internal) | Same as sign-pdf-form / update-status-form |
| `GET procurementpending/sign-pdf-match-image/{id}` | Match image (internal) | Same as sign-pdf-form / update-status-form |
| `GET procurementpending/sign-pdf-matches/{id}` | Sign PDF matches | Same as sign-pdf-form / update-status-form |
| **`GET procurementpending/download-rfq/{id}`** | **Download RFQ file** | **`procurementpending/download-rfq`** *(o automatic kung may `procurementpending/update-status-form`)* |
| **`GET procurementpending/download-award/{id}`** | **Download Award file** | **`procurementpending/download-award`** *(o automatic kung may `update-status-form`)* |
| **`GET procurementpending/download-attachment/{id}`** | **Download attachment** | **`procurementpending/download-attachment`** *(o automatic kung may `update-status-form`)* |
| `POST procurementpending/approve-rfq/{id}` | Approve RFQ | `procurementpending/approve-rfq` |

**Halimbawa:** Para makapag-**sign ng PDF**, kailangan mo ng permission na **`procurementpending/sign-pdf-form`**. Kung may **`procurementpending/update-status-form`** ka na (para sa Update Status page), pwede ka na ring mag-open ng PDF Signer at mag-download nang walang hiwalay na permission.

---

## Procurement Projects

| Route (URL pattern) | Ginagawa | Permission na kailangan |
|---------------------|----------|-------------------------|
| `GET procurementprojects` | Projects list | `procurementprojects/index` |
| `GET procurementprojects/archive` | Archive list | `procurementprojects/archive` |
| `GET procurementprojects/restore/{id}` | Restore from archive | `procurementprojects/restore` |
| `GET procurementprojects/view/{id}` | View project | `procurementprojects/view` |
| `GET procurementprojects/add` | Add project form | `procurementprojects/add` |
| `POST procurementprojects/add` | Save new project | `procurementprojects/add` |
| `GET procurementprojects/edit/{id}` | Edit project | `procurementprojects/edit` |
| `POST procurementprojects/edit/{id}` | Save edit | `procurementprojects/edit` |
| `GET procurementprojects/delete/{id}` | Delete project | `procurementprojects/delete` |
| `GET procurementprojects/download-rfq/{id}/{fileIndex?}` | Download RFQ file | `procurementprojects/download-rfq` |
| `GET procurementprojects/tracking-status-ajax/{id}` | AJAX tracking | `procurementprojects/tracking-status-ajax` |

---

## Procurement Steps

| Route (URL pattern) | Ginagawa | Permission na kailangan |
|---------------------|----------|-------------------------|
| `GET procurementsteps` | Steps list | `procurementsteps/index` |
| `GET procurementsteps/view/{id}` | View step | `procurementsteps/view` |
| `GET procurementsteps/add` | Add step | `procurementsteps/add` |
| `POST procurementsteps/add` | Save step | `procurementsteps/add` |
| `GET procurementsteps/edit/{id}` | Edit step | `procurementsteps/edit` |
| `POST procurementsteps/edit/{id}` | Save edit | `procurementsteps/edit` |
| `GET procurementsteps/delete/{id}` | Delete step | `procurementsteps/delete` |
| `GET procurementsteps/download-document/{step_id}` | Download step document | `procurementsteps/download-document` |

---

## Project Step Status

| Route (URL pattern) | Ginagawa | Permission na kailangan |
|---------------------|----------|-------------------------|
| `GET projectstepstatus` | List | `projectstepstatus/index` |
| `GET projectstepstatus/view/{id}` | View | `projectstepstatus/view` |
| `GET projectstepstatus/add` | Add | `projectstepstatus/add` |
| `POST projectstepstatus/add` | Store | `projectstepstatus/add` |
| `GET projectstepstatus/edit/{id}` | Edit | `projectstepstatus/edit` |
| `POST projectstepstatus/edit/{id}` | Update | `projectstepstatus/edit` |
| `GET projectstepstatus/delete/{id}` | Delete | `projectstepstatus/delete` |

---

## Project Step Movements

| Route (URL pattern) | Ginagawa | Permission na kailangan |
|---------------------|----------|-------------------------|
| `GET projectstepmovements` | List | `projectstepmovements/index` |
| `GET projectstepmovements/view/{id}` | View | `projectstepmovements/view` |
| `GET projectstepmovements/add` | Add | `projectstepmovements/add` |
| `POST projectstepmovements/add` | Store | `projectstepmovements/add` |
| `GET projectstepmovements/edit/{id}` | Edit | `projectstepmovements/edit` |
| `POST projectstepmovements/edit/{id}` | Update | `projectstepmovements/edit` |
| `GET projectstepmovements/delete/{id}` | Delete | `projectstepmovements/delete` |

---

## PPMP

| Route (URL pattern) | Ginagawa | Permission na kailangan |
|---------------------|----------|-------------------------|
| `GET ppmp` | PPMP list | `ppmp/index` |
| `GET ppmp/archive` | Archive | `ppmp/archive` |
| `GET ppmp/restore/{id}` | Restore | `ppmp/restore` |
| `GET ppmp/view/{id}` | View PPMP | `ppmp/view` |
| `GET ppmp/add` | Add PPMP | `ppmp/add` |
| `POST ppmp/add` | Store | `ppmp/add` |
| `GET ppmp/edit/{id}` | Edit PPMP | `ppmp/edit` |
| `POST ppmp/update/{id}` | Update | `ppmp/edit` (same path segment) |
| `GET ppmp/delete/{id}` | Delete | `ppmp/delete` |
| `GET ppmp/export-excel/{id}` | Export Excel | `ppmp/export-excel` |
| `GET ppmp/export-full-excel` | Full Excel export | `ppmp/export-full-excel` |
| `GET ppmp/bulk-pdf` | Bulk PDF | `ppmp/bulk-pdf` |
| `POST ppmp/approve/{id}` | Approve | `ppmp/approve` |
| `POST ppmp/decline/{id}` | Decline | `ppmp/decline` |

*Note: `ppmp/download-attachment` ay withoutMiddleware(['rbac']), walang permission check.*

---

## APP (Annual Procurement Plan)

| Route (URL pattern) | Ginagawa | Permission na kailangan |
|---------------------|----------|-------------------------|
| `GET app` | APP list | `app/index` |
| `GET app/archive` | Archive | `app/archive` |
| `GET app/restore/{id}` | Restore | `app/restore` |
| `GET app/view/{id}` | View APP | `app/view` |
| `GET app/add` | Add APP | `app/add` |
| `POST app/add` | Store | `app/add` |
| `GET app/edit/{id}` | Edit APP | `app/edit` |
| `POST app/update/{id}` | Update | `app/edit` |
| `GET app/delete/{id}` | Delete | `app/delete` |
| `GET app/pdf/{id}` | Download PDF | `app/pdf` |
| `GET app/bulk-pdf` | Bulk PDF | `app/bulk-pdf` |
| `GET app/export-excel/{id}` | Export Excel | `app/export-excel` |
| `GET app/export-full-excel` | Full Excel | `app/export-full-excel` |

*Note: `app/download-attachment` ay withoutMiddleware(['rbac']).*

---

## Accounts (User) at Account

| Route (URL pattern) | Ginagawa | Permission na kailangan |
|---------------------|----------|-------------------------|
| `GET usertbl` | User list | `usertbl/index` |
| `GET usertbl/archive` | User archive | `usertbl/archive` |
| `GET usertbl/restore/{id}` | Restore user | `usertbl/restore` |
| `GET usertbl/view/{id}` | View user | `usertbl/view` |
| `GET usertbl/add` | Add user | `usertbl/add` |
| `POST usertbl/add` | Store user | `usertbl/add` |
| `GET usertbl/edit/{id}` | Edit user | `usertbl/edit` |
| `POST usertbl/edit/{id}` | Update user | `usertbl/edit` |
| `GET usertbl/delete/{id}` | Delete user | `usertbl/delete` |
| `GET account` | My account / profile | `account/index` |
| `GET account/edit` | Edit my profile | `account/edit` |
| `POST account/edit` | Save profile | `account/edit` |

*Note: `account/esign` at `account/esignStore` ay withoutMiddleware(['rbac']) – lahat ng naka-login pwedeng mag-set ng e-signature.*

---

## Roles at Permissions

| Route (URL pattern) | Ginagawa | Permission na kailangan |
|---------------------|----------|-------------------------|
| `GET roles` | Roles list | `roles/index` |
| `GET roles/view/{id}` | View role | `roles/view` |
| `GET roles/add` | Add role | `roles/add` |
| `POST roles/add` | Store role | `roles/add` |
| `GET roles/edit/{id}` | Edit role | `roles/edit` |
| `POST roles/edit/{id}` | Update role | `roles/edit` |
| `GET roles/delete/{id}` | Delete role | `roles/delete` |
| `GET permissions` | Permissions list | `permissions/index` |
| `GET permissions/view/{id}` | View permission | `permissions/view` |
| `GET permissions/add` | Add permission | `permissions/add` |
| `POST permissions/add` | Store permission | `permissions/store` *(segment2 = add para sa GET, store sa POST – check: POST store ay same route add, so permission ay often permissions/add)* |
| `GET permissions/edit/{id}` | Edit permission | `permissions/edit` |
| `POST permissions/edit/{id}` | Update permission | `permissions/edit` |
| `GET permissions/delete/{id}` | Delete permission | `permissions/delete` |
| `POST permissions/delete/{id}` | Delete (post) | `permissions/delete` |

*Note: Laravel segment(2) for POST to same URL is still the second segment of the path (e.g. edit), so permission stays `permissions/edit`.*

---

## Division

| Route (URL pattern) | Ginagawa | Permission na kailangan |
|---------------------|----------|-------------------------|
| `GET division` | Division list | `division/index` |
| `GET division/view/{id}` | View division | `division/view` |
| `GET division/add` | Add division | `division/add` |
| `POST division/add` | Store | `division/add` |
| `GET division/edit/{id}` | Edit division | `division/edit` |
| `POST division/edit/{id}` | Update | `division/edit` |
| `GET division/delete/{id}` | Delete | `division/delete` |

---

## Designation

| Route (URL pattern) | Ginagawa | Permission na kailangan |
|---------------------|----------|-------------------------|
| `GET designationtbl` | List | `designationtbl/index` |
| `GET designationtbl/view/{id}` | View | `designationtbl/view` |
| `GET designationtbl/add` | Add | `designationtbl/add` |
| `POST designationtbl/add` | Store | `designationtbl/add` |
| `GET designationtbl/edit/{id}` | Edit | `designationtbl/edit` |
| `POST designationtbl/edit/{id}` | Update | `designationtbl/edit` |
| `GET designationtbl/delete/{id}` | Delete | `designationtbl/delete` |

---

## BAC Teams

| Route (URL pattern) | Ginagawa | Permission na kailangan |
|---------------------|----------|-------------------------|
| `GET bacteams` | List | `bacteams/index` |
| `GET bacteams/view/{id}` | View | `bacteams/view` |
| `GET bacteams/add` | Add | `bacteams/add` |
| `POST bacteams/add` | Store | `bacteams/add` |
| `GET bacteams/edit/{id}` | Edit | `bacteams/edit` |
| `POST bacteams/edit/{id}` | Update | `bacteams/edit` |
| `GET bacteams/delete/{id}` | Delete | `bacteams/delete` |

---

## Procurement Categories

| Route (URL pattern) | Ginagawa | Permission na kailangan |
|---------------------|----------|-------------------------|
| `GET procurementcategories` | List | `procurementcategories/index` |
| `GET procurementcategories/view/{id}` | View | `procurementcategories/view` |
| `GET procurementcategories/add` | Add | `procurementcategories/add` |
| `POST procurementcategories/add` | Store | `procurementcategories/add` |
| `GET procurementcategories/edit/{id}` | Edit | `procurementcategories/edit` |
| `POST procurementcategories/edit/{id}` | Update | `procurementcategories/edit` |
| `GET procurementcategories/delete/{id}` | Delete | `procurementcategories/delete` |

---

## Project Type

| Route (URL pattern) | Ginagawa | Permission na kailangan |
|---------------------|----------|-------------------------|
| `GET projecttype` | List | `projecttype/index` |
| `GET projecttype/view/{id}` | View | `projecttype/view` |
| `GET projecttype/add` | Add | `projecttype/add` |
| `POST projecttype/add` | Store | `projecttype/add` |
| `GET projecttype/edit/{id}` | Edit | `projecttype/edit` |
| `POST projecttype/edit/{id}` | Update | `projecttype/edit` |
| `GET projecttype/delete/{id}` | Delete | `projecttype/delete` |

---

## Signatories (RFQ, PPMP, APP, Award)

| Route (URL pattern) | Ginagawa | Permission na kailangan |
|---------------------|----------|-------------------------|
| `GET rfqsignatory` | RFQ Signatory list | `rfqsignatory/index` |
| `GET rfqsignatory/view/{id}` | View | `rfqsignatory/view` |
| `GET rfqsignatory/add` | Add | `rfqsignatory/add` |
| `POST rfqsignatory/add` | Store | `rfqsignatory/add` |
| `GET rfqsignatory/edit/{id}` | Edit | `rfqsignatory/edit` |
| `GET rfqsignatory/delete/{id}` | Delete | `rfqsignatory/delete` |
| `GET ppmpsignatory` | PPMP Signatory list | `ppmpsignatory/index` |
| `GET ppmpsignatory/view/{id}` | View | `ppmpsignatory/view` |
| `GET ppmpsignatory/add` | Add | `ppmpsignatory/add` |
| … (edit, delete) | … | `ppmpsignatory/edit`, `ppmpsignatory/delete` |
| `GET appsignatory` | APP Signatory list | `appsignatory/index` |
| … | View/Add/Edit/Delete | `appsignatory/view`, `appsignatory/add`, `appsignatory/edit`, `appsignatory/delete` |
| `GET awardsignatory` | Award Signatory list | `awardsignatory/index` |
| … | View/Add/Edit/Delete | `awardsignatory/view`, `awardsignatory/add`, `awardsignatory/edit`, `awardsignatory/delete` |

---

## Procurement Process

| Route (URL pattern) | Ginagawa | Permission na kailangan |
|---------------------|----------|-------------------------|
| `GET procurementprocess` | List | `procurementprocess/index` |
| `GET procurementprocess/view/{id}` | View | `procurementprocess/view` |
| `GET procurementprocess/add` | Add | `procurementprocess/add` |
| `POST procurementprocess/add` | Store | `procurementprocess/add` |
| `GET procurementprocess/edit/{id}` | Edit | `procurementprocess/edit` |
| `GET procurementprocess/delete/{id}` | Delete | `procurementprocess/delete` |

---

## Reports at Activity / Security

| Route (URL pattern) | Ginagawa | Permission na kailangan |
|---------------------|----------|-------------------------|
| `GET activitylog` | Activity Log list | `activitylog/index` |
| `GET procurementreport` | Procurement Report | `procurementreport/index` |
| `GET securityauditlog` | Security Audit Log | `securityauditlog/index` |
| `GET securityauditlog/view/{id}` | View log | `securityauditlog/view` |
| `GET securityauditlog/export` | Export | `securityauditlog/export` |

---

## Special Rule (Rbac)

- **Kung may `procurementpending/update-status-form`** ang role, automatic na pwedeng i-access (walang hiwalay na permission) ang:
  - `procurementpending/sign-pdf-form`
  - `procurementpending/sign-pdf-preview`
  - `procurementpending/sign-pdf`
  - `procurementpending/sign-pdf-match-image`, `sign-pdf-preview-position`, `sign-pdf-matches`
  - `procurementpending/download-rfq`
  - `procurementpending/download-award`
  - `procurementpending/download-attachment`

---

## Quick Example: PDF Sign

**Para makapag-sign ng PDF**, kailangan mo ng permission na:

- **`procurementpending/sign-pdf-form`**

O kung may **Update Status** permission ka na:

- **`procurementpending/update-status-form`**

— enough na iyon; pwede ka nang mag-open ng PDF Signer at mag-submit ng signature.

---

*Format ng permission: `segment1/segment2` ng URL (lowercase). Hal. URL `procurementpending/sign-pdf-form/5` → permission `procurementpending/sign-pdf-form`.*
