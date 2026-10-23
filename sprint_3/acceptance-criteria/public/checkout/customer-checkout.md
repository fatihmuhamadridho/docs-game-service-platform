# Fixed-Price Order Checkout

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As a Customer, I want to submit an order for an available fixed-price service with required details so that I can place an order using an authoritative amount and preserve purchase-time details.

## Acceptance Criteria

### AC-PUB-005

> **Given :** Customer selects available fixed-price service and supplies required contact, game-account, and quantity details
> **When :** Customer submits the order
> **Then :**
>
> 1. Valid order uses server-authoritative total and preserves purchase-time snapshots; unavailable service, invalid quantity, or missing details prevent creation

## Technical Flow

1. Customer: Customer submits the order.
2. System: Valid order uses server-authoritative total and preserves purchase-time snapshots; unavailable service, invalid quantity, or missing details prevent creation.

## Parameters

> **Weight per Parameter :** 15
> **New Technology :** TBD — no team assessment evidence available
> **Surrounding Involvement :** TBD — no confirmed count available
> **Technical Flow :** TBD — no assessed sequence count available
> **Acceptance Criteria :** TBD — team scoring rubric is not confirmed
> **Size :** TBD — no assessed scores or approved calculation available

## Subtask

### Frontend

| Task                                   | Mandays | Story Point |
| -------------------------------------- | ------: | ----------: |
| Collect order details and show outcome |     TBD |         TBD |
| **Total**                              | **TBD** |     **TBD** |

### Backend

| Task                                                                                            | Mandays | Story Point |
| ----------------------------------------------------------------------------------------------- | ------: | ----------: |
| Validate availability/quantity, calculate authoritative total, preserve snapshots, create order |     TBD |         TBD |
| **Total**                                                                                       | **TBD** |     **TBD** |

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

- Open estimation question: [Cross-feature Questions](./QUESTION.md) â€” sizing and effort remain TBD pending an approved rubric and estimates.
- Open feature decisions: [Feature Questions](QUESTION.md).
- BRD traceability: BR-007, BR-008 in [Sprint 3 BRD](../../../BRD.md).
- Team-approved sizing rubric, task estimates, and story point evidence were not available in supplied sources; assessments remain TBD. N/A marks an inapplicable discipline.
- This document describes BRD outcomes; solutioning coverage does not establish AC approval or implementation verification.
