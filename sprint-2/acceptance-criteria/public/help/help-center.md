# Help Center Page

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As a Customer, I want to search help content, browse topics, expand FAQs, and request support so that I can find guidance or learn when support is unavailable.

## Acceptance Criteria

### AC-HELP-001

> **Given :** Customer opens the help center
> **When :** Customer searches content, selects a topic, expands a FAQ, or selects contact admin
> **Then :**
>
> 1. Search narrows visible articles and questions; topic selection filters articles; FAQ answers can be expanded; contact action shows configured-channel status.

## Technical Flow

1. Customer opens help page.
2. Customer searches or selects a topic.
3. Storefront displays matching articles/questions or empty-result copy.
4. Customer expands an answer or selects support contact.
5. Storefront reports when the support channel is not configured.

## Parameters

> **Weight per Parameter :** 15
> **New Technology :** TBD — no team assessment evidence available
> **Surrounding Involvement :** TBD — no confirmed count available
> **Technical Flow :** TBD — no assessed sequence count available
> **Acceptance Criteria :** TBD — team scoring rubric is not confirmed
> **Size :** TBD — no assessed scores or approved calculation available

## Subtask

### Frontend

| Task                                                             | Mandays | Story Point |
| ---------------------------------------------------------------- | ------: | ----------: |
| Render topic/search/article/FAQ interactions and contact status. |     TBD |         TBD |
| **Total**                                                        | **TBD** |     **TBD** |

### Backend

| Task                                                               | Mandays | Story Point |
| ------------------------------------------------------------------ | ------: | ----------: |
| N/A — observed content and interactions are hardcoded in frontend. |     N/A |         N/A |
| **Total**                                                          | **N/A** |     **N/A** |

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
- Source traceability: No direct BRD requirement; related to BR-011/012 only as guidance. Product scope status remains unresolved where noted below.
- Observed source: Help page. Seed content references refunds, guarantees, vouchers, estimates, and security; these policy statements are mock content and must be reviewed by product/operations before publication. The page is FEATURE_SPRINT_2 gated. Product decision needed on release inclusion, approved article/FAQ content, and official support channel.
- This document records observable UI/code behavior and open decisions; it does not claim implementation completeness or production readiness.
