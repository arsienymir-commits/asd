# EPMS – Dokumentasyon ng Lahat ng Routes

Listahan ng lahat ng web at API routes sa **Procurement System (EPMS)**, at kung para saan ang bawat isa.  
*File: `routes/web.php`, `routes/api.php`, `routes/channels.php`, `routes/console.php`*

---

## 1. Public / Walang Login (Unauthenticated)

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `/` | `index` | Homepage / landing; may redirect kung naka-login na |
| GET | `index/login` | `login` | Login page |
| POST | `auth/login` | `auth.login` | Submit ng login form |
| GET | `info/about` | — | Static page: About |
| GET | `info/faq` | — | Static page: FAQ |
| GET | `info/features` | — | Static page: Features |
| GET | `info/privacypolicy` | — | Static page: Privacy Policy |
| GET | `info/termsandconditions` | — | Static page: Terms and Conditions |
| GET | `info/changelocale/{locale}` | `info.changelocale` | Palitan ang locale (wika) at redirect pabalik |

---

## 2. Auth (Login/Logout at Account Status)

*May `auth` middleware ang logout; ang iba ay pwedeng ma-access bago o pagkatapos mag-login.*

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| POST | `auth/login` | `auth.login` | Login (duplicate definition sa web.php) |
| ANY | `auth/logout` | `logout` | Logout (naka-auth) |
| GET | `auth/accountcreated` | `accountcreated` | Page pagkatapos ma-create ang account |
| GET | `auth/accountpending` | `accountpending` | Account pending approval |
| GET | `auth/accountblocked` | `accountblocked` | Account blocked |
| GET | `auth/accountinactive` | `accountinactive` | Account inactive |

---

## 3. Authenticated Routes (auth + rbac)

*Lahat nasa ilalim ng `Route::middleware(['auth', 'rbac'])` maliban kung may `withoutMiddleware(['rbac'])`.*

### 3.1 Home / Dashboard

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `home` | `home` | Home/dashboard page |
| GET | `home/tracking-status/{projectId}` | `home.tracking-status` | Kunin tracking status ng project (AJAX) |
| GET | `home/assigned-tasks-ajax` | `home.assigned-tasks-ajax` | Assigned tasks para sa dashboard (AJAX) |
| GET | `home/member-dashboard-ajax` | `home.member-dashboard-ajax` | Member dashboard data (AJAX) |

---

### 3.2 Division

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `division` | `division` | List ng divisions |
| GET | `division/index/{filter?}/{filtervalue?}` | `division.index` | List na may filter |
| GET | `division/view/{rec_id}` | `division.view` | View isang division |
| GET | `division/masterdetail/{rec_id}` | `division.masterdetail` | Master-detail view (walang RBAC) |
| GET | `division/add` | `division.add` | Form para mag-add ng division |
| POST | `division/add` | `division.store` | I-save ang bagong division |
| ANY | `division/edit/{rec_id}` | `division.edit` | Edit division |
| GET | `division/delete/{rec_id}` | — | Delete division |

---

### 3.3 Designation

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `designationtbl` | `designationtbl` | List ng designations |
| GET | `designationtbl/index/{filter?}/{filtervalue?}` | `designationtbl.index` | List na may filter |
| GET | `designationtbl/view/{rec_id}` | `designationtbl.view` | View designation |
| GET | `designationtbl/masterdetail/{rec_id}` | `designationtbl.masterdetail` | Master-detail (walang RBAC) |
| GET | `designationtbl/add` | `designationtbl.add` | Add designation form |
| POST | `designationtbl/add` | `designationtbl.store` | Store new designation |
| ANY | `designationtbl/edit/{rec_id}` | `designationtbl.edit` | Edit designation |
| GET | `designationtbl/delete/{rec_id}` | — | Delete designation |

---

### 3.4 Permissions

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `permissions` | `permissions` | List ng permissions |
| GET | `permissions/index/{filter?}/{filtervalue?}` | `permissions.index` | List na may filter |
| GET | `permissions/view/{rec_id}` | `permissions.view` | View permission |
| GET | `permissions/add` | `permissions.add` | Add permission form |
| POST | `permissions/add` | `permissions.store` | Store new permission |
| ANY | `permissions/edit/{rec_id}` | `permissions.edit` | Edit permission |
| GET / POST / DELETE | `permissions/delete/{rec_id}` | `permissions.delete` | Delete permission |

---

