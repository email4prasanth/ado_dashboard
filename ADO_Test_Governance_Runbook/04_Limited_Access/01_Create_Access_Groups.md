# Create Access Groups

## Required groups

Create three Azure DevOps groups in the test project:

```text
Governance Board Contributors
Governance Board Readers
Authorized Approvers
```

## Procedure

1. Open `Scrum-Governance-Test`.
2. Open **Project settings → Permissions**.
3. Select **New group**.
4. Create each group using the names above.
5. Add a description explaining the group's purpose.

## Membership model

| Group | Intended membership |
|---|---|
| Governance Board Contributors | Users who create and update Board work items |
| Governance Board Readers | View-only demonstration users |
| Authorized Approvers | Delivery managers/senior users authorized to approve Priority-1 items |

Do not add the Reader account to the project team or Contributors group. Default team membership can indirectly grant Contributor permissions.

Do not add Board users to:

- Project Administrators
- Project Collection Administrators
- Build Administrators
- Release Administrators

