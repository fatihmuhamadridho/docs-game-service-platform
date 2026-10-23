# Admin Bank Accounts Prototype

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As an administrator, I want to maintain bank-account entries and choose an active primary entry in the dashboard prototype so that I can organize candidate transfer instructions.

## Acceptance Criteria

### AC-MOCK-002

> **Given :** Administrator opens bank-account management
> **When :** Administrator searches/filters, creates/edits/deletes an entry, or marks it primary
> **Then :**
>
> 1. The page reflects these actions in local state, including active/inactive and primary status; no persisted payment instruction is implied.

## Technical Flow

1. Administrator sees sample bank-account entries.
2. Administrator filters or edits an entry.
3. Page updates local state and primary marker.

## Parameters

> **Weight per Parameter :** 15
> **New Technology :** TBD — no team assessment evidence available
> **Surrounding Involvement :** TBD — no confirmed count available
> **Technical Flow :** TBD — no assessed sequence count available
> **Acceptance Criteria :** TBD — team scoring rubric is not confirmed
> **Size :** TBD — no assessed scores or approved calculation available

## Subtask

### Frontend

| Task                                                                                 | Mandays | Story Point |
| ------------------------------------------------------------------------------------ | ------: | ----------: |
| Render bank-account list, filters, form, primary selection, and delete confirmation. |     TBD |         TBD |
| **Total**                                                                            | **TBD** |     **TBD** |

### Backend

| Task                                                       | Mandays | Story Point |
| ---------------------------------------------------------- | ------: | ----------: |
| N/A — observed page stores changes in local UI state only. |     N/A |         N/A |
| **Total**                                                  | **N/A** |     **N/A** |

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
- Source traceability: BR-009 related to payment instruction configuration; dashboard CRUD not approved. Product scope status remains unresolved where noted below.
- Observed source: AdminBankAccounts page/interface. Bank name, account number, holder, label, active status, and primary status are represented. BRD proposes manual payment methods but does not approve an admin bank-account CRUD capability. Decide owner, validation, privacy masking, primary uniqueness, and integration with public instructions.
- This document records observable UI/code behavior and open decisions; it does not claim implementation completeness or production readiness.
