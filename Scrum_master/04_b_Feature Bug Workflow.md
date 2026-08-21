## Feature Bug Workflow – Minimal Enterprise Set
```sh
State: New
  Reasons:
  - Reported
  - Identified During Testing
  - Production Defect
  - Duplicate

State: Approved
  Reasons:
  - Accepted for Fix
  - Prioritized

State: In Progress
  Reasons:
  - Fix Development Started
  - Root Cause Analysis

State: On Hold
  Reasons:
  - Awaiting Dependency
  - Deferred
  - Business Decision

State: Completed
  Reasons:
  - Fixed
  - Verified

State: Removed
  Reasons:
  - Duplicate
  - Not Reproducible
  - Won't Fix
  - Rejected
```
### State Transition Mapping
```sh
New
 ├─> Approved     (Accepted for Fix)
 └─> Removed      (Duplicate, Rejected)

Approved
 ├─> In Progress  (Fix Development Started)
 ├─> On Hold      (Deferred)
 └─> Removed      (Won't Fix)

In Progress
 ├─> On Hold      (Awaiting Dependency)
 ├─> Completed    (Fixed, Verified)
 └─> Removed      (Won't Fix)

On Hold
 ├─> In Progress  (Resumed)
 └─> Removed      (Won't Fix)
```