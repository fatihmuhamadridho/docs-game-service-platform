# Admin Manual Top-Up Prototype

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As an administrator, I want to inspect sample manual top-up requests and change their workflow status in the dashboard prototype so that I can organize top-up operations.

## Acceptance Criteria

### AC-MOCK-006

> **Given :** Administrator opens manual top-up requests
> **When :** Administrator searches/filters, opens a request, or changes its status
> **Then :**
>
> 1. Sample requests can be filtered by text/status, details are viewable, and status changes appear in local state.

## Technical Flow

1. Administrator searches or filters sample requests.
2. Administrator opens request details.
3. Administrator selects a status change.
4. Page updates local request state.

## Parameters

> **Weight per Parameter :** 15
> **New Technology :** TBD — no team assessment evidence available
> **Surrounding Involvement :** TBD — no confirmed count available
> **Technical Flow :** TBD — no assessed sequence count available
> **Acceptance Criteria :** TBD — team scoring rubric is not confirmed
> **Size :** TBD — no assessed scores or approved calculation available

## Subtask

### Frontend

| Task                                                                            | Mandays | Story Point |
| ------------------------------------------------------------------------------- | ------: | ----------: |
| Render request list, search/status filters, detail, and status action outcomes. |     TBD |         TBD |
| **Total**                                                                       | **TBD** |     **TBD** |

### Backend

| Task                                                              | Mandays | Story Point |
| ----------------------------------------------------------------- | ------: | ----------: |
| N/A — observed page stores status changes in local UI state only. |     N/A |         N/A |
| **Total**                                                         | **N/A** |     **N/A** |

### Summary

> **FE :** TBD
> **BE :** N/A
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
- Source traceability: No BRD requirement; top-up order scope may overlap BR-007 but this workflow is distinct. Product scope status remains unresolved where noted below.
- Observed source: AdminManualTopUps page/interface. Samples include customer/WhatsApp, game account, server, package, amount, payment reference, notes, and statuses Menunggu/Diproses/Selesai/Ditolak. BRD has no dedicated manual-top-up operations workflow. Decide operational ownership, PII access, validation, payment/fulfillment transitions, and whether this is a distinct product capability.
- This document records observable UI/code behavior and open decisions; it does not claim implementation completeness or production readiness.
