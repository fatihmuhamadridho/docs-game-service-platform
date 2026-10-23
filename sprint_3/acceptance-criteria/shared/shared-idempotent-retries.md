# Order and Evidence Retry Safety

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As a customer, I want to retry order creation or evidence submission safely so that I can avoid duplicate effects.

## Acceptance Criteria

### AC-SHR-023

> **Given :** Customer repeats an order/evidence operation after failure or repeated action
> **When :** Same operation is submitted again
> **Then :**
>
> 1. Retry does not create duplicate effects under the approved idempotency policy

## Technical Flow

1. customer: Same operation is submitted again.
2. System: Retry does not create duplicate effects under the approved idempotency policy.

## Parameters

> **Weight per Parameter :** 15
> **New Technology :** TBD — no team assessment evidence available
> **Surrounding Involvement :** TBD — no confirmed count available
> **Technical Flow :** TBD — no assessed sequence count available
> **Acceptance Criteria :** TBD — team scoring rubric is not confirmed
> **Size :** TBD — no assessed scores or approved calculation available

## Subtask

### Frontend

| Task                           | Mandays | Story Point |
| ------------------------------ | ------: | ----------: |
| Show repeat submission outcome |     TBD |         TBD |
| **Total**                      | **TBD** |     **TBD** |

### Backend

| Task                             | Mandays | Story Point |
| -------------------------------- | ------: | ----------: |
| Apply approved idempotency rules |     TBD |         TBD |
| **Total**                        | **TBD** |     **TBD** |

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

- Open estimation question: [Cross-feature Questions](../../../sprint_1/acceptance-criteria/QUESTION.md) â€” sizing and effort remain TBD pending an approved rubric and estimates.
- BRD traceability: BR-024 in [Sprint 3 BRD](../../BRD.md).
- Team-approved sizing rubric, task estimates, and story point evidence were not available in supplied sources; assessments remain TBD. N/A marks an inapplicable discipline.
- This document describes BRD outcomes; solutioning coverage does not establish AC approval or implementation verification.
