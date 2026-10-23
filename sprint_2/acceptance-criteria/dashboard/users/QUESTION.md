# Admin User Management Questions

## Q-USER-001 — Admin invitation availability

**Question:** What is the approved admin-account creation path while Go's create/invite route reports identity-provider invitation unavailable?

**Source / evidence gap:** `POST /api/admin/users/v1/create` is registered but inventory marks identity-provider invitation unavailable; the AC cannot establish a successful invitation lifecycle or credential setup.

**Related solutioning / routes:** [Admin Account IFAs](../../../../sprint_1/solutioning/dashboard/admin-accounts/README.md), `/admin/admins`, `POST /api/admin/users/v1/create`.

**Impact:** Account provisioning acceptance and operational fallback remain undefined.

## Q-USER-002 — Members and users page distinction

**Question:** Should `/admin/members` and `/admin/users` represent separate concepts or the same member-management capability?

**Source / evidence gap:** Both routes map to one AC and no distinct behavior or role boundary is established in the supplied page evidence.

**Affected docs / routes:** Sprint 3 [member management IFAs](../../../../sprint_3/solutioning/dashboard/members/), `/admin/members`, `/admin/users`.

**Impact:** Navigation, permissions, and test coverage may duplicate or omit behavior until the distinction is confirmed.
