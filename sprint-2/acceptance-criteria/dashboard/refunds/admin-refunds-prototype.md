# Admin Refunds Prototype

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As an administrator, I want to review sample refund requests and change status or admin note in dashboard prototype so that I can organize proposed refund decisions.

## Acceptance Criteria

### AC-MOCK-009

> **Given :** Administrator opens refund management
> **When :** Administrator searches/filters, views details, or changes status and note
> **Then :**
>
> 1. Sample rows can be searched and filtered by status; details show order/customer/payment context; status/note changes appear in local state only.

## Technical Flow

1. Administrator filters sample refund rows.
2. Administrator reviews details and reason.
3. Administrator changes the mock decision/status and note.
4. Page updates local state.

## Parameters

> **Weight per Parameter :** 15
> **New Technology :** TBD — no team assessment evidence available
> **Surrounding Involvement :** TBD — no confirmed count available
> **Technical Flow :** TBD — no assessed sequence count available
> **Acceptance Criteria :** TBD — team scoring rubric is not confirmed
> **Size :** TBD — no assessed scores or approved calculation available

## Subtask

### Frontend

| Task                                                                                | Mandays | Story Point |
| ----------------------------------------------------------------------------------- | ------: | ----------: |
| Render sample refund list, search/status filter, details, status and note controls. |     TBD |         TBD |
| **Total**                                                                           | **TBD** |     **TBD** |

### Backend

| Task                                                                         | Mandays | Story Point |
| ---------------------------------------------------------------------------- | ------: | ----------: |
| N/A — observed page stores changes in local UI state and executes no refund. |     N/A |         N/A |
| **Total**                                                                    | **N/A** |     **N/A** |

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
- Source traceability: BR-015 covers recorded refund transactions; refund execution is explicitly out of scope. Product scope status remains unresolved where noted below.
- Observed source: AdminRefunds page/interface. No refund execution contract exists; BRD excludes refund execution. Mock Disetujui/Selesai status does not move funds. Decide eligibility, authority, state transitions, customer notification, financial reconciliation, and audit before production.
- This document records observable UI/code behavior and open decisions; it does not claim implementation completeness or production readiness.
