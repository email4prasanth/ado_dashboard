# Limited-Access Test Cases

Use separate browser profiles or private sessions for each identity. Do not validate access by impersonating assumptions from an administrator session.

## Board Contributor

| ID | Test | Expected result |
|---|---|---|
| AC-01 | Open the private test project | Project opens |
| AC-02 | Open Boards | Board is visible |
| AC-03 | Create a PBI | Allowed |
| AC-04 | Move a card with all required fields | Allowed |
| AC-05 | Move a card with missing mandatory fields | Blocked |
| AC-06 | Open Organization settings → Process | Cannot edit process |
| AC-07 | Attempt to bypass work-item rules | Not permitted |
| AC-08 | Open Repos | Unavailable/no access |
| AC-09 | Open another private project | Unavailable unless separately granted |

## Board Reader

| ID | Test | Expected result |
|---|---|---|
| AC-10 | Open Boards | Board is visible |
| AC-11 | Open a work item | Work item is visible |
| AC-12 | Edit Title or Description | Not allowed |
| AC-13 | Move a card | Not allowed |
| AC-14 | Create a work item | Not allowed |
| AC-15 | Open Project settings | No administrative access |

## Authorized Approver

| ID | Test | Expected result |
|---|---|---|
| AC-16 | Edit Approved By | Allowed |
| AC-17 | Approve Priority-1 PBI | Allowed when other requirements pass |
| AC-18 | Modify process settings | Not allowed unless separately assigned process-admin permission |

## Review effective permissions

For unexpected access:

1. Open **Project settings → Permissions**.
2. Select the test user.
3. Review direct and inherited group membership.
4. Look for explicit Deny.
5. Verify Stakeholder/Basic access level.
6. Sign out and sign in again after changing membership.

