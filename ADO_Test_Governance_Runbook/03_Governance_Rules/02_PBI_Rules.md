# Product Backlog Item Rules

## How to add each rule

For every rule below:

1. Open **Organization settings → Process → Scrum Hybrid Governance**.
2. Select **Product Backlog Item**.
3. Select **Rules → New rule**.
4. Enter the rule name exactly.
5. Add the listed condition or conditions.
6. Add the listed actions.
7. Save.
8. Open a test PBI and immediately verify the rule.

UI labels can vary slightly. Select the matching condition/action shown in your Azure DevOps screen; do not substitute an unrelated action.

## PBI-01: Planning fields required for Approved/Rule_2

```text
When:
A work item state changes to → Approved

Then:
Make required → Description
Make required → Acceptance Criteria
Make required → Priority
Make required → Effort
```

Purpose: planning information exists before delivery is approved.

## PBI-02: Owner required for In Progress/ Rule_1

```text
When:
A work item state changes to → In Progress

Then:
Make required → Assigned To
```

## PBI-03: Reason required for On Hold

```text
When:
A work item state changes to → On Hold

Then:
Make required → Governance Reason
```

## PBI-04: Clear reason when work resumes

```text
When:
A work item state changes to → In Progress

Then:
Clear the value of → Governance Reason
```

If the team needs to retain the latest hold reason for reporting, do not create PBI-04; keep the value and use work item history for context.

## PBI-05: Priority-1 approval required

Create this only if the UI permits both conditions together:

```text
When:
A work item state changes to → In Progress
Field value → Priority equals 1

Then:
Make required → Senior Approval Status
Make required → Approved By
```

If the rule UI does not permit this combination, stop and record the limitation. Do not make approval mandatory for every priority by removing the Priority condition.

## PBI-06: Protect the Approved By field

Prerequisite: the `Authorized Approvers` Azure DevOps group must exist.

```text
When:
Current user is not a member of group → Authorized Approvers

Then:
Make read-only → Approved By
```

This prevents ordinary contributors from entering someone else's identity as evidence of approval.

## PBI-07: Restrict invalid backward movement - optional

Add transition restrictions only after all mandatory-field rules pass testing.

Example:

```text
When:
A work item state moved from → Done

Then:
Restrict the transition to state → New
Restrict the transition to state → Approved
```

Keep a controlled recovery path such as Done → In Progress for authorized correction. Do not make Description read-only as a substitute for restricting the State transition.

