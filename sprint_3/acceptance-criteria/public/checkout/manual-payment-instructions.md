# Manual Payment Instructions

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As a Customer, I want to choose an available approved manual payment method so that I can receive current payment instructions.

## Acceptance Criteria

### AC-PUB-006

> **Given :** Customer has a valid order and approved method is available
> **When :** Customer continues to payment
> **Then :**
>
> 1. Current instructions are shown; order or instructions do not mean payment is confirmed

### AC-PUB-006-002

> **Given :** Customer has completed a transfer for an eligible order
> **When :** Customer reports payment through the order or tracking flow
> **Then :**
>
> 1. The payment is marked for manual review and is not represented as settled until an authorized review confirms it.
> 2. An ineligible or invalid report does not change the payment outcome.

## Technical Flow

1. Customer: Customer continues to payment.
2. System: Current instructions are shown; order or instructions do not mean payment is confirmed.
3. Customer: Reports that payment has been made using the order/tracking flow.
4. System: Records the report for manual review, or returns a safe failure without marking the payment settled.

## Parameters

> **Weight per Parameter :** 15
> **New Technology :** TBD — no team assessment evidence available
> **Surrounding Involvement :** TBD — no confirmed count available
> **Technical Flow :** TBD — no assessed sequence count available
> **Acceptance Criteria :** TBD — team scoring rubric is not confirmed
> **Size :** TBD — no assessed scores or approved calculation available

## Subtask

### Frontend

| Task                                              | Mandays | Story Point |
| ------------------------------------------------- | ------: | ----------: |
| Show approved instructions or unavailable outcome |     TBD |         TBD |
| **Total**                                         | **TBD** |     **TBD** |

### Backend

| Task                                                                     | Mandays | Story Point |
| ------------------------------------------------------------------------ | ------: | ----------: |
| Provide configured instructions and keep order/payment statuses distinct |     TBD |         TBD |
| **Total**                                                                | **TBD** |     **TBD** |

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
- BRD traceability: BR-009 in [Sprint 3 BRD](../../../BRD.md).
- Team-approved sizing rubric, task estimates, and story point evidence were not available in supplied sources; assessments remain TBD. N/A marks an inapplicable discipline.
- This document describes BRD outcomes; solutioning coverage does not establish AC approval or implementation verification.
- Go route evidence: POST /api/orders/v1/payments/{paymentId}/report-paid and POST /api/orders/v1/tracking/{orderNumber}/payment record a customer payment report for manual review; they do not confirm settlement.
