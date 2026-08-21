# Functional Test Cases

Use a fresh work item for each test. Record the work item ID, actual result, and screenshot.

| ID | Test | Expected result |
|---|---|---|
| FT-01 | Move a PBI to Approved without Description | Save/move is blocked |
| FT-02 | Add Description but omit Acceptance Criteria | Save/move is blocked |
| FT-03 | Complete Description, Acceptance Criteria, Priority and Effort | PBI can enter Approved |
| FT-04 | Move PBI to In Progress without Assigned To | Save/move is blocked |
| FT-05 | Complete Assigned To | PBI can enter In Progress |
| FT-06 | Move PBI to On Hold without Governance Reason | Save/move is blocked |
| FT-07 | Set Governance Reason | PBI can enter On Hold |
| FT-08 | Move Priority-1 PBI to In Progress without approval | Save/move is blocked |
| FT-09 | Ordinary contributor attempts to edit Approved By | Field is read-only |
| FT-10 | Authorized approver records approval | Approval can be saved |
| FT-11 | Move Task to In Progress without Activity | Save/move is blocked |
| FT-12 | Complete Task execution fields | Task can enter In Progress |
| FT-13 | Move Task to Done | Remaining Work becomes 0 if TASK-02 is enabled |
| FT-14 | Move Bug to In Progress without Repro Steps | Save/move is blocked |
| FT-15 | Add Repro Steps but omit Platform | Save/move is blocked |
| FT-16 | Complete all required Bug fields | Bug can enter In Progress |
| FT-17 | Move Bug to Done without Resolution Notes | Save/move is blocked |
| FT-18 | Attempt a prohibited state transition | Destination is unavailable or transition is rejected |

## Rule test method

Test each applicable case twice:

1. Change State from the work item form.
2. Drag the card to the destination board column.

## Evidence table

| Test ID | Work item ID | Pass/Fail | Evidence filename | Notes |
|---|---:|---|---|---|
| | | | | |

