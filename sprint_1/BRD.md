# JokiHub — Sprint 1 Business Requirements: Admin Access and Games

> **Status:** Proposed priority sequence; not a delivery commitment or scope approval.  
> **Sprint:** 1  
> **Source:** Requirement IDs remain portfolio identifiers. See [portfolio sequence](../SPRINT-PLAN.md). Acceptance criteria are not being maintained for this planning revision.

## 1. Project Overview

Sprint 1 focuses on administrator access, roles, administrator accounts, and games.

## 2. Objectives

- Establish approved admin access and role foundations before protected administration.
- Make approved games manageable and discoverable.
- Preserve administrator identity and permission boundaries.

Estimates, owners, policy, targets, and dates remain **TBD**.

## 3. Scope and sequence

### Prerequisite

Verify current admin login/session and authorization behavior. BR-023 requires protected admin actions to reject invalid input or missing permission without changing or disclosing protected data; this criterion is included in each affected admin feature AC. If a new admin identity or RBAC implementation is required, protected work is gated pending explicit scope and policy approval.

### Proposed delivery order

1. Verify current admin login/session and authorization behavior.
2. Roles and permissions — BR-018. Seed awal hanya memiliki satu role, Super Admin, dengan akses ke seluruh fungsi admin. Role ini dilindungi dari update/delete; role khusus tambahan hanya dibuat secara eksplisit setelah bootstrap.
3. Read-only administrator account directory — BR-017; see [admin account solutioning](./solutioning/dashboard/admin-accounts/). Super Admin is provisioned by a controlled backend/database/script bootstrap; dashboard exposes list and detail only.
4. Admin game management — game slice of BR-019.
5. Public game discovery — BR-001/002, after active-game data and publication rules are approved.

### Out of Scope / Later

Category/service management slices of BR-019 and public category/service discovery (BR-003/004/005) are assigned to Sprint 2. Checkout, payment, order status, evidence, admin operational mutations, and dashboard/read views are assigned later or remain separately gated. No additional admin-login behavior is implied.

## 4. Business Requirements

| ID     | Requirement                                                                                                                                                                                                                                                                                                           |
| ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| BR-001 | As a visitor, I need to view active public games so that I can find a game with available services.                                                                                                                                                                                                                   |
| BR-002 | As a visitor, I need to search, filter, sort, and paginate the game catalog so that I can find relevant games efficiently.                                                                                                                                                                                            |
| BR-017 | As an authorized administrator, I need to view administrator accounts and their assigned roles so that privileged access is transparent and auditable. Super Admin provisioning is handled through a controlled backend/database/script bootstrap; dashboard account operations are read-only.                        |
| BR-018 | As an authorized administrator, I need role and permission controls so that access is enforced consistently. Sprint 1 starts with only one active Super Admin role with access to all admin functions; this system role cannot be updated or deleted. Additional roles are created explicitly, not seeded by default. |
| BR-019 | As an authorized administrator, I need to manage games, service categories, and services so that the public catalog reflects approved offerings. This sprint covers game management; category and service slices continue in Sprint 2.                                                                                |
| BR-023 | As the business, I need public and administrative operations to validate inputs and enforce caller authorization so invalid or unauthorized actions do not change protected data.                                                                                                                                     |

## 5. Assumptions and Dependencies

- Current route/UI evidence does not approve target identity policy or prove production readiness.
- Admin login verification is a prerequisite only. If current baseline fails target policy, scope an admin identity/login implementation explicitly before protected admin delivery.
- Super Admin bootstrap and protection are defined in Sprint 1; permission-to-role mapping for any additional role requires Security/Product sign-off. Admin bootstrap is an operational backend/database/script process, not an invitation flow. Game lifecycle/publication rules remain unresolved.
- Category/service data and pricing rules are prerequisites for public service discovery in Sprint 2.
- No estimates, sprint duration, owners, or dates are approved; all remain TBD.

## 6. Open Questions

- Does the existing admin login/session and authorization baseline meet target policy, or is new admin-auth work required? — Owner not identified; before protected admin work.
- Which permission-to-role matrix and delegation restrictions apply to additional roles? — Security/Product owner; before those roles are introduced.
- What game activation, publication, filtering, and production catalog rules are approved? — Owner not identified; before catalog release.

## 7. SLA Baseline

SLA baseline berlaku untuk operasi API dashboard dan katalog publik Sprint 1. Angka beban serta kapasitas berikut adalah asumsi target awal, bukan hasil pengukuran; validasi melalui NFT sebelum rilis.

| Metrik                      | Target                                                                                                             |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| Availability bulanan        | 99,9%                                                                                                              |
| Beban NFT                   | 50 VUser dan 20 request/detik dengan campuran route representatif                                                  |
| Latency baca                | p95 ≤ 500 ms; p99 ≤ 2 detik                                                                                        |
| Latency tulis               | p95 ≤ 1 detik; p99 ≤ 2 detik                                                                                       |
| Kapasitas awal per instance | 2 vCPU, 4 GiB RAM; CPU ≤ 70% dan memori ≤ 75% pada beban target                                                    |
| Error server                | ≤ 0,1% request valid per bulan; timeout dan HTTP 5xx dihitung, 4xx validasi/otorisasi yang diharapkan dikecualikan |
