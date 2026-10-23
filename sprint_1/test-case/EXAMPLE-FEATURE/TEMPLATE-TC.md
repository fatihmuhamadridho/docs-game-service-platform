# [Feature Name] - Test Cases

> **Document Type:** Test Case  
> **Status:** Draft  
> **Story Link:** [Ticket or document link]  
> **Acceptance Criteria Link:** [Document link]

## Story

> As a [role], I want [capability] so that [benefit].

## Test Cases

Create one case for each distinct scenario or expected outcome. Use unique `TC-###` IDs and link each case to the acceptance criterion it verifies. Cover positive, negative, permission, empty, and boundary cases where applicable. Copy the block below for additional cases.

### TC-001 - [Short scenario title]

> **Related AC:** [AC-001]  
> **Role:** [User or system role]  
> **Behaviour:** [Positive / Negative / Boundary]  
> **Priority:** [High / Medium / Low]  
> **Type:** [Functional / Integration / Other]

#### Preconditions

- [Starting user, system, and data state required for this case.]
- [Required access or permission, if applicable.]

#### Test Data

| Data               | Value / Condition                   |
| ------------------ | ----------------------------------- |
| [Field or fixture] | [Representative value or condition] |

#### Steps and Expected Results

State one action and its observable result per row. Include the final outcome; do not assume a successful result from an action alone.

| #   | Action                   | Expected Result         |
| --- | ------------------------ | ----------------------- |
| 1   | [Action or event]        | [Observable result]     |
| 2   | [Next action, if needed] | [Final expected result] |

#### Cleanup / Postconditions

- [Required cleanup or final state; write `None` if not applicable.]
