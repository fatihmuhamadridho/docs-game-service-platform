# Admin Payouts Prototype

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As an administrator, I want to review sample payout records and change their status in the dashboard prototype so that I can organize proposed partner payout requests.

## Acceptance Criteria

### AC-MOCK-008

> **Given :** Administrator opens payout management
> **When :** Administrator searches/filters, views details, or changes status
> **Then :**
>
> 1. Sample rows can be searched and filtered by status; details show masked account number; status changes appear in local state and terminal Berhasil/Ditolak states update a processed timestamp.

## Technical Flow

1. Administrator filters sample payout rows.
2. Administrator reviews payout details.
3. Administrator changes the sample status.
4. Page updates local status and processed time where applicable.

## Parameters

> **Weight per Parameter :** 15
> **New Technology :** TBD — no team assessment evidence available
> **Surrounding Involvement :** TBD — no confirmed count available
> **Technical Flow :** TBD — no assessed sequence count available
> **Acceptance Criteria :** TBD — team scoring rubric is not confirmed
> **Size :** TBD — no assessed scores or approved calculation available

## Subtask

### Frontend

| Task                                                                                     | Mandays | Story Point |
| ---------------------------------------------------------------------------------------- | ------: | ----------: |
| Render sample payout list, filters, details, masked account number, and status controls. |     TBD |         TBD |
| **Total**                                                                                | **TBD** |     **TBD** |

### Backend

| Task                                                                                  | Mandays | Story Point |
| ------------------------------------------------------------------------------------- | ------: | ----------: |
| N/A — observed page stores status changes in local UI state and executes no transfer. |     N/A |         N/A |
| **Total**                                                                             | **N/A** |     **N/A** |

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
- Source traceability: BR-015 covers transaction reporting; payout execution is explicitly out of scope. Product scope status remains unresolved where noted below.
- Observed source: AdminPayouts page/interface. No payout route/API contract exists in Go inventory and BRD excludes payout execution. Do not interpret mock status as bank transfer. Decide whether payout capability is needed, who approves it, required reconciliation/audit, and privacy controls.
- This document records observable UI/code behavior and open decisions; it does not claim implementation completeness or production readiness.
