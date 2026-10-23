# JokiHub — Sprint 1 Business Requirements: Access, Users, and Games

> **Status:** Proposed priority sequence; not a delivery commitment or scope approval.  
> **Sprint:** 1  
> **Source:** Requirement IDs remain portfolio identifiers. See [portfolio sequence](../SPRINT-PLAN.md) and [AC crosswalk](./acceptance-criteria/portfolio-index.md).

## 1. Project Overview

Sprint 1 prioritizes member/user management, roles, customer login, and games. Existing admin login/session behavior must be verified as a prerequisite. The source describes a shared /login UI with distinct customer and admin sessions. AC-PUB-004 is customer account access; no admin-login story is defined in the current 41 AC documents.

## 2. Objectives

- Establish approved access, member, and role foundations before later protected administration.
- Make approved games manageable and discoverable.
- Preserve separate customer and administrator actors, identity decisions, and permission boundaries.

Estimates, owners, policy, targets, and dates remain **TBD**.

## 3. Scope and sequence

### Prerequisite

Verify current admin login/session and authorization behavior. [AC-SHR-022](./acceptance-criteria/shared/shared-authorization-validation.md) is the shared authorization/input-validation foundation. If a new admin identity or RBAC implementation is required, protected work is gated pending explicit scope and policy approval.

### Proposed delivery order

1. [Member/customer user management](./acceptance-criteria/dashboard/users/admin-member-management.md) — BR-016 / AC-ADM-013.
2. [Roles and permissions](./acceptance-criteria/dashboard/accounts-roles/admin-role-management.md) — BR-018 / AC-ADM-015.
3. [Privileged admin accounts](./acceptance-criteria/dashboard/users/admin-account-management.md) — BR-017 / AC-ADM-014; depends on approved role taxonomy and identity/invitation flow.
4. [Customer account access](./acceptance-criteria/public/auth/customer-account-access.md) — BR-006 / AC-PUB-004; distinct from admin login verification.
5. [Admin game management](./acceptance-criteria/dashboard/games/admin-game-management.md) — game slice of BR-019 / AC-ADM-016.
6. [Public game discovery](./acceptance-criteria/public/games/public-game-discovery.md) — BR-001/002 / AC-PUB-001, after active-game data and publication rules are approved.

### Out of Scope / Later

Category/service management slices of BR-019 and public category/service discovery (BR-003/004/005) are assigned to Sprint 2. Checkout, payment, order status, evidence, admin operational mutations, and dashboard/read views are assigned later or remain separately gated. No additional admin-login behavior is implied.

## 4. Business Requirements

| ID     | Requirement                                                                                                                                                                                                                            |
| ------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| BR-001 | As a visitor, I need to view active public games so that I can find a game with available services.                                                                                                                                    |
| BR-002 | As a visitor, I need to search, filter, sort, and paginate the game catalog so that I can find relevant games efficiently.                                                                                                             |
| BR-006 | As a customer, I need to register and authenticate through the approved identity flow so that I can use account-based features. Credentials and OTPs must not be collected as general order fields.                                    |
| BR-016 | As an authorized administrator, I need to manage member accounts separately from admin accounts so that customer lifecycle actions do not grant admin privileges implicitly.                                                           |
| BR-017 | As an authorized administrator, I need to create and manage admin accounts with explicitly assigned roles so that privileged access is controlled and auditable. Credential setup uses the approved identity/invitation flow.          |
| BR-018 | As an authorized administrator, I need to manage roles and permissions so that admin access follows least privilege; protected roles and unauthorized delegation cannot be changed.                                                    |
| BR-019 | As an authorized administrator, I need to manage games, service categories, and services so that the public catalog reflects approved offerings. This sprint covers game management; category and service slices continue in Sprint 2. |
| BR-023 | As the business, I need public and administrative operations to validate inputs and enforce caller authorization so invalid or unauthorized actions do not change protected data.                                                      |

## 5. Assumptions and Dependencies

- Current route/UI evidence does not approve target identity policy or prove production readiness.
- Admin login verification is a prerequisite only. If current baseline fails target policy, scope an admin identity/login implementation explicitly before protected admin delivery.
- Role taxonomy/delegation, member lifecycle fields/actions, admin invitation, customer identity/session policy, and game lifecycle/publication rules are unresolved.
- Category/service data and pricing rules are prerequisites for public service discovery in Sprint 2.
- No estimates, sprint duration, owners, or dates are approved; all remain TBD.

## 6. Open Questions

- Does the existing admin login/session and authorization baseline meet target policy, or is new admin-auth work required? — Owner not identified; before protected admin work.
- Which identity provider, permission taxonomy, role delegation, invitation, and credential setup rules apply to admin accounts? — Owner not identified; before role/account delivery.
- Which customer registration/login/session rules apply, and are customer and admin identities isolated as source evidence indicates? — Product/security owner not identified; before customer account release.
- Which member fields and lifecycle actions are permitted? — Owner not identified; before member-management approval.
- What game activation, publication, filtering, and production catalog rules are approved? — Owner not identified; before catalog release.
