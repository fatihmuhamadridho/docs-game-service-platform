# JokiHub Documentation

Project documentation uses three proposed delivery groups, ordered from foundational work to the most data- and decision-dependent work. Sprint placement orders work; it does not approve requirements, policies, estimates, or release scope.

## Delivery grouping

- [Sprint plan and dependency gates](./SPRINT-PLAN.md)
- [Sprint 1 BRD — Access, identity, and games](./sprint_1/BRD.md)
- [Sprint 2 BRD — Catalog and supporting features](./sprint_2/BRD.md)
- [Sprint 3 BRD — Payment lifecycle and dashboard reads](./sprint_3/BRD.md) · [BR-010 payment evidence candidate](./sprint_3/payment-evidence-BRD.md)
- [Cross-sprint AC index and route/feature coverage](./sprint_1/acceptance-criteria/portfolio-index.md)
- [Sprint 1 — Access, identity, and games](./sprint_1/README.md)
- [Sprint 2 — Catalog and supporting features](./sprint_2/README.md)
- [Sprint 3 — Payment lifecycle and dashboard reads](./sprint_3/README.md)
- [Portfolio Go/Next.js source evidence](./sprint_1/solutioning/source-inventory/README.md)
- [Shared glossary](./sprint_1/GLOSSARY.md)
- [Sprint 3 artwork reference](./sprint_3/reference/game-artwork-sources.md)

Acceptance and solutioning documents remain drafts unless owners approve them. Existing UI, routes, and source contracts describe current evidence, not product approval or complete implementation.

## Sprint grouping and status

| Sprint                           | Scope group                                                                                                           | Status and BRD traceability                                                                                                 |
| -------------------------------- | --------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------- |
| [Sprint 1](./sprint_1/README.md) | Verify admin access → member management → roles → privileged admin accounts → customer login → games/discovery        | 7 stories; early identity/access and catalog foundation. Proposed and gated; BR-001/002, BR-006, BR-016–019, BR-023.        |
| [Sprint 2](./sprint_2/README.md) | Service categories/services, settings/audit, lower-risk public content/recovery, prototype and operational candidates | 22 stories; mid-complexity work. Uncommitted; BR-003–005, BR-019/020/022 plus gated candidates.                             |
| [Sprint 3](./sprint_3/README.md) | Customer checkout/payment/order lifecycle, persisted admin read views, dashboard overview, retry/privacy controls     | 12 stories; payment and dashboard are last due to lifecycle, data, and policy dependencies. BR-007–015, BR-021, BR-024/025. |

Sprint 1 verifies existing admin login/access before protected work. Current source has shared login UI but distinct customer/admin identities and sessions; AC-PUB-004 covers customer account access, while admin login remains a verification prerequisite. No new admin-login story is inferred. Role definitions precede privileged admin account creation; games precede categories and services. Shared authorization is an early prerequisite; retry and privacy controls remain Sprint 3 stories and block affected earlier operations until resolved. Sprint 3 order/transaction reads require persisted order/payment records, activity history requires audit events, and overview follows all read models plus approved metric definitions.

Automated settlement/reconciliation, refund and payout execution, fulfillment execution, and admin payment approval/rejection remain excluded unless separately approved. Mock admin features remain unapproved candidates. Questions, TBD estimates, and source gaps remain unresolved.
