# Sprint 1 - Role Management - Frontend Task Detail

> **Document Type:** Frontend Task Detail  
> **Status:** Draft  
> **Story / AC:** BR-004, BR-006, BR-008  
> **Design:** [dashboard.png](../../design/dashboard.png); role-specific screens TBD

## Task Overview

| PIC                | Total Mandays |
| ------------------ | ------------: |
| Frontend Developer |          6.00 |
| **Total**          |      **6.00** |

## Task Breakdown

### Role List and Details

| ID          | Task / Deliverable                                                                                                                                                                                                                                    | Related AC             | Mandays | PIC                |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------- | ------: | ------------------ |
| FE-ROLE-001 | Build role table with name, code, status, permission count, and updated time; add paging, search by name/code, status filter, sort controls, and permitted create/detail/update/delete entry points.                                                  | BR-004                 |    1.00 | Frontend Developer |
| FE-ROLE-002 | Integrate `GET /api/admin/roles/v1/list` using page/limit/search/status/sort; support page 1 and limit 10 defaults, max limit 100, search max 100 characters, and the IFA sort fields/directions; render loading, empty, fetch-error, and 429 states. | BR-004, BR-006         |    0.75 | Frontend Developer |
| FE-ROLE-003 | Build role detail view and integrate `GET /api/admin/roles/v1/detail/{roleId}`; display metadata, ACTIVE/INACTIVE status, and resolved permission keys; safely handle 400/401/403/404/500 responses.                                                  | BR-004, BR-006, BR-008 |    0.75 | Frontend Developer |

### Create and Update Role

| ID          | Task / Deliverable                                                                                                                                                                                                                                                      | Related AC     | Mandays | PIC                |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ------: | ------------------ |
| FE-ROLE-004 | Build create-role form for name, code, description, status (default ACTIVE), and searchable permission checkboxes grouped by permission domain from the active Sprint 1 catalog; implement required/max-length and duplicate-selection validation from create contract. | BR-004, BR-006 |    0.75 | Frontend Developer |
| FE-ROLE-005 | Integrate `POST /api/admin/roles/v1/create`; refresh list/detail after success, preserve entered values on validation/conflict/network errors, and show clear success/failure feedback.                                                                                 | BR-004, BR-006 |    0.50 | Frontend Developer |
| FE-ROLE-006 | Build custom-role edit form and integrate `PUT /api/admin/roles/v1/update/{roleId}` for metadata, status, and permissions; allow empty permission set, show unchanged-save success, and handle invalid input/conflicts without discarding edits.                        | BR-004, BR-006 |    0.75 | Frontend Developer |

### Delete and Access Control

| ID          | Task / Deliverable                                                                                                                                                                                                                                  | Related AC     | Mandays | PIC                |
| ----------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ------: | ------------------ |
| FE-ROLE-007 | Add delete confirmation for custom roles and integrate `DELETE /api/admin/roles/v1/delete/{roleId}` with no request body; show success for repeated delete and actionable feedback when role is protected, assigned to active accounts, or missing. | BR-004, BR-006 |    0.50 | Frontend Developer |
| FE-ROLE-008 | Apply role permissions (`role.read/create/update/delete`) to navigation and actions; show `SUPER_ADMIN` details as read-only without edit/disable/delete controls, and handle API 401/403 as authoritative access denial with safe feedback.        | BR-004, BR-006 |    1.00 | Frontend Developer |

## Dependencies

- **Dependencies:** [Role permission model](../../solutioning/dashboard/accounts-roles/permission-model.md), [Role ERD](../../erd/role-erd.md), list/create/detail/update/delete role contracts in `../../solutioning/dashboard/accounts-roles/`, [Sprint 1 HLD](../../hld/sprint-1-hld.md) session/CSRF flow, and confirmed visual designs for role list/detail/forms.

## Estimation Notes

- **Basis:** Coarse estimate; separate list/detail and form/mutation surfaces, plus access handling.
- **Included:** Responsive UI, API integration, client validation, loading/empty/error states, permission-aware actions, and client verification.
- **Excluded:** Permission catalog CRUD, backend role/assignment checks, account-role assignment, audit log, and automated QA execution.
