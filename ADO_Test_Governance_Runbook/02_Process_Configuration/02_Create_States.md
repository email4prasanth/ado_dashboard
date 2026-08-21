# Create the Workflow States

## Checkpoint

Open:

```text
Organization settings
→ Process
→ Scrum Hybrid Governance
```

Make changes only inside `Scrum Hybrid Governance`.

## State-category mapping

Use the following mapping consistently:

| State | State category |
|---|---|
| New | Proposed |
| Approved | Proposed |
| In Progress | In Progress |
| On Hold | Resolved |
| Done | Completed |
| Removed | Removed |

The category controls how Azure Boards treats the state in backlogs and boards. The displayed state name and category are different concepts.

## Epic, Feature and Product Backlog Item

Repeat this procedure for Epic, Feature, and Product Backlog Item:

1. Select the work item type.
2. Select **States**.
3. Record the inherited states before changing anything.
4. Add any missing states from the mapping table.
5. Assign the exact category shown in the table.
6. Hide unnecessary inherited states only after verifying that the test project has no work items using them.
7. Keep no more than these six visible states:

```text
New
Approved
In Progress
On Hold
Done
Removed
```

## Task and Bug

For Task and Bug, use:

```text
New
In Progress
On Hold
Done
Removed
```

`Approved` is not required for the first Task/Bug proof of concept.

## Important limitation concerning Reason

The system State and Reason area is inherited and has limited customization in the inherited-process model.

For business explanations such as dependency, resource constraint, duplicate, or cancellation, create a separate `Governance Reason` field. Do not assume that the native Reason field can be replaced or that its choices can be freely customized.

## Board configuration

After adding states:

1. Open **Boards → Boards** in the test project.
2. Open **Board settings → Columns**.
3. Map every workflow state to a board column.
4. Ensure no state is shown as unmapped.
5. Save and refresh the board.