### 3.5 Procurement Pending (Tasks / RFQ / Award / Sign PDF)

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `procurementpending` | `procurementpending` | List ng pending procurement |
| GET | `procurementpending/index/{filter?}/{filtervalue?}` | `procurementpending.index` | List na may filter |
| GET | `procurementpending/my-tasks` | `procurementpending.my-tasks` | “My tasks” view |
| GET | `procurementpending/view/{rec_id}` | `procurementpending.view` | View isang pending record |
| GET | `procurementpending/download-rfq/{rec_id}` | `procurementpending.download-rfq` | I-download RFQ document |
| GET | `procurementpending/download-award/{rec_id}` | `procurementpending.download-award` | I-download Award document |
| GET | `procurementpending/download-attachment/{rec_id}` | `procurementpending.download-attachment` | I-download attachment (may query param para sa file) |
| GET | `procurementpending/sign-pdf-form/{rec_id}` | `procurementpending.sign-pdf-form` | Form para mag-sign ng PDF |
| GET | `procurementpending/sign-pdf-preview/{rec_id}` | `procurementpending.sign-pdf-preview` | Preview ng PDF bago i-sign |
| GET | `procurementpending/sign-pdf-preview-position/{rec_id}` | `procurementpending.sign-pdf-preview-position` | Preview position ng signature |
| GET | `procurementpending/sign-pdf-match-image/{rec_id}` | `procurementpending.sign-pdf-match-image` | Match/retrieve signature image para sa signing |
| POST | `procurementpending/sign-pdf/{rec_id}` | `procurementpending.sign-pdf` | I-submit ang PDF signature |
| POST | `procurementpending/approve-rfq/{rec_id}` | `procurementpending.approve-rfq` | I-approve ang RFQ |
| GET | `procurementpending/add` | `procurementpending.add` | Add pending form |
| POST | `procurementpending/add` | `procurementpending.store` | Store new pending |
| GET | `procurementpending/update-status-form/{rec_id}` | `procurementpending.update-status-form` | Form para i-update status |
| POST | `procurementpending/update-status-form/{rec_id}` | `procurementpending.update-status` | I-save ang status update |
| GET | `procurementpending/tracking-status-ajax/{rec_id}` | `procurementpending.tracking-status-ajax` | AJAX: tracking status ng pending |
| ANY | `procurementpending/edit/{rec_id}` | `procurementpending.edit` | Edit pending |
| GET | `procurementpending/delete/{rec_id}` | — | Delete pending |

---

### 3.6 Procurement Projects

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `procurementprojects` | `procurementprojects` | List ng procurement projects |
| GET | `procurementprojects/index/{filter?}/{filtervalue?}` | `procurementprojects.index` | List na may filter |
| GET | `procurementprojects/archive` | `procurementprojects.archive` | Archive list |
| GET | `procurementprojects/archive/index/{filter?}/{filtervalue?}` | `procurementprojects.archive.index` | Archive list na may filter |
| GET | `procurementprojects/restore/{rec_id}` | `procurementprojects.restore` | I-restore mula archive |
| GET | `procurementprojects/view/{rec_id}` | `procurementprojects.view` | View project |
| GET | `procurementprojects/download-rfq/{rec_id}/{fileIndex?}` | `procurementprojects.download-rfq` | I-download RFQ file (optional file index) |
| GET | `procurementprojects/masterdetail/{rec_id}` | `procurementprojects.masterdetail` | Master-detail (walang RBAC) |
| GET | `procurementprojects/add` | `procurementprojects.add` | Add project form |
| POST | `procurementprojects/add` | `procurementprojects.store` | Store new project |
| ANY | `procurementprojects/edit/{rec_id}` | `procurementprojects.edit` | Edit project |
| GET | `procurementprojects/delete/{rec_id}` | — | Delete project |
| GET | `procurementprojects/tracking-status-ajax/{rec_id}` | `procurementprojects.tracking-status-ajax` | AJAX: tracking status ng project |

---

### 3.7 Procurement Steps

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `procurementsteps` | `procurementsteps` | List ng procurement steps |
| GET | `procurementsteps/index/{filter?}/{filtervalue?}` | `procurementsteps.index` | List na may filter |
| GET | `procurementsteps/view/{rec_id}` | `procurementsteps.view` | View step |
| GET | `procurementsteps/masterdetail/{rec_id}` | `procurementsteps.masterdetail` | Master-detail (walang RBAC) |
| GET | `procurementsteps/add` | `procurementsteps.add` | Add step form |
| POST | `procurementsteps/add` | `procurementsteps.store` | Store new step |
| ANY | `procurementsteps/edit/{rec_id}` | `procurementsteps.edit` | Edit step |
| GET | `procurementsteps/delete/{rec_id}` | — | Delete step |
| GET | `procurementsteps/download-document/{step_id}` | `procurementsteps.download-document` | I-download step document |

