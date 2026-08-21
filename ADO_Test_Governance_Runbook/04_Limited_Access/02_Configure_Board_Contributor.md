# Configure the Board Contributor

## Organization access level

1. Open **Organization settings → Users**.
2. Add or select the test Board Contributor.
3. Set access level to **Stakeholder**.
4. Limit the user to the `Scrum-Governance-Test` project if the organization UI provides project selection.

Stakeholder is suitable for demonstrating work-item access without Azure Repos access.

## Project membership

1. Open **Project settings → Permissions**.
2. Open `Governance Board Contributors`.
3. Select **Members → Add**.
4. Add the test user.
5. Add `Governance Board Contributors` to the built-in **Contributors** group if required to inherit normal work-item contribution permissions.

## Remove dangerous permissions

Verify the user/group does not have:

```text
Change process of team project
Bypass rules on work item updates
Delete team project
Delete and restore work items
Permanently delete work items
Manage permissions
```

Use `Not set` where the standard permission model already provides the safe result. Use explicit Deny only when another inherited Allow must be overridden, because Deny can override membership in other groups.

## Expected result

The user can:

- View Boards and backlogs.
- Create and edit permitted work items.
- Move cards when governance rules are satisfied.

The user cannot:

- Edit the inherited process.
- Bypass governance rules.
- Administer the project.
- Access Azure Repos with Stakeholder access.

