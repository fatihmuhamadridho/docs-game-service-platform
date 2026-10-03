# View Service Request Status

> **Document Type :** Acceptance Criteria
> **Status :** Example

## Story

> As a customer, I want to view the status of a service request I submitted so that I can understand its progress without contacting support.

## Acceptance Criteria

### AC-001

> **Given :** The customer is signed in and has submitted at least one service request.
> **When :** The customer opens the request list
> **Then :**
>
> 1. The system shows only requests associated with the signed-in customer.
> 2. Each request shows its reference number, submission date, and current status.

### AC-002

> **Given :** The customer is signed in and can see a service request in the list.
> **When :** The customer opens that request
> **Then :**
>
> 1. The system shows the request details, current status, and latest customer-visible update.
> 2. The system does not show internal-only notes.

### AC-003

> **Given :** The customer is signed in and has not submitted any service requests.
> **When :** The customer opens the request list
> **Then :**
>
> 1. The system shows an empty state explaining that no requests are available.
> 2. The system does not show another customer's request.

### AC-004

> **Given :** The customer is signed in and opens a request that does not belong to their account.
> **When :** The customer attempts to view the request
> **Then :**
>
> 1. The system denies access to the request details.
> 2. The system shows a safe message that the request is unavailable.

## Technical Flow

1. Customer: Sign in to the customer portal and open the service request list.
2. Customer portal: Request the signed-in customer's service requests.
3. Request service: Verify customer identity and return only that customer's requests.
4. Customer portal: Display request reference, submission date, and current status.
5. Customer: Select a request to view its details.
6. Customer portal: Request the selected request details.
7. Request service: Verify request ownership and exclude internal-only notes.
8. Customer portal: Display the request details and latest customer-visible update, or show an unavailable message if access is denied.

## Parameters

Illustrative estimate only. Replace with estimates assessed by the delivery team.

> **Weight per Parameter :** 15
> **New Technology :** Experienced in project (1)
> **Surrounding Involvement :** 1 (2)
> **Technical Flow :** 5-7 sequence (2)
> **Acceptance Criteria :** 1-4 (1)
> **Size :** (1 x 15) + (2 x 15) + (2 x 15) + (1 x 15) = 15 + 30 + 30 + 15 = 90 (S)

## Subtask

Illustrative task estimates only.

### Frontend

| Task                                  |  Mandays | Story Point |
| ------------------------------------- | -------: | ----------: |
| Show request list and request details |     0.50 |        1.00 |
| Show empty and unavailable states     |     0.25 |        0.50 |
| **Total**                             | **0.75** |    **1.50** |

### Backend

| Task                                              |  Mandays | Story Point |
| ------------------------------------------------- | -------: | ----------: |
| Return requests filtered by signed-in customer    |     0.50 |        1.00 |
| Enforce request ownership and hide internal notes |     0.50 |        1.00 |
| **Total**                                         | **1.00** |    **2.00** |

### Summary

> **FE :** 1.50
> **BE :** 2.00
> **Size Task :** S

## Sizing Reference

| SIZE | SCORE RANGE | ESTIMATE COMPLETION | STORY POINT |
| ---- | ----------- | ------------------- | ----------- |
| S    | ≦ 100       | < 2 days            | 1-3         |
| M    | 101-200     | 2 – 3 days          | 4-6         |
| L    | 201-250     | > 3 – 5 days        | 7-10        |
| XL   | > 250       | > 5 days            | > 10        |

## Notes

1. The example uses the sizing reference and estimation assumptions shown above; confirm estimates and sizing against the team’s agreed process.
2. Example story point values are estimated as mandays multiplied by two.
3. Example task size follows the story size for illustration; confirm this rule with the team before applying it to delivery work.
