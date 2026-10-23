# Sprint 1 — Access, Users, and Core Games

> **Status:** Proposed foundational sequence, not a delivery commitment. Scope and policy remain subject to approval.

## Ordered scope

**Prerequisite gate:** verify that existing admin login, identity separation, and authorization protect privileged routes. Current source has a shared login UI and distinct customer/admin auth behavior. AC-PUB-004 covers customer registration and login; admin login is verification only. No new admin-login build is implied.

1. **Member management** — AC-ADM-013 / BR-016.
2. **Role and permission management** — AC-ADM-015 / BR-018.
3. **Privileged admin account management** — AC-ADM-014 / BR-017; depends on defined roles and approved identity/invitation rules.
4. **Customer account access** — AC-PUB-004 / BR-006; separate customer identity/session from admin access.
5. **Admin game management** — AC-ADM-016 / BR-019.
6. **Public game discovery** — AC-PUB-001 / BR-001/002; depends on approved active game data.

The [shared authorization and input-validation story](./acceptance-criteria/shared/shared-authorization-validation.md) is the cross-cutting prerequisite for protected operations. The six numbered items plus that shared story make seven AC story documents.

## Dependencies and open decisions

- Existing admin login/access verification gates member and role administration. If current admin sign-in or permissions fail policy, new admin-auth scope requires explicit approval; it is not covered by AC-PUB-004.
- Approve member lifecycle actions, role taxonomy/delegation, identity provider, and admin invitation/credential flow. Admin account creation follows role definition.
- Approve game publication, active/inactive rules, and public fields before game management or discovery is treated as release-ready.
- Public game discovery can expose only approved active catalog records.
- Estimates, performance targets, and dates remain TBD.

## Documents

- [Sprint 1 BRD](./BRD.md)
- [Sprint 1 AC index](./acceptance-criteria/README.md)
- [Cross-sprint AC and route index](./acceptance-criteria/portfolio-index.md)
- [Sprint 1 solutioning](./solutioning/README.md)
- [Portfolio source evidence](./solutioning/source-inventory/README.md)
- [Portfolio glossary](./GLOSSARY.md)
