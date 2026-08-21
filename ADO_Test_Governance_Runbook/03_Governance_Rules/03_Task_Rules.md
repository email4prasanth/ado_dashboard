# Task Rules

## TASK-01: Execution fields required

Open:

```text
Organization settings
→ Process
→ Scrum Hybrid Governance
→ Task
→ Rules
→ New rule
```

Configure:

```text
Name: TASK-01 Execution fields required for In Progress

When:
A work item state changes to → In Progress

Then:
Make required → Assigned To
Make required → Activity
Make required → Remaining Work
```

## TASK-02: Complete remaining work

Preferred behavior:

```text
Name: TASK-02 Set Remaining Work to zero for Done

When:
A work item state changes to → Done

Then:
Set the value of → Remaining Work → 0
```

Use this only if `Set the value of` and `Remaining Work` appear in the rule UI for Task.

Do not use “Remaining Work is required for Done” as a replacement. A nonempty value such as 8 would pass a required-field check even though the Task is Done.

## Why Remaining Work was missing previously

Remaining Work is normally a Task field. It might not appear while configuring rules for Product Backlog Item. Always confirm the selected work item type at the top of the process page.

