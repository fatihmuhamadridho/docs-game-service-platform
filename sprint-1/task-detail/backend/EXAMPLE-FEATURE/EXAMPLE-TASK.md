# Sprint 1 - View Service Request Status - Backend Task Detail

> **Document Type:** Backend Task Detail  
> **Status:** Example  
> **Story / AC:** Acceptance Criteria feature (belum tersedia pada contoh)  
> **Solutioning:** TBD for this feature

## Task Overview

Illustrative estimates only. Confirm scope and effort with the delivery team.

| PIC               | Total Mandays |
| ----------------- | ------------: |
| Backend Developer |          1.25 |
| **Total**         |      **1.25** |

## Task Breakdown

### Customer Request List

| ID     | Task / Deliverable                                                                                                                                                    | Related AC / API                   | Mandays | PIC               |
| ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------- | ------: | ----------------- |
| BE-001 | Return only requests associated with the signed-in customer, including reference number, submission date, and current status; return an empty result when none exist. | AC-001, AC-003 / list contract TBD |    0.75 | Backend Developer |

### Request Details and Ownership

| ID     | Task / Deliverable                                                                                                                                                         | Related AC / API                     | Mandays | PIC               |
| ------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------ | ------: | ----------------- |
| BE-002 | Verify request ownership before returning details; include the latest customer-visible update, exclude internal-only notes, and deny access to another customer's request. | AC-002, AC-004 / detail contract TBD |    0.50 | Backend Developer |

## Dependencies and Open Questions

- **Dependencies:** Existing request data, caller identity from the authenticated session, and confirmed response fields.
- **Open questions:** Which statuses and updates are customer-visible? See Pertanyaan terbuka BRD (belum tersedia pada contoh). API contract remains TBD.

## Estimation Notes

- **Basis:** Matches the backend subtasks in the example acceptance criteria: 0.75 + 0.50 = 1.25 mandays.
- **Included:** Request retrieval, ownership filtering, response shaping, and verification.
- **Excluded:** Support staff update flow and frontend presentation.
