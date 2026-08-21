## Test Plan Workflow – Minimal Enterprise Set
```sh
State: New
  Reasons:
  - Created
  - Under Review

State: Approved
  Reasons:
  - Approved for Execution

State: In Progress
  Reasons:
  - Test Execution Started
  - Test Cases Assigned

State: On Hold
  Reasons:
  - Awaiting Build
  - Environment Unavailable
  - Dependency Blocked

State: Completed
  Reasons:
  - Execution Completed
  - Sign-Off Obtained

State: Removed
  Reasons:
  - Obsolete
  - Duplicate
  - Cancelled
```
### State Transition Mapping
```sh
New
 ├─> Approved     (Approved for Execution)
 └─> Removed      (Obsolete, Duplicate)

Approved
 ├─> In Progress  (Test Execution Started)
 ├─> On Hold      (Awaiting Build)
 └─> Removed      (Cancelled)

In Progress
 ├─> On Hold      (Environment Unavailable)
 ├─> Completed    (Execution Completed)
 └─> Removed      (Cancelled)

On Hold
 ├─> In Progress  (Resumed)
 └─> Removed      (Cancelled)
```