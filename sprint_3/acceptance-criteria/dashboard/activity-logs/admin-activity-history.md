# Admin Activity History

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As an authorized administrator, I want to search and review recent administrative activity so that I can investigate changes and operational events.

## Acceptance Criteria

### AC-ADM-020

> **Given :** Administrator has activity-read permission
> **When :** Administrator searches, filters by category/date, or pages through events
> **Then :**
>
> 1. Matching events are visible with sensitive values redacted; unauthorized requests reveal no records

## Technical Flow

1. authorized administrator: Administrator searches, filters by category/date, or pages through events.
2. System: Matching events are visible with sensitive values redacted; unauthorized requests reveal no records.

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
| Present filtered activity history |     TBD |         TBD |
| **Total**                         | **TBD** |     **TBD** |

### Backend

| Task                                            | Mandays | Story Point |
| ----------------------------------------------- | ------: | ----------: |
| Provide authorized activity reads and redaction |     TBD |         TBD |
| **Total**                                       | **TBD** |     **TBD** |

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
- BRD traceability: BR-021 in [Sprint 3 BRD](../../../BRD.md).
- Team-approved sizing rubric, task estimates, and story point evidence were not available in supplied sources; assessments remain TBD. N/A marks an inapplicable discipline.
- This document describes BRD outcomes; solutioning coverage does not establish AC approval or implementation verification.
