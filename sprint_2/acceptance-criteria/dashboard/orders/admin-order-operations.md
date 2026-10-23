# Admin Order Payment Review and Completion

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As an authorized administrator, I want to review reported payments and mark eligible orders complete so that I can advance approved operational work.

## Acceptance Criteria

### AC-ADM-021-001

> **Given :** Administrator has orders-manage access and opens an order with an eligible payment/progress state
> **When :** Administrator approves/rejects payment with optional note or marks order complete
> **Then :**
>
> 1. The chosen action is recorded against the order/payment and the refreshed order state reflects the result; rejected/failed actions do not display success.

### AC-ADM-021-002

> **Given :** Administrator has orders-manage permission and an order has submitted private payment evidence
> **When :** Administrator requests to inspect the evidence
> **Then :**
>
> 1. Only an authorized administrator can view the private image; public users cannot access it.
> 2. This image capability remains unavailable while the Sprint 2 feature gate is disabled.

## Technical Flow

1. Administrator opens order detail.
2. System checks order-management access and available actions.
3. Administrator records review decision/note or completion action.
4. System applies a valid transition or reports a failure.
5. Order list/detail refreshes with resulting state.
6. When payment-proof viewing is enabled, authorized administrator can inspect a private evidence image for the order.

## Parameters

> **Weight per Parameter :** 15
> **New Technology :** TBD — no team assessment evidence available
> **Surrounding Involvement :** TBD — no confirmed count available
> **Technical Flow :** TBD — no assessed sequence count available
> **Acceptance Criteria :** TBD — team scoring rubric is not confirmed
> **Size :** TBD — no assessed scores or approved calculation available

## Subtask

### Frontend

| Task                                                                                                  | Mandays | Story Point |
| ----------------------------------------------------------------------------------------------------- | ------: | ----------: |
| Present proof-review and completion controls, confirmation/error outcomes, and refreshed order state. |     TBD |         TBD |
| **Total**                                                                                             | **TBD** |     **TBD** |

### Backend

| Task                                                                                                      | Mandays | Story Point |
| --------------------------------------------------------------------------------------------------------- | ------: | ----------: |
| Support payment-review and order-completion decisions with actor attribution and valid state transitions. |     TBD |         TBD |
| **Total**                                                                                                 | **TBD** |     **TBD** |

### Summary

> **FE :** TBD
> **BE :** TBD
> **Size Task :** TBD

## Sizing Reference

| SIZE | SCORE RANGE | ESTIMATE COMPLETION | STORY POINT |
| ---- | ----------- | ------------------- | ----------- |
| S    | ≦ 100       | < 2 days            | 1-3         |
| M    | 101-200     | 2 – 3 days          | 4-6         |
| L    | 201-250     | > 3 – 5 days        | 7-10        |
| XL   | > 250       | > 5 days            | > 10        |

## Notes

- Open estimation question: [Cross-feature Questions](../../../../sprint_1/acceptance-criteria/QUESTION.md) â€” sizing and effort remain TBD pending an approved rubric and estimates.
- Open feature decisions: [Feature Questions](QUESTION.md).
- Source traceability: BR-014 related to order inspection; payment-proof decision and fulfillment execution explicitly out of scope unless separately approved. Product scope status remains unresolved where noted below.
- Observed source: AdminOrders page calls POST /api/admin/orders/v1/payment-review and POST /api/admin/orders/v1/complete/{orderId}; Go inventory lists these routes and GET /api/admin/orders/v1/proofs/{proofId}/image (FEATURE_SPRINT_2). BRD excludes admin payment-proof approval/rejection absent separate approval and excludes fulfillment execution. Product/operations must decide Sprint 1 inclusion and define allowed transitions, review evidence, and completion meaning. This draft records visible shipped behavior only; it is not approved scope.
- This document records observable UI/code behavior and open decisions; it does not claim implementation completeness or production readiness.