---

### 3.8 Project Step Movements

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `projectstepmovements` | `projectstepmovements` | List ng step movements |
| GET | `projectstepmovements/index/{filter?}/{filtervalue?}` | `projectstepmovements.index` | List na may filter |
| GET | `projectstepmovements/view/{rec_id}` | `projectstepmovements.view` | View movement |
| GET | `projectstepmovements/add` | `projectstepmovements.add` | Add form |
| POST | `projectstepmovements/add` | `projectstepmovements.store` | Store |
| ANY | `projectstepmovements/edit/{rec_id}` | `projectstepmovements.edit` | Edit |
| GET | `projectstepmovements/delete/{rec_id}` | — | Delete |

---

### 3.9 Project Step Status

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `projectstepstatus` | `projectstepstatus` | List ng step statuses |
| GET | `projectstepstatus/index/{filter?}/{filtervalue?}` | `projectstepstatus.index` | List na may filter |
| GET | `projectstepstatus/view/{rec_id}` | `projectstepstatus.view` | View status |
| GET | `projectstepstatus/masterdetail/{rec_id}` | `projectstepstatus.masterdetail` | Master-detail (walang RBAC) |
| GET | `projectstepstatus/add` | `projectstepstatus.add` | Add form |
| POST | `projectstepstatus/add` | `projectstepstatus.store` | Store |
| ANY | `projectstepstatus/edit/{rec_id}` | `projectstepstatus.edit` | Edit |
| GET | `projectstepstatus/delete/{rec_id}` | — | Delete |

---

### 3.10 Roles

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `roles` | `roles` | List ng roles |
| GET | `roles/index/{filter?}/{filtervalue?}` | `roles.index` | List na may filter |
| GET | `roles/view/{rec_id}` | `roles.view` | View role |
| GET | `roles/masterdetail/{rec_id}` | `roles.masterdetail` | Master-detail (walang RBAC) |
| GET | `roles/add` | `roles.add` | Add role form |
| POST | `roles/add` | `roles.store` | Store new role |
| ANY | `roles/edit/{rec_id}` | `roles.edit` | Edit role |
| GET | `roles/delete/{rec_id}` | — | Delete role |

---

### 3.11 User (UserTbl) at Account

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `usertbl` | `usertbl` | List ng users |
| GET | `usertbl/index/{filter?}/{filtervalue?}` | `usertbl.index` | List na may filter |
| GET | `usertbl/archive` | `usertbl.archive` | Archived users list |
| GET | `usertbl/archive/index/{filter?}/{filtervalue?}` | `usertbl.archive.index` | Archive list na may filter |
| GET | `usertbl/restore/{rec_id}` | `usertbl.restore` | I-restore user mula archive |
| GET | `usertbl/view/{rec_id}` | `usertbl.view` | View user |
| GET | `usertbl/masterdetail/{rec_id}` | `usertbl.masterdetail` | Master-detail (walang RBAC) |
| GET | `account` | `account` | Current user account (index) |
| ANY | `account/edit` | `account.edit` | Edit own account |
| GET | `account/esign` | `account.esign` | E-signature setup page (walang RBAC) |
| POST | `account/esign` | `account.esign.store` | I-save e-signature (walang RBAC) |
| GET | `usertbl/add` | `usertbl.add` | Add user form |
| POST | `usertbl/add` | `usertbl.store` | Store new user |
| ANY | `usertbl/edit/{rec_id}` | `usertbl.edit` | Edit user |
| GET | `usertbl/delete/{rec_id}` | — | Delete user |
| GET | `usertbl/change-password/{rec_id}` | `usertbl.change-password` | Change password form |
| POST | `usertbl/change-password/{rec_id}` | `usertbl.change-password.update` | I-update password |

---

