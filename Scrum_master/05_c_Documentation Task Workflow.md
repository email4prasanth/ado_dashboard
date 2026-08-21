## Documentation Task Workflow – Minimal Enterprise Set
```sh
State: New
  Reasons:
  - Created
  - Assigned

State: In Progress
  Reasons:
  - Documentation Started
  - Update Required

State: On Hold
  Reasons:
  - Awaiting Information
  - Dependency Blocked

State: Completed
  Reasons:
  - Documentation Completed

State: Removed
  Reasons:
  - Cancelled
  - Duplicate
```
### State Transition Mapping
```sh
New
 ├─> In Progress  (Documentation Started)
 └─> Removed      (Cancelled)

In Progress
 ├─> On Hold      (Awaiting Information)
 ├─> Completed    (Documentation Completed)
 └─> Removed      (Cancelled)

On Hold
 ├─> In Progress  (Resumed)
 └─> Removed      (Cancelled)
```