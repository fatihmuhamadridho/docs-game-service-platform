# Admin Member Management

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As an authorized administrator, I want to manage member accounts as allowed so that I can support customer lifecycle without granting admin privileges.

## Acceptance Criteria

### AC-ADM-013

> **Given :** Administrator has member-management permission
> **When :** Administrator creates, views, updates, or removes a member
> **Then :**
>
> 1. Allowed member action is applied; it cannot grant administrative access implicitly
> 2. Invalid input or missing permission rejects the action; protected data remains unchanged and is not disclosed

## Technical Flow

1. authorized administrator: Administrator creates, views, updates, or removes a member.
2. System: Allowed member action is applied; it cannot grant administrative access implicitly.
3. System: Invalid input or missing permission rejects the action; protected data remains unchanged and is not disclosed.

## Parameters

> **Weight per Parameter :** 15
> **New Technology :** TBD — no team assessment evidence available
> **Surrounding Involvement :** TBD — no confirmed count available
> **Technical Flow :** TBD — no assessed sequence count available
> **Acceptance Criteria :** TBD — team scoring rubric is not confirmed
> **Size :** TBD — no assessed scores or approved calculation available

## Subtask

### Frontend

| Task                             | Mandays | Story Point |
| -------------------------------- | ------: | ----------: |
| Present permitted member actions |     TBD |         TBD |
| **Total**                        | **TBD** |     **TBD** |

### Backend

| Task                                                                | Mandays | Story Point |
| ------------------------------------------------------------------- | ------: | ----------: |
| Authorize and apply member actions separately from admin privileges |     TBD |         TBD |
| **Total**                                                           | **TBD** |     **TBD** |

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

- Open estimation question: [Cross-feature Questions](../../../../sprint_1/acceptance-criteria/QUESTION.md) â€” sizing and effort remain TBD pending an approved rubric and estimates.
- Open feature decisions: [Feature Questions](../../../../sprint_2/acceptance-criteria/dashboard/users/QUESTION.md).
- BRD traceability: BR-016 in [Sprint 1 BRD](../../../BRD.md).
- Team-approved sizing rubric, task estimates, and story point evidence were not available in supplied sources; assessments remain TBD. N/A marks an inapplicable discipline.
- This document describes BRD outcomes; solutioning coverage does not establish AC approval or implementation verification.
