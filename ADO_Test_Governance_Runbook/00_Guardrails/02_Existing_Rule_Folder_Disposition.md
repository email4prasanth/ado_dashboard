# Existing Rule Folder Disposition

This runbook supersedes the original Rule-folder instructions for the test implementation. Do not combine both sets of instructions.

| Existing file | Decision | Replacement in this runbook |
|---|---|---|
| Rule_1 | Replace | PBI-02 and TASK-01 use actual states rather than an “In Progress category” condition |
| Rule_2 | Replace | Acceptance Criteria is required at Approved through PBI-01, not at On Hold |
| Rule_3 | Modify | Priority is required at Approved through PBI-01 to preserve quick work-item creation |
| Rule_4 | Replace | Effort is required before work starts; Task Remaining Work is set to zero at Done |
| Rule_5 | Replace | Description is required at Approved; Designed and To Do are not used |
| Rule_6 | Replace | PBI-05 includes the missing `Priority = 1` condition; PBI-06 protects Approved By |
| rULE_7 | Discard | Making Description read-only does not prevent a backward state transition |
| Rule_8 | Rebuild | Use the allowed-transition table and retain controlled recovery paths |
| Rule_9 | Replace | BUG-01 consistently validates In Progress and includes Platform |
| Rule_10 | Modify | Activity is required when Task enters In Progress, not during quick creation |
| Rule_11 | Discard | Empty file |
| skeleton | Discard | Empty file |

## Naming standard

Use these spellings everywhere:

```text
Product Backlog Item
Approved
In Progress
On Hold
Done
Removed
Committed (only if the inherited parent requires this state)
```

Do not use the misspelling `Commited`.

## State reduction

Do not bring the following experimental states into the first proof of concept:

```text
Designed
To Do
Active
In Planning
Open
Ready
Closed
Completed
Inactive
```

They overlap in meaning and make rules, board columns, reporting, and training unnecessarily difficult.

## Rule-number standard

Use work-item-specific identifiers:

```text
PBI-01, PBI-02, ...
TASK-01, TASK-02, ...
BUG-01, BUG-02, ...
```

This prevents ambiguous names such as “Rule 4” when several work item types have different rules.

