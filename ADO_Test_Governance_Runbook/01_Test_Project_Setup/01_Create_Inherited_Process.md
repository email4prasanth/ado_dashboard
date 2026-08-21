# Create the Scrum Hybrid Governance Process

## Checkpoint

Confirm the top-left organization selector shows `inlai-projects`.

## Choose the correct parent

Use this decision:

- If `Scrum Hybrid` exists in the testing organization and is an inherited process, create a child inherited process from it.
- If it does not exist, create `Scrum Hybrid Governance` directly from the Microsoft `Scrum` system process.
- Do not create or copy anything from the production organization.

## Procedure

1. Open **Organization settings**.
2. Select **Process**.
3. Locate the selected parent process.
4. Select the process `...` menu.
5. Select **Create inherited process**.
6. Enter:

```text
Name: Scrum Hybrid Governance
Description: Isolated test process for Azure Boards workflow, mandatory fields, approvals and access governance.
```

7. Select **Create process**.
8. Return to the Process list.
9. Open `Scrum Hybrid Governance`.
10. Confirm that it currently has no production projects assigned.
11. Capture a screenshot showing the new process and its parent.

## Result

The process exists but does not affect any project until a project is created with it or changed to it.

## Troubleshooting

- If **Create inherited process** is unavailable, request the specific `Create process` permission.
- If the parent process cannot have a child, use the standard Scrum process as the parent.
- If the name already exists, do not reuse or edit it until its owner and assigned projects are verified.