### 3.12 BAC Teams

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `bacteams` | `bacteams` | List ng BAC teams |
| GET | `bacteams/index/{filter?}/{filtervalue?}` | `bacteams.index` | List na may filter |
| GET | `bacteams/view/{rec_id}` | `bacteams.view` | View BAC team |
| GET | `bacteams/masterdetail/{rec_id}` | `bacteams.masterdetail` | Master-detail (walang RBAC) |
| GET | `bacteams/add` | `bacteams.add` | Add form |
| POST | `bacteams/add` | `bacteams.store` | Store |
| ANY | `bacteams/edit/{rec_id}` | `bacteams.edit` | Edit |
| GET | `bacteams/delete/{rec_id}` | — | Delete |

---

### 3.13 Procurement Categories

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `procurementcategories` | `procurementcategories` | List ng procurement categories |
| GET | `procurementcategories/index/{filter?}/{filtervalue?}` | `procurementcategories.index` | List na may filter |
| GET | `procurementcategories/view/{rec_id}` | `procurementcategories.view` | View category |
| GET | `procurementcategories/masterdetail/{rec_id}` | `procurementcategories.masterdetail` | Master-detail (walang RBAC) |
| GET | `procurementcategories/add` | `procurementcategories.add` | Add form |
| POST | `procurementcategories/add` | `procurementcategories.store` | Store |
| ANY | `procurementcategories/edit/{rec_id}` | `procurementcategories.edit` | Edit |
| GET | `procurementcategories/delete/{rec_id}` | — | Delete |

---

### 3.14 Project Type

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `projecttype` | `projecttype` | List ng project types |
| GET | `projecttype/index/{filter?}/{filtervalue?}` | `projecttype.index` | List na may filter |
| GET | `projecttype/view/{rec_id}` | `projecttype.view` | View project type |
| GET | `projecttype/masterdetail/{rec_id}` | `projecttype.masterdetail` | Master-detail (walang RBAC) |
| GET | `projecttype/add` | `projecttype.add` | Add form |
| POST | `projecttype/add` | `projecttype.store` | Store |
| ANY | `projecttype/edit/{rec_id}` | `projecttype.edit` | Edit |
| GET | `projecttype/delete/{rec_id}` | — | Delete |

---

### 3.15 APP (Annual Procurement Plan)

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `app` | `app` | List ng APP |
| GET | `app/index/{filter?}/{filtervalue?}` | `app.index` | List na may filter |
| GET | `app/archive` | `app.archive` | Archive list |
| GET | `app/archive/index/{filter?}/{filtervalue?}` | `app.archive.index` | Archive list na may filter |
| GET | `app/restore/{rec_id}` | `app.restore` | I-restore APP |
| GET | `app/view/{rec_id}` | `app.view` | View APP |
| GET | `app/pdf/{rec_id}` | `app.pdf` | I-download APP PDF |
| GET | `app/bulk-pdf` | `app.bulk-pdf` | Bulk download PDF |
| GET | `app/export-excel/{id}` | `app.export-excel` | Export APP to Excel |
| GET | `app/export-full-excel` | `app.export-full-excel` | Export full APP to Excel |
| GET | `app/download-attachment/{app_req_id}` | `app.download-attachment` | I-download APP attachment (walang RBAC) |
| GET | `app/add` | `app.add` | Add APP form |
| POST | `app/add` | `app.store` | Store new APP |
| GET | `app/edit/{app_id}` | `app.edit` | Edit APP form |
| POST | `app/update/{app_id}` | `app.update` | Update APP |
| GET / DELETE | `app/delete/{app_id}` | `app.delete` | Delete APP |

---

### 3.16 PPMP

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `ppmp` | `ppmp` | List ng PPMP |
| GET | `ppmp/index/{filter?}/{filtervalue?}` | `ppmp.index` | List na may filter |
| GET | `ppmp/archive` | `ppmp.archive` | Archive list |
| GET | `ppmp/archive/index/{filter?}/{filtervalue?}` | `ppmp.archive.index` | Archive list na may filter |
| GET | `ppmp/restore/{rec_id}` | `ppmp.restore` | I-restore PPMP |
| GET | `ppmp/view/{rec_id}` | `ppmp.view` | View PPMP |
| GET | `ppmp/export-excel/{id}` | `ppmp.export-excel` | Export PPMP to Excel |
| GET | `ppmp/export-full-excel` | `ppmp.export-full-excel` | Export full PPMP to Excel |
| GET | `ppmp/bulk-pdf` | `ppmp.bulk-pdf` | Bulk download PPMP PDF |
| GET | `ppmp/download-attachment/{ppmp_req_id}/{file_index?}` | `ppmp.download-attachment` | I-download PPMP attachment (walang RBAC) |
| GET | `ppmp/add` | `ppmp.add` | Add PPMP form |
| POST | `ppmp/add` | `ppmp.store` | Store new PPMP |
| GET | `ppmp/edit/{ppmp_id}` | `ppmp.edit` | Edit PPMP form |
| POST | `ppmp/update/{ppmp_id}` | `ppmp.update` | Update PPMP |
| GET / DELETE | `ppmp/delete/{ppmp_id}` | `ppmp.delete` | Delete PPMP |
| POST | `ppmp/approve/{ppmp_id}` | `ppmp.approve` | I-approve PPMP |
| POST | `ppmp/decline/{ppmp_id}` | `ppmp.decline` | I-decline PPMP |
| GET | `ppmp/get-ppmp-requests/{ref}` | `ppmp.getRequests` | Kunin PPMP requests by reference |
| GET | `ppmp/get-total-processed/{id}` | `ppmp.getTotalProcessed` | Total processed value para sa PPMP request |

