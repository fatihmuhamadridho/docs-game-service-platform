# Admin Banners Prototype

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As an administrator, I want to organize homepage/game/promo banner entries in the dashboard prototype so that I can prepare promotional placements.

## Acceptance Criteria

### AC-MOCK-003

> **Given :** Administrator opens banner management
> **When :** Administrator searches/filters or creates/edits/deletes a banner
> **Then :**
>
> 1. Rows can be narrowed by text, active status, and placement; changes appear in local page state.

## Technical Flow

1. Administrator views banner examples.
2. Administrator filters or edits banner metadata.
3. Page updates local list after save or confirmed delete.

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
| Render seeded banners, search/status/placement filters, and create/edit/delete controls. |     TBD |         TBD |
| **Total**                                                                                | **TBD** |     **TBD** |

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
- Observed source: AdminBanners page/interface. Fields include title, description, image URL/name, target URL, placement, active flag, and priority. Image upload/publication behavior is not established. No banner requirement exists in BRD; confirm product scope, content approval, link safety, and asset handling.
- This document records observable UI/code behavior and open decisions; it does not claim implementation completeness or production readiness.
