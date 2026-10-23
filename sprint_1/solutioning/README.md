# Sprint 1 Solutioning — Identity, Access, and Games

Early delivery group: customer account access, admin user/role management, and game management. Admin authentication/RBAC verification is a technical prerequisite for protected admin features; it does not add an admin-login product story. Customer account-access AC remains customer-only.

## Identity and access

- [Customer authentication and account handling](auth-user-handling.md)
- [Customer authentication APIs](public/auth)
- [Admin accounts and role assignment source analysis](backend-go/admin-account-ifa.md)
- [Admin user-management contracts](dashboard/users)
- [Roles and permissions](dashboard/accounts-roles)
- [Admin management open decisions](dashboard-management/QUESTION.md)
- [Customer account-access AC](../acceptance-criteria/public/auth/customer-account-access.md) · [Admin member-management AC](../acceptance-criteria/dashboard/users/admin-member-management.md) · [Admin role-management AC](../acceptance-criteria/dashboard/accounts-roles/admin-role-management.md)

Proposed implementation sequence: (1) verify existing admin authentication/RBAC access, (2) member management, (3) role/permission setup, (4) privileged admin account creation and role assignment, (5) customer account access, (6) admin game management, (7) public game discovery. This is dependency ordering, not a release commitment. Admin account creation depends on established roles; admin verification is a prerequisite, not new login scope. [SHR-022 shared authorization/validation AC](../acceptance-criteria/shared/shared-authorization-validation.md) supplies the shared prerequisite.

The Go source has separate `UserAccount` and `AdminAccount` identities and separate customer/admin session cookies. It exposes `POST /api/auth/v1/login` and `POST /api/admin/auth/v1/login`; the shared customer login handler also currently checks admin credentials. This is current implementation evidence. Admin auth is a readiness/security verification prerequisite only; no new admin login product scope is introduced by this solutioning grouping.

## Games

- [Admin game catalog contracts](dashboard/games)
- [Public game discovery contracts](public/games)
- [Game catalog ERD](erd/game-catalog-erd.md)
- [Public game-discovery AC](../acceptance-criteria/public/games/public-game-discovery.md) · [Admin game-management AC](../acceptance-criteria/dashboard/games/admin-game-management.md)

## Shared source and technical prerequisites

- [Portfolio Go and Next.js source inventory](source-inventory/README.md) covers routes across all delivery groups.
- [Recovery and session implementation analysis](backend-go/additional-auth-ifa.md); recovery/provider decisions remain explicit.
- [Customer/member ERD](erd/member-erd.md) · [Admin role ERD](erd/role-erd.md) · [User-management ERD](erd/user-management-erd.md)
- Verify database migrations, JWT configuration, super-admin bootstrap, rate-limit indexes, permission enforcement, and session separation before enabling protected admin management.
- [Shared authorization and validation AC](../acceptance-criteria/shared/shared-authorization-validation.md) remains product/QA scope; this solutioning index records technical dependencies only.