---

### 3.17 Activity Log

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `activitylog` | `activitylog` | List ng activity log |
| GET | `activitylog/index` | `activitylog.index` | Activity log index |
| POST | `activity-log/log-button-click` | `activity-log.log-button-click` | I-log ang button click (walang RBAC) |

---

### 3.18 RFQ Signatory

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `rfqsignatory` | `rfqsignatory` | List ng RFQ signatories |
| GET | `rfqsignatory/index/{filter?}/{filtervalue?}` | `rfqsignatory.index` | List na may filter |
| GET | `rfqsignatory/view/{rec_id}` | `rfqsignatory.view` | View RFQ signatory |
| GET | `rfqsignatory/add` | `rfqsignatory.add` | Add form |
| POST | `rfqsignatory/add` | `rfqsignatory.store` | Store |
| ANY | `rfqsignatory/edit/{rec_id}` | `rfqsignatory.edit` | Edit |
| GET | `rfqsignatory/delete/{rec_id}` | `rfqsignatory.delete` | Delete |

---

### 3.19 PPMP Signatory

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `ppmpsignatory` | `ppmpsignatory` | List ng PPMP signatories |
| GET | `ppmpsignatory/index/{filter?}/{filtervalue?}` | `ppmpsignatory.index` | List na may filter |
| GET | `ppmpsignatory/view/{rec_id}` | `ppmpsignatory.view` | View PPMP signatory |
| GET | `ppmpsignatory/add` | `ppmpsignatory.add` | Add form |
| POST | `ppmpsignatory/add` | `ppmpsignatory.store` | Store |
| ANY | `ppmpsignatory/edit/{rec_id}` | `ppmpsignatory.edit` | Edit |
| GET | `ppmpsignatory/delete/{rec_id}` | `ppmpsignatory.delete` | Delete |

---

### 3.20 APP Signatory

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `appsignatory` | `appsignatory` | List ng APP signatories |
| GET | `appsignatory/index/{filter?}/{filtervalue?}` | `appsignatory.index` | List na may filter |
| GET | `appsignatory/view/{rec_id}` | `appsignatory.view` | View APP signatory |
| GET | `appsignatory/add` | `appsignatory.add` | Add form |
| POST | `appsignatory/add` | `appsignatory.store` | Store |
| ANY | `appsignatory/edit/{rec_id}` | `appsignatory.edit` | Edit |
| GET | `appsignatory/delete/{rec_id}` | `appsignatory.delete` | Delete |

---

### 3.21 Award Signatory

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `awardsignatory` | `awardsignatory` | List ng Award signatories |
| GET | `awardsignatory/index/{filter?}/{filtervalue?}` | `awardsignatory.index` | List na may filter |
| GET | `awardsignatory/view/{rec_id}` | `awardsignatory.view` | View Award signatory |
| GET | `awardsignatory/add` | `awardsignatory.add` | Add form |
| POST | `awardsignatory/add` | `awardsignatory.store` | Store |
| ANY | `awardsignatory/edit/{rec_id}` | `awardsignatory.edit` | Edit |
| GET | `awardsignatory/delete/{rec_id}` | `awardsignatory.delete` | Delete |

---

### 3.22 Procurement Process

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `procurementprocess` | `procurementprocess.index` | List ng procurement process |
| GET | `procurementprocess/index/{filter?}/{filtervalue?}` | `procurementprocess.list` | List na may filter |
| GET | `procurementprocess/view/{pp_id}` | `procurementprocess.view` | View process |
| GET | `procurementprocess/add` | `procurementprocess.add` | Add form |
| POST | `procurementprocess/add` | `procurementprocess.store` | Store |
| ANY | `procurementprocess/edit/{pp_id}` | `procurementprocess.edit` | Edit |
| GET | `procurementprocess/delete/{pp_id}` | `procurementprocess.delete` | Delete |

