# Dashboard — Open Contract Decisions

Current Go behavior is documented in [the dashboard source IFA](../../../sprint_3/solutioning/backend-go/dashboard-source-ifa.md). The questions below identify target behavior that source code cannot approve.

## Q1 — When is admin invitation provisioning enabled?

- **Question:** Which configured identity/email provider and rollout condition should enable account invitation for `POST /api/admin/admin-users/v1/create`?
- **Evidence gap:** Handler requires Super Admin and `settings-manage`; controller validates unique identity then returns HTTP 503 `USRT02001` because identity-provider invitation is unavailable. No provider configuration/availability evidence in Go.
- **Affected routes/docs:** `POST /api/admin/admin-users/v1/create`; [create admin user IFA](../dashboard/users/create-admin-user.md), [admin account IFA](../backend-go/admin-account-ifa.md), [source IFA](../../../sprint_3/solutioning/backend-go/dashboard-source-ifa.md).
- **Decision owner:** Identity/platform owner.
- **Impact:** Account creation cannot succeed until provisioning dependency and failure/retry behavior are selected.

## Q2 — Are current Go permission strings and role restrictions the target policy?

- **Question:** Approve the implemented permission checks (`catalog-manage`, `users-manage`, `settings-manage`, `orders-manage`, `finance-read`, `dashboard-read`), with Super Admin bypass and Super Admin-only role assignment?
- **Evidence gap:** Checks are explicit in handlers/controllers; no signed-off permission matrix or admin-role AC was located as source for the target policy.
- **Affected routes/docs:** All 40 admin routes; [role management IFA](../dashboard/accounts-roles/update-role.md), [admin account IFA](../backend-go/admin-account-ifa.md), [dashboard source IFA](../../../sprint_3/solutioning/backend-go/dashboard-source-ifa.md).
- **Decision owner:** Security/product owner.
- **Impact:** Authorization matrix, UI affordances, and regression/security testing.

## Q3 — Does member create remain unavailable or require invitation implementation?

- **Question:** Is member invitation in scope for this release, and which identity/email provider is its dependency?
- **Evidence gap:** `POST /api/admin/users/v1/create` validates input and uniqueness but always returns HTTP 503 `USRT02001`; source has no create persistence or email send path.
- **Affected routes/docs:** Member create IFA and [dashboard source IFA](../../../sprint_3/solutioning/backend-go/dashboard-source-ifa.md).
- **Decision owner:** Product/platform owner.
- **Impact:** If in scope, endpoint requires implementation and release criteria; otherwise UI must not imply a successful invite.

## Q4 — What operational targets apply to admin reads and writes?

- **Question:** What availability, concurrency, resource, and latency targets should be used for the 40 dashboard routes, and which routes require a separate target tier?
- **Evidence gap:** Current Go handlers/controllers and Mongo repositories define synchronous behavior, but no approved SLA/SLO, production workload profile, measured latency, or NFT result was supplied. Source code cannot establish capacity targets.
- **Affected routes/docs:** All 40 dashboard routes; [dashboard source IFA](../../../sprint_3/solutioning/backend-go/dashboard-source-ifa.md), [route inventory](../source-inventory/backend-go/route-service-inventory.md).
- **Decision owner:** Platform/SRE owner.
- **Impact:** NFT acceptance thresholds, capacity planning, and release readiness.
