# Customer Payment Report

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As a customer, I want to report that I have paid for an eligible order so that my payment can be reviewed.

## Acceptance Criteria

### AC-PAY-001

> **Given :** Customer has an eligible order and has completed payment using an approved manual method
> **When :** Customer reports payment through the order or tracking flow
> **Then :**
>
> 1. The payment report is associated with the eligible order and awaits manual review.
> 2. Reporting payment does not mean settlement is confirmed.

### AC-PAY-002

> **Given :** Customer reports payment for an ineligible order or with details that do not match
> **When :** Customer submits the report
> **Then :**
>
> 1. The report is rejected without confirming payment or changing protected order/payment state.

### AC-PAY-003

> **Given :** Customer repeats a payment report after a transient failure or repeated action
> **When :** The same report is submitted again
> **Then :**
>
> 1. The retry does not create duplicate payment-report effects under the approved retry policy.

## Technical Flow

1. Customer: Completes payment using an approved manual method.
2. Customer: Reports payment from the order or tracking view.
3. System: Validates that the report matches an eligible payment attempt.
4. System: Records the report for manual review or returns a safe failure.
5. System: Keeps settlement unconfirmed until an authorized review outcome.

## Parameters

> **Weight per Parameter :** 15
> **New Technology :** TBD — no team assessment evidence available
> **Surrounding Involvement :** TBD — no confirmed count available
> **Technical Flow :** TBD — no assessed sequence count available
> **Acceptance Criteria :** TBD — team scoring rubric is not confirmed
> **Size :** TBD — no assessed scores or approved calculation available

## Subtask

### Frontend

| Task                                                                | Mandays | Story Point |
| ------------------------------------------------------------------- | ------: | ----------: |
| Present payment-report action and pending-review or failure outcome |     TBD |         TBD |
| **Total**                                                           | **TBD** |     **TBD** |

### Backend

| Task                                                                       | Mandays | Story Point |
| -------------------------------------------------------------------------- | ------: | ----------: |
| Validate and record eligible payment reports without confirming settlement |     TBD |         TBD |
| **Total**                                                                  | **TBD** |     **TBD** |

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
- BRD traceability: BR-009 and BR-024 in [Sprint 3 BRD](../../../BRD.md); BR-010 is in the [separate BR-010 candidate BRD](../../../payment-evidence-BRD.md).
- Go route evidence: POST /api/orders/v1/payments/{paymentId}/report-paid and POST /api/orders/v1/tracking/{orderNumber}/payment are listed as customer payment-report routes.
- Manual review outcome policy remains subject to product/operations approval; payment-report submission alone is not settlement.
- Team-approved sizing rubric, task estimates, and story point evidence were not available; assessments remain TBD.
