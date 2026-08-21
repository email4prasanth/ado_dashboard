## Epic Workflow – Minimal Enterprise Set
```sh
State: New (Category: Proposed)
  Reasons:
  - Proposed

State: Approved (Category: Proposed)
  Reasons:
  - Approved for Delivery

State: In Progress (Category: In Progress)
  Reasons:
  - Work Started
  - Resumed

State: On Hold (Category: Resolved)
  Reasons:
  - Awaiting Dependency
  - Resource Constraint
  - Business Decision

State: Done (Category: Completed)
  Reasons:
  - Delivered
  - Accepted

State: Removed (Category: Removed)
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
 ├─> Done         (Delivered, Accepted)
 └─> Removed      (Cancelled)

On Hold
 ├─> In Progress  (Resumed)
 └─> Removed      (Cancelled)
```
- create picklist
```sh
Organization Settings
 └─ Process
     └─ Scrum Hybrid Governance
        └─ Epic
             └─ Layout
Add a New Field
Name: State Reason
Type: Picklist (String)
# pick list values
Proposed
Approved for Delivery
Work Started
Resumed
Awaiting Dependency
Resource Constraint
Business Decision
Delivered
Accepted
Cancelled
Duplicate
Out of Scope
Rejected
Blocked
Deferred
Fixed
Verified
Testing Started
Testing Completed
Documentation Completed
```
| State       | Suggested Reasons                                           |
| ----------- | ----------------------------------------------------------- |
| New         | Proposed                                                    |
| Approved    | Approved for Delivery                                       |
| In Progress | Work Started, Resumed                                       |
| On Hold     | Awaiting Dependency, Resource Constraint, Business Decision |
| Done        | Delivered, Accepted                                         |
| Removed     | Cancelled, Duplicate, Out of Scope, Rejected                |

- options
```sh
Field Name: State Reason
Reference Name: Custom.StateReason

Required:
  No

Allow users to enter their own values:
  No

Default Value:
  Proposed
```