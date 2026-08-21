# Configure the Board Reader

## Procedure

1. Open **Organization settings → Users**.
2. Add or select the Reader test identity.
3. Set access level to **Stakeholder**.
4. Open **Project settings → Permissions**.
5. Add the identity to `Governance Board Readers`.
6. Add `Governance Board Readers` to the built-in **Readers** group if required.
7. Confirm the identity is not a member of:

```text
Project team
Contributors
Governance Board Contributors
Project Administrators
```

## Expected result

The Reader can:

- Open the private test project.
- View permitted Boards, backlogs, queries, and work items.

The Reader cannot:

- Create or edit work items.
- Move cards.
- change process or project settings.
- Access disabled project services.

## Effective-access check

1. Open **Project settings → Permissions → Users**.
2. Select the Reader identity.
3. Inspect effective permissions.
4. Confirm `View project-level information` is allowed.
5. Confirm contribution and administration permissions are not allowed.
6. Sign in using a separate browser profile to test the actual experience.