---

### 3.23 Procurement Report

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `procurementreport` | `procurementreport` | Procurement report page |
| GET | `procurementreport/index` | `procurementreport.index` | Report index |

---

### 3.24 Security Audit Log

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| GET | `securityauditlog` | `securityauditlog` | List ng security audit log |
| GET | `securityauditlog/index` | `securityauditlog.index` | Audit log index |
| GET | `securityauditlog/view/{log_id}` | `securityauditlog.view` | View isang log entry |
| GET | `securityauditlog/export` | `securityauditlog.export` | I-export audit log |

---

### 3.25 File Uploader (auth + rbac, pero walang RBAC sa route)

| Method | URI | Route Name | Para saan |
|--------|-----|------------|-----------|
| POST | `fileuploader/upload/{fieldname}` | — | Upload file (walang RBAC) |
| POST | `fileuploader/s3upload/{fieldname}` | — | S3 upload (walang RBAC) |
| POST | `fileuploader/remove_temp_file` | — | Tanggalin temporary file (walang RBAC) |

---

## 4. Components Data (Dropdown / Options) – auth only

*Ginagamit para sa option lists at validation (e.g. Select2, duplicate check).*

| Method | URI | Para saan |
|--------|-----|-----------|
| GET | `componentsdata/role_id_option_list` | Option list para sa role_id |
| GET | `componentsdata/project_id_option_list` | Option list para sa project_id |
| GET | `componentsdata/step_id_option_list` | Option list para sa step_id |
| GET | `componentsdata/status_id_option_list` | Option list para sa status_id |
| GET | `componentsdata/assigned_desig_id_option_list` | Option list para sa assigned designation |
| GET | `componentsdata/division_id_option_list` | Option list para sa division_id |
| GET | `componentsdata/assigned_user_option_list` | Option list para sa assigned user |
| GET | `componentsdata/usertbl_full_name_value_exist` | Check kung may existing full_name |
| GET | `componentsdata/usertbl_email_address_value_exist` | Check kung may existing email |

---

## 5. Web Helper / API-like (auth only, sa web.php)

| Method | URI | Para saan |
|--------|-----|-----------|
| GET | `api/categories/{category_id}/bac-team` | JSON: bac_team_id ng category (web) |
| GET | `api/app/search` | JSON: search APP by title (Select2-style) |
| GET | `api/users/search` | JSON: search users by full_name (Select2-style) |
| GET | `api/app/{app_id}/projects-and-budget` | JSON: projects at budget info para sa APP |

---

## 6. API Routes (`routes/api.php`)

*Kung may prefix ang API (e.g. `/api`), idinagdag sa baba.*

| Method | URI | Para saan |
|--------|-----|-----------|
| GET | `/user` (auth:sanctum) | I-return ang current user (Sanctum) |
| GET | `/categories/{category_id}/bac-team` | JSON: bac_team_id ng procurement category |
| GET | `/procurement-methods/{pp_id}/steps` | JSON: procurement steps by method (pp_id), may assigned user |
| GET | `/projects/{project_id}/progress` | JSON: project progress (current step, total steps, % completion) |
| GET | `/steps/{step_id}/form-fields` | JSON: form fields para sa step |

---

## 7. Broadcast Channels (`routes/channels.php`)

| Channel | Para saan |
|---------|-----------|
| `App.Models.User.{id}` | Authorize user para makinig sa kanilang own user channel (e.g. notifications). |

---

## 8. Console Routes (`routes/console.php`)

| Command | Para saan |
|---------|-----------|
| `php artisan inspire` | Display inspiring quote (Laravel default). |

---

## Maikling Glossary

- **APP** – Annual Procurement Plan  
- **PPMP** – Project Procurement Management Plan  
- **RFQ** – Request for Quotation  
- **BAC** – Bids and Awards Committee  
- **RBAC** – Role-Based Access Control (middleware na nag-check ng permissions)  
- **rec_id** – Record ID (primary key ng record)  
- **masterdetail** – View na may master-detail relationship (minsan walang RBAC para sa embedded data)

Kung may binago o nadagdag na route, i-update ang `routes/web.php` o `routes/api.php` at ang dokumentasyong ito.
