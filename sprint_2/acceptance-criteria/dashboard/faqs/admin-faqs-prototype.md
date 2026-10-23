# Admin FAQ Prototype

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As an administrator, I want to maintain FAQ entries and publication status in the dashboard prototype so that I can organize help answers by category.

## Acceptance Criteria

### AC-MOCK-005

> **Given :** Administrator opens FAQ management
> **When :** Administrator searches/filters, creates/edits, or confirms deletion
> **Then :**
>
> 1. Rows are filtered by text/status; saved entries appear in local page state with question, answer, category, and Draft/Published status.

## Technical Flow

1. Administrator views FAQ entries.
2. Administrator filters or opens create/edit form.
3. Page updates local state after save or confirmed deletion.

## Parameters

> **Weight per Parameter :** 15
> **New Technology :** TBD — no team assessment evidence available
> **Surrounding Involvement :** TBD — no confirmed count available
> **Technical Flow :** TBD — no assessed sequence count available
> **Acceptance Criteria :** TBD — team scoring rubric is not confirmed
> **Size :** TBD — no assessed scores or approved calculation available

## Subtask

### Frontend

| Task                                                        | Mandays | Story Point |
| ----------------------------------------------------------- | ------: | ----------: |
| Render seeded FAQs, filters, form, and delete confirmation. |     TBD |         TBD |
| **Total**                                                   | **TBD** |     **TBD** |

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
- Source traceability: No BRD requirement. Product scope status remains unresolved where noted below.
- Observed source: AdminFaqs page/interface. Public Help page contains separate hardcoded FAQ content, so no publishing link is established. BRD has no FAQ management requirement. Decide whether dashboard entries drive public content and define editorial approval/localization before production.
- This document records observable UI/code behavior and open decisions; it does not claim implementation completeness or production readiness.
