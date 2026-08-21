## Test Case Workflow – Minimal Enterprise Set
```sh
State: New
  Reasons:
  - Created
  - Under Review

State: In Progress
  Reasons:
  - Execution Started
  - Retesting

State: On Hold
  Reasons:
  - Awaiting Build
  - Environment Issue

State: Completed
  Reasons:
  - Passed
  - Failed
  - Blocked

State: Removed
  Reasons:
  - Obsolete
  - Duplicate
```
### State Transition Mapping
```sh
New
 ├─> In Progress  (Execution Started)
 └─> Removed      (Obsolete, Duplicate)

In Progress
 ├─> On Hold      (Awaiting Build, Environment Issue)
 ├─> Completed    (Passed, Failed, Blocked)
 └─> Removed      (Obsolete)

On Hold
 ├─> In Progress  (Resumed)
 └─> Removed      (Obsolete)
```