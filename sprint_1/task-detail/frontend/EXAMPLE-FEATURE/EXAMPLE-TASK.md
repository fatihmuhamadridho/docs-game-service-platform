# Sprint 1 - View Service Request Status - Frontend Task Detail

> **Document Type:** Frontend Task Detail  
> **Status:** Example  
> **Story / AC:** [View Service Request Status](../../acceptance-criteria/EXAMPLE-FEATURE/EXAMPLE-ACCEPTANCE_CRITERIA.md)  
> **Design:** TBD

## Task Overview

Illustrative estimates only. Confirm scope and effort with the delivery team.

| PIC                | Total Mandays |
| ------------------ | ------------: |
| Frontend Developer |          0.75 |
| **Total**          |      **0.75** |

## Task Breakdown

### Request List and Details

| ID     | Task / Deliverable                                                                                                                                               | Related AC     | Mandays | PIC                |
| ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- | ------: | ------------------ |
| FE-001 | Show the signed-in customer's request list with reference number, submission date, and status; show selected request details and latest customer-visible update. | AC-001, AC-002 |    0.50 | Frontend Developer |

### Empty and Unavailable States

| ID     | Task / Deliverable                                                                                           | Related AC     | Mandays | PIC                |
| ------ | ------------------------------------------------------------------------------------------------------------ | -------------- | ------: | ------------------ |
| FE-002 | Show an empty state when no requests exist and a safe unavailable message when a request cannot be accessed. | AC-003, AC-004 |    0.25 | Frontend Developer |

## Dependencies and Open Questions

- **Dependencies:** Request list and detail responses that enforce customer ownership and omit internal-only notes; approved UI design.
- **Open questions:** Which request statuses are customer-visible, and what target browsers are supported? See [BRD open questions](../../EXAMPLE-BRD.md#7-open-questions).

## Estimation Notes

- **Basis:** Matches the frontend subtasks in the example acceptance criteria: 0.50 + 0.25 = 0.75 mandays.
- **Included:** UI implementation, integration, and verification of list, detail, empty, and unavailable states.
- **Excluded:** Backend access control and request data changes.
