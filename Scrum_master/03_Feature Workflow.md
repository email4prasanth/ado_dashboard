## Feature Workflow – Minimal Enterprise Set
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
  - Resource Constraint
  - Business Decision
  - Ready for Validation
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
  - Delivered
  - Accepted

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
 ├─> On Hold      (Resource Constraint, Business Decision)
 ├─> Completed    (Delivered, Accepted)
 └─> Removed      (Cancelled)

On Hold
 ├─> In Progress  (Resumed)
 └─> Removed      (Cancelled)
```