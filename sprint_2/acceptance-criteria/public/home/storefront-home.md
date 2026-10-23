# Storefront Home

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As a Visitor, I want to discover games and service categories from the storefront home page so that I can find a relevant next step toward browsing or checkout.

## Acceptance Criteria

### AC-HOME-001

> **Given :** Visitor opens the home page
> **When :** Popular games and service categories load or fail
> **Then :**
>
> 1. The page displays the available API-backed popular games and categories; failed category/game loading produces an empty collection without inventing catalog entries.

## Technical Flow

1. Visitor opens storefront home.
2. Storefront requests popular games and categories.
3. Storefront displays returned content or empty sections when a request fails.
4. Visitor can navigate to game discovery, checkout, or order tracking.

## Parameters

> **Weight per Parameter :** 15
> **New Technology :** TBD — no team assessment evidence available
> **Surrounding Involvement :** TBD — no confirmed count available
> **Technical Flow :** TBD — no assessed sequence count available
> **Acceptance Criteria :** TBD — team scoring rubric is not confirmed
> **Size :** TBD — no assessed scores or approved calculation available

## Subtask

### Frontend

| Task                                                                                    | Mandays | Story Point |
| --------------------------------------------------------------------------------------- | ------: | ----------: |
| Render the home sections, loading results, empty collections, and discovery navigation. |     TBD |         TBD |
| **Total**                                                                               | **TBD** |     **TBD** |

### Backend

| Task                                                                                | Mandays | Story Point |
| ----------------------------------------------------------------------------------- | ------: | ----------: |
| N/A — home composition is frontend-only; it consumes existing catalog capabilities. |     N/A |         N/A |
| **Total**                                                                           | **N/A** |     **N/A** |

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
- Open feature decisions: [Feature Questions](./QUESTION.md).
- Source traceability: BR-001, BR-003 (related catalog requirements; home composition is prototype-derived). Product scope status remains unresolved where noted below.
- Observed source: HomeV2 page/organisms. Home also displays hardcoded promotion and benefit claims, including speed, safety, competitive pricing, and 24/7 support. Product owner must approve or remove each claim before production publication. These are not BRD commitments.
- This document records observable UI/code behavior and open decisions; it does not claim implementation completeness or production readiness.
