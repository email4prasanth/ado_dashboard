## User Story Workflow – Minimal Enterprise Set
```sh
State: New
  Reasons:
  - Approved for Delivery
  - Rejected
  - Duplicate

State: Approved
  Reasons:
  - Work Started
  - Awaiting Dependency
  - Cancelled

State: In Progress
  Reasons:
  - Development Started
  - Ready for Testing
  - Blocked
  - Cancelled

State: On Hold
  Reasons:
  - Awaiting Dependency
  - Resource Constraint
  - Business Decision
  - Resumed
  - Cancelled

State: Completed
  Reasons:
  - Accepted
  - Delivered

State: Removed
  Reasons:
  - Rejected
  - Duplicate
  - Cancelled
  - Out of Scope
```
### State Transition Mapping
```sh
New
 ├─> Approved     (Approved for Delivery)
 └─> Removed      (Rejected, Duplicate)

Approved
 ├─> In Progress  (Work Started)
 ├─> On Hold      (Awaiting Dependency)
 └─> Removed      (Cancelled)

In Progress
 ├─> On Hold      (Blocked)
 ├─> Completed    (Accepted, Delivered)
 └─> Removed      (Cancelled)

On Hold
 ├─> In Progress  (Resumed)
 └─> Removed      (Cancelled)
```