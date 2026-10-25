# Sprint 2 — Admin Permission Catalog Extension

> **Status:** Draft assessment and decision proposal. This document scopes the Sprint 2 access-control extension without changing Sprint 1 solutioning or approving a permission model.

## Purpose

BR-019 and BR-020 require authorized administrators to manage catalog records and platform settings. Sprint 1 already defines the role and system-managed permission-catalog foundation. Sprint 2 must decide how its new administrative capabilities are represented in that catalog and assigned to custom roles.

## Evidence in the repository

| Evidence                                                                                                               | Current statement                                                                                                                                                                                                                                        | Assessment                                                                                                                                                       |
| ---------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Sprint 1 permission model](../../../../sprint-1/solutioning/dashboard/accounts-roles/permission-model.md)             | Category/service permissions are outside Sprint 1 and continue in Sprint 2. Permission definitions are system-managed, require solutioning and owner approval, and unknown/inactive keys fail closed. `SUPER_ADMIN` has access to later admin functions. | A Sprint 2 catalog delta needs its own solutioning and approval; no Sprint 1 file needs to change.                                                               |
| [Admin route inventory](../../../../sprint-3/solutioning/backend-go/dashboard-source-ifa.md#route-to-handler-coverage) | Game, category, and service routes currently use `catalog-manage`; general settings uses `settings-manage`. The role response exposes a permission catalog and role permission representation.                                                           | These are current documented source facts. Route permission keys and role UI permission categories must not be assumed to be the same identifier.                |
| Ten Sprint 2 category/service IFAs                                                                                     | Target proposals use `service-category-read/create/update/delete` and `service-read/create/update/delete`. The settings IFA uses `settings-manage`.                                                                                                      | Granular catalog/service keys are proposals and have not been reconciled with the source `catalog-manage` route policy or the system-managed permission catalog. |

## Sprint 2 access requirements

1. Every Sprint 2 admin route must resolve to an approved active permission key and an explicitly authorized role grant.
2. Custom-role grant validation remains fail-closed: a key that is absent or inactive cannot authorize a request or be granted through role management.
3. `SUPER_ADMIN` retains the access to later admin functions defined by the existing system-role policy.
4. Permission definitions remain system-managed; role CRUD assigns catalog entries and does not create or edit permission definitions.
5. The route-to-permission mapping, permission-catalog response, and role grant behavior must agree. If keys or grant semantics change, compatibility and migration of existing custom-role grants must be decided.

## Route and permission reconciliation

| Sprint 2 capability           | Current route inventory | IFA target proposal                                                             | Status                                                                                                                     |
| ----------------------------- | ----------------------- | ------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------- |
| Category list/detail          | `catalog-manage`        | `service-category-read`                                                         | Decision D-18                                                                                                              |
| Category create/update/delete | `catalog-manage`        | `service-category-create`, `service-category-update`, `service-category-delete` | Decision D-18                                                                                                              |
| Service list/detail           | `catalog-manage`        | `service-read`                                                                  | Decision D-18                                                                                                              |
| Service create/update/delete  | `catalog-manage`        | `service-create`, `service-update`, `service-delete`                            | Decision D-18                                                                                                              |
| General settings read/update  | `settings-manage`       | `settings-manage`                                                               | Route and IFA key align in the current inventory; confirm catalog/grant representation with D-18 and settings policy D-05. |

## Decision options — not approved

- **Reuse the source route policy:** retain `catalog-manage` for game/category/service routes and `settings-manage` for settings; revise Sprint 2 target permission wording after API/security approval.
- **Extend the catalog with granular capabilities:** add the IFA-proposed category/service keys as active system-managed entries, map each route to one, and define how existing custom-role grants and role-management projections remain compatible.

The choice affects least-privilege administration, role assignment, route middleware, and existing grants. Do not treat either option as approved or silently map the role UI categories (`catalog`, `settings`) to route authorization keys.

## Decision gate

**D-18 — Approve the Sprint 2 role permission-catalog extension.** API/security and product/access owners must choose the route permission model; approve the stable key set, route mapping, role-catalog/UI representation, custom-role grant lifecycle, `SUPER_ADMIN` behavior, and any migration or compatibility rule. Evidence to close: an approved route-to-permission matrix, permission-catalog delta or explicit reuse decision, and verified custom-role grant behavior for each Sprint 2 admin capability. Coordinate HTTP/authentication compatibility with D-11; do not edit Sprint 1 artifacts.

## References

- [Sprint 2 BRD](../../../BRD.md) — BR-019 and BR-020.
- [Create service category IFA](../service-categories/create-service-category.md) and the other category/service IFAs — proposed route permissions.
- [General settings IFA](../settings/general-settings.md) — `settings-manage` target.
- [Sprint 2 assessment and decision register](../../README.md#keputusan-yang-menghalangi-finalisasi) — D-05, D-11, and D-18.
- [Sprint 1 role ERD](../../../../sprint-1/erd/role-erd.md) — existing permission and role-grant entities; assess physical changes only if the approved model requires them.
