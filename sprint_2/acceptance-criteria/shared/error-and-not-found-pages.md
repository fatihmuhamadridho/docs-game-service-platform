# Access Denied and Not Found States

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As a Visitor or signed-in user, I want to understand when a page is forbidden or unavailable so that I can return to an accessible part of the service.

## Acceptance Criteria

### AC-SYS-001

> **Given :** A user lacks permission or requests a missing page
> **When :** The application renders its forbidden or not-found state
> **Then :**
>
> 1. The user sees a state that distinguishes access denied from unavailable content without exposing protected information.

## Technical Flow

1. User requests a page.
2. Application determines access is forbidden or page is not found.
3. Application presents the corresponding safe error state.

## Parameters

> **Weight per Parameter :** 15
> **New Technology :** TBD — no team assessment evidence available
> **Surrounding Involvement :** TBD — no confirmed count available
> **Technical Flow :** TBD — no assessed sequence count available
> **Acceptance Criteria :** TBD — team scoring rubric is not confirmed
> **Size :** TBD — no assessed scores or approved calculation available

## Subtask

### Frontend

| Task                                                               | Mandays | Story Point |
| ------------------------------------------------------------------ | ------: | ----------: |
| Render /403, forbidden, and not-found states with safe navigation. |     TBD |         TBD |
| **Total**                                                          | **TBD** |     **TBD** |

### Backend

| Task                                                                                                                                            | Mandays | Story Point |
| ----------------------------------------------------------------------------------------------------------------------------------------------- | ------: | ----------: |
| N/A — rendering states are frontend/framework behavior; protected-data decisions are covered by the relevant admin feature acceptance criteria. |     N/A |         N/A |
| **Total**                                                                                                                                       | **N/A** |     **N/A** |

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

- Open estimation question: [Cross-feature Questions](../../../sprint_1/acceptance-criteria/QUESTION.md) â€” sizing and effort remain TBD pending an approved rubric and estimates.
- Source traceability: BR-023 related to authorization; dedicated error-page behavior not specified. Product scope status remains unresolved where noted below.
- Observed routes/files: /403, forbidden.tsx, not-found.tsx. Exact copy, recovery links, and whether forbidden errors redirect or render in place are design/product decisions. No API-specific error message content is assumed.
- This document records observable UI/code behavior and open decisions; it does not claim implementation completeness or production readiness.
