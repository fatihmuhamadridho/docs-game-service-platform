# Sprint 1 - Dashboard Game Management - Frontend Task Detail

> **Document Type:** Frontend Task Detail  
> **Status:** Draft  
> **Story / AC:** BR-005, BR-006, BR-008  
> **Design:** [dashboard.png](../../design/dashboard.png); game administration surfaces TBD

## Task Overview

| PIC                | Total Mandays |
| ------------------ | ------------: |
| Frontend Developer |          7.50 |
| **Total**          |      **7.50** |

## Task Breakdown

### Game List and Detail

| ID          | Task / Deliverable                                                                                                                                     | Related AC             | Mandays | PIC                |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------- | ------: | ------------------ |
| FE-GAME-001 | Build dashboard game list with name, slug, platform when present, active/popular flags, sort order, pagination, and create/detail/edit/delete actions. | BR-005                 |    1.00 | Frontend Developer |
| FE-GAME-002 | Integrate `GET /api/admin/games/v1/list` with page default 1, limit default 20/max 100; implement loading, empty, fetch-error, and 429 retry states.   | BR-005, BR-006         |    0.50 | Frontend Developer |
| FE-GAME-003 | Build game detail view and integrate `GET /api/admin/games/v1/detail/{gameId}`; show required display fields and safe 400/401/403/404/500 handling.    | BR-005, BR-006, BR-008 |    0.75 | Frontend Developer |

### Create and Update Game

| ID          | Task / Deliverable                                                                                                                                                                                                                                                                     | Related AC     | Mandays | PIC                |
| ----------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ------: | ------------------ |
| FE-GAME-004 | Build create-game form for name, slug, optional description (max 500), required absolute HTTPS image URL, optional platform (`MOBILE`, `PC`, `CONSOLE`), and sort order; client-validate required/format/length and show field errors.                                                 | BR-005, BR-006 |    1.00 | Frontend Developer |
| FE-GAME-005 | Integrate `POST /api/admin/games/v1/create`; handle duplicate name/slug as conflict, generic validation/service errors, preserve user input on failure, and update list after confirmed success.                                                                                       | BR-005, BR-006 |    0.50 | Frontend Developer |
| FE-GAME-006 | Build edit form and integrate `PATCH /api/admin/games/v1/update/{gameId}` for editable game details; allow blank optional description/platform where contract permits, preserve data on failure, and avoid changing `updatedAt` assumptions in client state when values are unchanged. | BR-005, BR-006 |    1.00 | Frontend Developer |

### Publication, Delete, and Permissions

| ID          | Task / Deliverable                                                                                                                                                                                                       | Related AC     | Mandays | PIC                |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------- | ------: | ------------------ |
| FE-GAME-007 | Expose `isActive` and `isPopular` editing only to Super Admin; enforce UI dependency that popular requires active, and handle API rejection without showing false success.                                               | BR-005, BR-006 |    0.75 | Frontend Developer |
| FE-GAME-008 | Add soft-delete confirmation and integrate `DELETE /api/admin/games/v1/delete/{gameId}` without a request body; remove deleted records from current results only on confirmed success and report missing/error outcomes. | BR-005, BR-006 |    0.50 | Frontend Developer |
| FE-GAME-009 | Apply `game.read/create/update/delete` access to routes and controls; distinguish permission denial, expired session, form validation, conflict, and service failure states.                                             | BR-005, BR-006 |    1.50 | Frontend Developer |

## Dependencies

- **Dependencies:** Game list/detail/create/update/delete contracts in `../../solutioning/dashboard/games/`, [Game Catalog ERD](../../erd/game-catalog-erd.md), [permission catalog](../../solutioning/dashboard/accounts-roles/permission-model.md), [Sprint 1 HLD](../../hld/sprint-1-hld.md) session flow, and approved dashboard game designs.

## Estimation Notes

- **Basis:** Coarse estimate split by list/detail, create/update, delete/publication fields, and authorization states.
- **Included:** Responsive CRUD views, form validation, API integration, status feedback, and client verification.
- **Excluded:** Backend mutation rules, image hosting/upload, categories/services, ratings/reviews, and automated QA execution.
