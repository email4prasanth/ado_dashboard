# Product Backlog Item Rules

## PBI-01: Planning fields required for Approved

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

## PBI-02: Owner required for In Progress

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

## Now Configure the Rule:
Name	"PBI-05a_Senior Approval for Priority 1-Commited"

Condition: A work item state changes to ... → Committed
Make required → Senior Approval Status
Make required → Approved By
Click Save

Name	"PBI-05b_Senior Approval for Priority 1 - Active"
Conditions (When)
Condition: A work item state changes to ... →  Active
Then:
Make required → Senior Approval Status
Make required → Approved By
Click Save

Name	"PBI-05c_Senior Approval for Priority 1 - Inprogress"
Conditions (When)
Condition: A work item state changes to ... →  Inprogress
Then:
Make required → Senior Approval Status
Make required → Approved By
Click Save

## PBI-06_No Moving from Resolved Back to Proposed
Conditions (When)
✅	A work item state changes from ...
✅	Field  On Hold
✅  Approved
Actions (Then)
✅	Make read-only ...
✅	Field Description
Final:
Click Save

## PBI-07_Block Moving Back from On Hold

Conditions (When)
Condition: A work item state moved from... →  On Hold
Actions: Restrict the transition to state.. → New

Actions: Restrict the transition to state.. → Approved

Actions: Restrict the transition to state.. → Designed

Actions: Restrict the transition to state.. → To Do
Click Save

