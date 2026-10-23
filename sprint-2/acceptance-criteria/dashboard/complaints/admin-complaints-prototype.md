# Admin Complaints Prototype

> **Document Type :** Acceptance Criteria
> **Status :** Draft

## Story

> As an administrator, I want to review complaint conversations, change status, and reply in the dashboard prototype so that I can organize customer support requests.

## Acceptance Criteria

### AC-MOCK-004

> **Given :** Administrator opens complaints list
> **When :** Administrator searches/filters, opens a complaint, changes status, or sends a reply
> **Then :**
>
> 1. Search/status/priority filters narrow sample records; detail shows conversation; nonblank reply is added to local thread and a Baru item becomes Diproses; selected status changes in local state.

## Technical Flow

1. Administrator filters complaint records.
2. Administrator opens a conversation.
3. Administrator changes status or submits a nonblank reply.
4. Page updates the local conversation/status.

## Parameters

> **Weight per Parameter :** 15
> **New Technology :** TBD — no team assessment evidence available
> **Surrounding Involvement :** TBD — no confirmed count available
> **Technical Flow :** TBD — no assessed sequence count available
> **Acceptance Criteria :** TBD — team scoring rubric is not confirmed
> **Size :** TBD — no assessed scores or approved calculation available

## Subtask

### Frontend

| Task                                                                                      | Mandays | Story Point |
| ----------------------------------------------------------------------------------------- | ------: | ----------: |
| Render complaint list, filters, conversation detail, status controls, and reply feedback. |     TBD |         TBD |
| **Total**                                                                                 | **TBD** |     **TBD** |

### Backend

| Task                                                                      | Mandays | Story Point |
| ------------------------------------------------------------------------- | ------: | ----------: |
| N/A — observed page stores replies/status changes in local UI state only. |     N/A |         N/A |
| **Total**                                                                 | **N/A** |     **N/A** |

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
- Observed source: AdminComplaints page/interface. Seeded records include customer/contact/order data and message history. BRD has no complaint workflow. Define customer submission channel, privacy/retention, SLA, status transitions, permissions, notification behavior, and persistence before production; prototype data is not real customer data.
- This document records observable UI/code behavior and open decisions; it does not claim implementation completeness or production readiness.
