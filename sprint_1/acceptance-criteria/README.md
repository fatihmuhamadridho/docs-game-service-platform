# Sprint 1 Acceptance Criteria — Access, Users, and Games

Seven story documents prioritized around user and access foundations. Placement is a proposed sequence, not a delivery commitment or scope approval.

## Prerequisites and gates

- Verify existing admin login/session and permission enforcement before protected admin work. The source shows a shared /login UI but separate customer/admin sessions; no separate admin-login AC exists. AC-PUB-004 covers customer account access only.
- [Shared Authorization and Input Validation](./shared/shared-authorization-validation.md) (AC-SHR-022; BR-023) is a cross-sprint foundation and prerequisite for protected operations. If target admin access requires new auth/RBAC implementation, block protected changes until policy and scope are approved.
- Role taxonomy, member lifecycle, admin invitation, customer identity/session rules, and game publication policy remain open.

## Story order

1. [Admin Member Management](./dashboard/users/admin-member-management.md) — AC-ADM-013; BR-016. Customer/member account lifecycle; no implicit admin privilege.
2. [Admin Role and Permission Management](./dashboard/accounts-roles/admin-role-management.md) — AC-ADM-015; BR-018. Define least-privilege roles.
3. [Admin Account Management](./dashboard/users/admin-account-management.md) — AC-ADM-014; BR-017. Depends on approved roles and identity/invitation flow.
4. [Customer Account Access](./public/auth/customer-account-access.md) — AC-PUB-004; BR-006. Customer registration/sign-in/sign-out/session renewal, separate from admin login verification.
5. [Admin Game Management](./dashboard/games/admin-game-management.md) — AC-ADM-016; BR-019. Requires approved game lifecycle/publish rules.
6. [Public Game Discovery](./public/games/public-game-discovery.md) — AC-PUB-001; BR-001/002. Depends on approved active catalog data.

## References and open decisions

- [Sprint 1 BRD](../BRD.md), [portfolio AC and route index](./portfolio-index.md), and [route/page coverage](./portfolio-route-feature-coverage.md).
- Customer identity and recovery questions remain with [Sprint 2 auth questions](../../sprint_2/acceptance-criteria/public/auth/QUESTION.md); admin identity and role questions remain with [Sprint 2 admin questions](../../sprint_2/acceptance-criteria/dashboard/users/QUESTION.md).
- Solutioning route presence does not establish approval or implementation completeness. Estimate, sizing, dates, IAM choices, field policy, and game data remain TBD.
