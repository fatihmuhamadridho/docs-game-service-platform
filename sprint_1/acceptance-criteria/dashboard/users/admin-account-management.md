# Admin Account Management

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As an authorized administrator, I want to manage admin accounts with explicit role assignment so that I can control and audit privileged access.

## Acceptance Criteria

### AC-ADM-014

> **Given :** Administrator has approved admin-account authority
> **When :** Administrator creates or changes an account and assigns a role
> **Then :**
>
> 1. Only explicitly assigned approved access is granted; credential setup follows approved identity/invitation flow; protected assignments are prevented

## Technical Flow

1. authorized administrator: Administrator creates or changes an account and assigns a role.
2. System: Only explicitly assigned approved access is granted; credential setup follows approved identity/invitation flow; protected assignments are prevented.

## Parameters

> **Weight per Parameter :** 15
> **New Technology :** TBD — no team assessment evidence available
> **Surrounding Involvement :** TBD — no confirmed count available
> **Technical Flow :** TBD — no assessed sequence count available
> **Acceptance Criteria :** TBD — team scoring rubric is not confirmed
> **Size :** TBD — no assessed scores or approved calculation available

## Subtask

### Frontend

| Task                              | Mandays | Story Point |
| --------------------------------- | ------: | ----------: |
| Present permitted account actions |     TBD |         TBD |
| **Total**                         | **TBD** |     **TBD** |

### Backend

| Task                                                                   | Mandays | Story Point |
| ---------------------------------------------------------------------- | ------: | ----------: |
| Enforce authority, explicit role assignment, approved credential setup |     TBD |         TBD |
| **Total**                                                              | **TBD** |     **TBD** |

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
- BRD traceability: BR-017 in [Sprint 1 BRD](../../../BRD.md).
- Team-approved sizing rubric, task estimates, and story point evidence were not available in supplied sources; assessments remain TBD. N/A marks an inapplicable discipline.
- This document describes BRD outcomes; solutioning coverage does not establish AC approval or implementation verification.
