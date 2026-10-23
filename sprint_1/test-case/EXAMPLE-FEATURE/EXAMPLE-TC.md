# View Service Request Status - Test Cases

> **Document Type:** Test Case  
> **Status:** Example  
> **Story Link:** [Example BRD](../../EXAMPLE-BRD.md)  
> **Acceptance Criteria Link:** [View Service Request Status](../../acceptance-criteria/EXAMPLE-FEATURE/EXAMPLE-ACCEPTANCE_CRITERIA.md)

## Story

> As a customer, I want to view the status of a service request I submitted so that I can understand its progress without contacting support.

## Test Cases

### TC-001 - Customer sees own request list

> **Related AC:** AC-001  
> **Role:** Customer  
> **Behaviour:** Positive  
> **Priority:** High  
> **Type:** Functional

#### Preconditions

- Customer A is signed in and has one submitted service request.
- Customer B has a separate submitted service request.

#### Test Data

| Data               | Value / Condition                                                   |
| ------------------ | ------------------------------------------------------------------- |
| Customer A request | Reference `REQ-A-001`; submitted `2026-10-01`; status `In progress` |
| Customer B request | Reference `REQ-B-001`                                               |

#### Steps and Expected Results

| #   | Action                                       | Expected Result                                                                     |
| --- | -------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1   | Open the service request list as Customer A. | `REQ-A-001` appears with its reference number, submission date, and current status. |
| 2   | Review all listed requests.                  | `REQ-B-001` and other customers' requests do not appear.                            |

#### Cleanup / Postconditions

- None. Test data remains available for other cases.

### TC-002 - Customer sees details without internal notes

> **Related AC:** AC-002  
> **Role:** Customer  
> **Behaviour:** Positive  
> **Priority:** High  
> **Type:** Functional

#### Preconditions

- Customer A is signed in and can see `REQ-A-001` in the request list.
- The request has a customer-visible update and an internal-only note.

#### Test Data

| Data                    | Value / Condition                 |
| ----------------------- | --------------------------------- |
| Customer-visible update | `Your request is being reviewed.` |
| Internal-only note      | `Internal review note`            |

#### Steps and Expected Results

| #   | Action                          | Expected Result                                                             |
| --- | ------------------------------- | --------------------------------------------------------------------------- |
| 1   | Open `REQ-A-001` from the list. | Request details, current status, and latest customer-visible update appear. |
| 2   | Review all visible details.     | `Internal review note` does not appear.                                     |

#### Cleanup / Postconditions

- None.

### TC-003 - Customer with no requests sees empty state

> **Related AC:** AC-003  
> **Role:** Customer  
> **Behaviour:** Boundary  
> **Priority:** Medium  
> **Type:** Functional

#### Preconditions

- Customer C is signed in and has no submitted service requests.
- Other customers have submitted requests.

#### Test Data

| Data                | Value / Condition |
| ------------------- | ----------------- |
| Customer C requests | None              |

#### Steps and Expected Results

| #   | Action                                       | Expected Result                                         |
| --- | -------------------------------------------- | ------------------------------------------------------- |
| 1   | Open the service request list as Customer C. | An empty state explains that no requests are available. |
| 2   | Review the page.                             | No other customer's request appears.                    |

#### Cleanup / Postconditions

- None.

### TC-004 - Customer cannot view another customer's request

> **Related AC:** AC-004  
> **Role:** Customer  
> **Behaviour:** Negative  
> **Priority:** High  
> **Type:** Functional

#### Preconditions

- Customer A is signed in.
- `REQ-B-001` belongs to Customer B, and Customer A has a link to its detail page.

#### Test Data

| Data                     | Value / Condition |
| ------------------------ | ----------------- |
| Other customer's request | `REQ-B-001`       |

#### Steps and Expected Results

| #   | Action                                              | Expected Result                                                                     |
| --- | --------------------------------------------------- | ----------------------------------------------------------------------------------- |
| 1   | Open the detail link for `REQ-B-001` as Customer A. | Access to the request details is denied.                                            |
| 2   | Review the page.                                    | A safe unavailable message appears; no request details or internal notes are shown. |

#### Cleanup / Postconditions

- None.
