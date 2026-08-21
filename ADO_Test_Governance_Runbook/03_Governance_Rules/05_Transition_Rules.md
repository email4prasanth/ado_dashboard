# State Transition Rules

## Implement transition rules last

Do not create transition restrictions until:

- All required states exist.
- All board columns are mapped.
- Required-field rules pass.
- The team agrees on recovery paths.

## Allowed transitions

### Epic, Feature and PBI

| From | Allowed destinations |
|---|---|
| New | Approved, Removed |
| Approved | In Progress, On Hold, Removed |
| In Progress | On Hold, Done, Removed |
| On Hold | In Progress, Removed |
| Done | In Progress for controlled correction |
| Removed | New for controlled recovery |

### Task and Bug

| From | Allowed destinations |
|---|---|
| New | In Progress, Removed |
| In Progress | On Hold, Done, Removed |
| On Hold | In Progress, Removed |
| Done | In Progress for controlled correction |
| Removed | New for controlled recovery |

## How restrictions work

Azure DevOps transition rules list the destinations that must be restricted, not the destinations that are allowed.

Example for On Hold:

```text
When:
A work item state moved from → On Hold

Then restrict transitions to:
New
Approved
Done
```

This leaves `In Progress` and `Removed` available.

## Procedure

1. Select the work item type.
2. Open **Rules → New rule**.
3. Select `A work item state moved from`.
4. Select one source state.
5. Add a `Restrict the transition to state` action for every disallowed destination.
6. Save.
7. Verify the State dropdown.
8. Verify board drag-and-drop.
9. Repeat for the next source state.

Do not add unrelated conditions to a `state moved from` transition rule if the UI prohibits them.

