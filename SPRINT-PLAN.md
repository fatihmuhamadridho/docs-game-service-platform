# JokiHub Sprint Plan — Proposed Delivery Order

> **Status:** Planning proposal only. Sprint assignment orders delivery; it does not approve scope, policy, estimates, or release commitments. BR and AC identifiers remain unchanged.

## Sprint sequence

| Sprint                           | Scope and delivery order                                                                                                                                         | AC story documents | Requirement/status                                                                                                                      |
| -------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- | -----------------: | --------------------------------------------------------------------------------------------------------------------------------------- |
| [Sprint 1](./sprint_1/README.md) | Verify existing admin access → member management → roles/permissions → privileged admin accounts → customer account access → admin games → public game discovery |                  7 | Early identity/access and game foundation; BR-001/002, BR-006, BR-016–019, BR-023. Proposed, gated.                                     |
| [Sprint 2](./sprint_2/README.md) | Categories/services → settings/audit → lower-risk public content and recovery → mock/gated admin candidates                                                      |                 22 | Mid-complexity catalog/supporting capabilities; BR-003–005, BR-019/020/022 plus candidates. Uncommitted.                                |
| [Sprint 3](./sprint_3/README.md) | Checkout, payment reporting/evidence, order lookup/status → persisted admin order/transaction/activity reads → dashboard overview; retry/privacy controls        |                 12 | Payment lifecycle and data-dependent dashboard consumers; BR-007–015, BR-021, BR-024/025. Payment and dashboard decisions remain gated. |

Total: 41 AC story documents (7 + 22 + 12). The payment-report story contains multiple AC identifiers; counts above refer to documents, not individual AC rows. Prototype-derived and decision-gated stories remain unapproved.

## Why this order

- **Access prerequisite:** Verify current admin login and authorization before protected admin mutations. Existing code has shared login UI and distinct customer/admin auth behavior. AC-PUB-004 covers customer registration/sign-in/session behavior; admin authentication is currently a verification prerequisite, not a new admin-login story. If new admin login scope is required, add and approve it explicitly.
- **Sprint 1:** Put user management first, then role management, customer login, and games. Verify existing admin access before protected work. Member management uses that existing authorized access; define roles before creating or assigning privileged admin accounts. Customer login is independent of admin RBAC but follows the requested delivery priority. Game management precedes public game discovery data use.
- **Sprint 2:** Category and service records depend on games. Settings, audit recording, and lower-risk public/supporting content follow the core identity/catalog foundation. Mock UI and payment-review candidates remain gated.
- **Sprint 3:** Payment and order lifecycle require approved catalog/pricing, customer/guest identity rules, payment instructions, and order-state policy. Dashboard order, transaction, activity, and overview reads depend on persisted source records; overview also depends on approved metrics, currency basis, and freshness. Dashboard read/overview work therefore stays last without moving all admin management late.
- **Cross-sprint controls:** Authorization is an early prerequisite. Retry safety and privacy remain late shared stories but block any earlier affected operation until approved and implemented.
- **BR-010:** Payment-evidence upload remains separately gated; see [candidate BRD](./sprint_3/payment-evidence-BRD.md).

## Traceability

- [Sprint 1 BRD](./sprint_1/BRD.md) · [Sprint 2 BRD](./sprint_2/BRD.md) · [Sprint 3 BRD](./sprint_3/BRD.md)
- [Sprint 1–3 AC indexes](./sprint_1/acceptance-criteria/README.md) · [portfolio AC/route index](./sprint_1/acceptance-criteria/portfolio-index.md)
- [Portfolio Go/Next.js source inventory](./sprint_1/solutioning/source-inventory/README.md)

Questions, sizing TBDs, exclusions, and solutioning evidence gaps remain unresolved until their decision owners close them.
