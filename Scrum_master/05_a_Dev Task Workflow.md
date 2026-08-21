## Task Workflow – Minimal Enterprise Set
```sh
State: New
  Reasons:
  - Created
  - Assigned

State: In Progress
  Reasons:
  - Development Started
  - Rework Required

State: On Hold
  Reasons:
  - Awaiting Dependency
  - Blocked
  - Resource Constraint

State: Completed
  Reasons:
  - Development Completed

State: Removed
  Reasons:
  - Cancelled
  - Duplicate
  - Out of Scope
```
### State Transition Mapping
```sh
New
 ├─> In Progress  (Development Started)
 └─> Removed      (Cancelled, Duplicate)

In Progress
 ├─> On Hold      (Blocked, Awaiting Dependency)
 ├─> Completed    (Development Completed)
 └─> Removed      (Cancelled)

On Hold
 ├─> In Progress  (Resumed)
 └─> Removed      (Cancelled)
```