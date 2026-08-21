# Azure DevOps Governance

## 1. Purpose

This document defines the state mapping, Product Backlog Item (PBI) governance rules, custom fields, and validation workflow for the Azure DevOps inherited process.

State availability can vary by work item type. Bug and Task workflows should be configured according to their specific requirements.

## 2. State Mapping

| State category | States |
|---|---|
| Proposed | New (default), Approved (default), Designed, To Do |
| In Progress | Committed (default), Active, In Planning, In Progress, Open, Ready |
| Resolved | On Hold |
| Completed | Done (default), Closed, Completed, Inactive |

> **Naming:** Use `Committed`, `In Progress`, `In Planning`, `On Hold`, and `Completed` consistently. Do not use `Commited`, `Inprogress`, or `Compeleted`.

## 3. Product Backlog Item Rules

### PBI-01: Planning Fields Required for Approved

**When**

```text
A work item state changes to → Approved
```

**Then**

```text
Make required → Description
Make required → Acceptance Criteria
Make required → Priority
Make required → Effort
```

**Purpose:** Ensure that the required planning information exists before delivery is approved.

### PBI-02: Owner Required for In Progress

**When**

```text
A work item state changes to → In Progress
```

**Then**

```text
Make required → Assigned To
```

### PBI-03: Reason Required for On Hold

**When**

```text
A work item state changes to → On Hold
```

**Then**

```text
Make required → Governance Reason
```

### PBI-04: Clear Reason When Work Resumes

**When**

```text
A work item state changes to → In Progress
```

**Then**

```text
Clear the value of → Governance Reason
```

This rule is optional. If the team needs to retain the latest hold reason for reporting, do not create PBI-04. Keep the value and use work item history for additional context.

### PBI-05a: Senior Approval for Priority 1 — Committed

**When**

```text
A work item state changes to → Committed
Priority equals → 1
```

**Then**

```text
Make required → Senior Approval Status
Make required → Approved By
```

### PBI-05b: Senior Approval for Priority 1 — Active

**When**

```text
A work item state changes to → Active
Priority equals → 1
```

**Then**

```text
Make required → Senior Approval Status
Make required → Approved By
```

### PBI-05c: Senior Approval for Priority 1 — In Progress

**When**

```text
A work item state changes to → In Progress
Priority equals → 1
```

**Then**

```text
Make required → Senior Approval Status
Make required → Approved By
```

> **Important:** Create PBI-05a, PBI-05b, and PBI-05c only if Azure DevOps permits the state and `Priority = 1` conditions together. Without the Priority condition, approval would incorrectly become mandatory for every PBI entering the selected state.

### PBI-06: Prevent Invalid Backward Movement

The original rule made `Description` read-only after movement from `On Hold` or `Approved`. A read-only field does not prevent a state transition, so that configuration should not be used as a transition control.

Use an Azure DevOps **Restrict the transition to state** action when backward movement must be prevented. Test the restriction before production use and retain an authorized recovery path.

### PBI-07: Restrict Movement Back from On Hold

**When**

```text
A work item state moved from → On Hold
```

**Then, where these states are enabled for the PBI workflow**

```text
Restrict the transition to state → New
Restrict the transition to state → Approved
Restrict the transition to state → Designed
Restrict the transition to state → To Do
```

This rule is optional. Add transition restrictions only after all required-field rules pass testing. Ensure that the workflow retains a controlled recovery path for authorized correction.

## 4. Custom Governance Fields

### 4.1 Open the Process

```text
Organization settings
→ Process
→ Scrum Hybrid Governance
→ Work Item Types
```

### 4.2 Product Backlog Item Fields

Open **Product Backlog Item → Layout → New field** and create the following fields.

#### Governance Reason

```text
Name: Governance Reason
Type: Picklist (string)
Required: No
Default: None
Allow users to enter their own values: No
```

Values:

```text
Awaiting dependency
Resource constraint
Environment unavailable
Business decision
Blocked
Deferred
Cancelled
Duplicate
Out of scope
Rejected
```

Do not set `Proposed` as the default. A default value can remain after the state changes and produce an incorrect explanation.

#### Senior Approval Status

```text
Name: Senior Approval Status
Type: Picklist (string)
Required: No
Default: None
Values: Pending, Approved, Rejected
```

#### Approved By

```text
Name: Approved By
Type: Identity
Required: No
Default: None
```

### 4.3 Bug Fields

Open **Bug → Layout → New field**.

#### Platform

```text
Type: Picklist (string)
Required: No
Default: None
Example values: Web, Android, iOS, API, Database, Infrastructure, Other
```

#### Resolution Notes

```text
Type: Text (multiple lines) or HTML, depending on the available UI option
Required: No
Default: None
```

### 4.4 Reuse Existing Fields

If `Governance Reason` already exists at the organization level:

1. Select **Use an existing field** instead of creating a duplicate.
2. Add the existing field to Epic, Feature, Task, and Bug where required.

Do not mark these fields globally required in field options. The conditional rules control when the fields become required.

## 5. PBI Validation Workflow

| Step | Transition | Expected validation |
|---|---|---|
| 1 | New PBI → Approved | Description, Acceptance Criteria, Priority, and Effort are required by PBI-01. |
| 2 | Approved → Committed | For Priority 1, Senior Approval Status and Approved By are required by PBI-05a. |
| 3 | Committed → Active | For Priority 1, Senior Approval Status and Approved By are required by PBI-05b. |
| 4 | Active → In Progress | Assigned To is required by PBI-02. For Priority 1, approval fields are required by PBI-05c. |
| 5 | In Progress → On Hold | Governance Reason is required by PBI-03. |
| 6 | On Hold → In Progress | Governance Reason is cleared by PBI-04 when that optional rule is enabled. Verify PBI-07 transition restrictions separately. |
| 7 | Ready → Done | The PBI completes successfully. |

## 6. Verification Checklist

1. Open a new PBI in the test project.
2. Confirm that Governance Reason, Senior Approval Status, and Approved By appear.
3. Verify PBI-01 through PBI-04 using the transitions in the validation workflow.
4. Verify that PBI-05 rules apply only when Priority equals 1.
5. Confirm that a non-Priority-1 PBI can enter the configured In Progress states without senior approval fields becoming mandatory.
6. Verify optional transition restrictions without removing all recovery paths.
7. Open a new Bug and confirm that Platform and Resolution Notes appear.
8. Record the test evidence before applying the rules to a production process.
