## QA Task Workflow – Minimal Enterprise Set
```sh
State: New
  Reasons:
  - Created
  - Assigned

State: In Progress
  Reasons:
  - Testing Started
  - Retesting

State: On Hold
  Reasons:
  - Awaiting Build
  - Environment Issue
  - Dependency Blocked

State: Completed
  Reasons:
  - Testing Completed

State: Removed
  Reasons:
  - Cancelled
  - Duplicate
```
### State Transition Mapping
```sh
New
 ├─> In Progress  (Testing Started)
 └─> Removed      (Cancelled)

In Progress
 ├─> On Hold      (Awaiting Build, Environment Issue)
 ├─> Completed    (Testing Completed)
 └─> Removed      (Cancelled)

On Hold
 ├─> In Progress  (Resumed)
 └─> Removed      (Cancelled)
```