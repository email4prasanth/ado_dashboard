# State Mapping Table user stories
```sh
- Proposed
    - New 
    - Refinement 

- In Progress
    - Ready for Dev 
    - Dev In Progress 
    - Code Review 
    - Ready for QA 
    - QA In Progress 

- Resolved
    - On Hold
    - Ready for Pre-prod 
    - Ready for Prod 

- Done
    - Closed 

- Removed
    - Reopened  *
    - Rejected  *
    - Deferred  *
```
- under layout click new filed -- 
creat new field 
### Reopen Reason
```text
Name: Reopen Reason
Type: single line (text)
Required: No
Default: None
Values: Pending, Approved, Rejected
```
or use exisitng field Reopen Reason and add.

### Rejection Reason
```text
Name: Rejection Reason
Type: single line (text)
Required: No
Default: None
Values: Pending, Approved, Rejected
```
or use exisitng field Rejection Reason and add

### Deferral Reason
```text
Name: Deferral Reason
Type: single line (text)
Required: No
Default: None
Values: Pending, Approved, Rejected
```
or use exisitng field Deferral Reason and add
###  Rules

- Click "New Rule".
- "Restrict From New State".
- Under When, select "A work item state moved from..." and choose New.
- Under Then, select "Restrict the state transition to..." and choose all the states that should NOT be allowed from "New".
- In your case, you would restrict transitions to all states except Ready for Dev, Dev In Progress, Code Review, Ready for QA, and QA In Progress.
- Click "Save".

### 

## USER STORY - Restrict State Transition Rules
```sh

1. **Restrict From New**
   - Under When, select "A work item state moved from..." and choose Ready for New
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except Refinement, Ready for Dev, Dev In Progress, Code Review, Ready for QA, QA In Progress, Removed only

2. **Restrict From Refinement**
   - Under When, select "A work item state moved from..." and choose Ready for Refinement
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except Ready for Dev, Removed (Rejected, Deferred) only

3. **Restrict From Ready for Dev**
   - Under When, select "A work item state moved from..." and choose Ready for Ready for Dev
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except Dev In Progress, Refinement, On Hold, Removed only

4. **Restrict From Dev In Progress**
   - Under When, select "A work item state moved from..." and choose Ready for Dev In Progress
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except Code Review, Ready for Dev, On Hold, Removed only

5. **Restrict From Code Review**
   - Under When, select "A work item state moved from..." and choose Ready for Code Review
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except Ready for QA, Dev In Progress, On Hold, Removed only

6. **Restrict From Ready for QA**
   - Under When, select "A work item state moved from..." and choose Ready for Ready for QA
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except QA In Progress, Code Review, On Hold, Removed only

7. **Restrict From QA In Progress**
   - Under When, select "A work item state moved from..." and choose Ready for QA In Progress
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except Ready for Pre-prod, Ready for QA, Code Review, Removed only

8. **Restrict From On Hold**
   - Under When, select "A work item state moved from..." and choose Ready for On Hold
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except Ready for Dev, Dev In Progress, Code Review, Ready for QA, QA In Progress, Removed only

9. **Restrict From Ready for Pre-prod**
   - Under When, select "A work item state moved from..." and choose Ready for Ready for Pre-prod
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except Ready for Prod, QA In Progress, On Hold, Removed only

10. **Restrict From Ready for Prod**
    - Under When, select "A work item state moved from..." and choose Ready for Ready for Prod
    - Under Then, select "Restrict the state transition to..." should NOT be allowed except Closed, Ready for Pre-prod, On Hold, Removed only

11. **Restrict From Closed**
    - Under When, select "A work item state moved from..." and choose Ready for Closed
    - Under Then, select "Restrict the state transition to..." should NOT be allowed except Reopened, Removed, Rejected, Deferred only

12. **Restrict From Reopened**
    - Under When, select "A work item state moved from..." and choose Ready for Reopened
    - Under Then, select "Restrict the state transition to..." should NOT be allowed except Ready for Dev, Dev In Progress, Code Review, Ready for QA, QA In Progress, Closed only

13. **Restrict From Rejected**
    - Under When, select "A work item state moved from..." and choose Ready for Rejected
    - Under Then, select "Restrict the state transition to..." should NOT be allowed except New, Refinement, Removed, Deferred only

14. **Restrict From Deferred**
    - Under When, select "A work item state moved from..." and choose Ready for Deferred
    - Under Then, select "Restrict the state transition to..." should NOT be allowed except Refinement, Ready for Dev, New, Removed only
```
## USER STORY - Field Validation Rules
```sh
15. **Require Description for New**
    - A work item state changes to ..  State is New
    - Make required ... - Description

16. **Require Rules for Refinement**
    - A work item state changes to ..  State is Refinement
    - Make required ... -Description, Acceptance Criteria, Priority

17. **Require Rules for Ready for Dev**
    - A work item state changes to ..  State is Ready for Dev
    - Then: 
      - Make required ... - Assigned To, Priority, Effort
      - Make read only ... SeniorApprovedBy, Senior Approval Status

18. **Require Rules for Dev In Progress**
    - A work item state changes to ..  State is Dev In Progress
    - Then:
      - Clear the value of ...  Priority, Time Criticality
      - Make required ... - Priority, Time Criticality, Effort
      - Make read only ... Assigned To, SeniorApprovedBy, Senior Approval Status, Business Value

19. **Require Rules for Code Review**
    - A work item state changes to ..  State is Code Review
    - Then:
      - Make required ... - Description, Acceptance Criteria, Priority
      - Make read only ... Assigned To, Effort, Time Criticality, SeniorApprovedBy, Senior Approval Status

20. **Require Rules for Ready for QA**
    - A work item state changes to ..  State is Ready for QA
    - Then:
      - Make required ... - Acceptance Criteria, Priority
      - Make read only ... Assigned To, Effort, Time Criticality, SeniorApprovedBy, Senior Approval Status

21. **Require Rules for QA In Progress**
    - A work item state changes to ..  State is QA In Progress
    - Then:
      - Make required ... - Description, Acceptance Criteria, Priority
      - Make read only ... Assigned To, Effort, Time Criticality, SeniorApprovedBy, Senior Approval Status

22. **Require Reason for On Hold**
    - A work item state changes to ..  State is On Hold
    - Then:
      - Clear the value of ...  Description
      - Make required ... - Description
      - Make read only ... Assigned To, Priority, Time Criticality, SeniorApprovedBy, Senior Approval Status, Effort, Value Area, Business Value

23. **Require Rules for Ready for Pre-prod**
    - A work item state changes to ..  State is Ready for Pre-prod
    - Then:
      - Make required ... - Description, Acceptance Criteria, Priority
      - Make read only ... Assigned To, Effort, Time Criticality, SeniorApprovedBy, Senior Approval Status, Business Value

24. **Require Rules for Ready for Prod**
    - A work item state changes to ..  State is Ready for Prod
    - Then:
      - Make required ... - Description, Acceptance Criteria, Release Notes
      - Make read only ... Assigned To, Priority, Effort, Time Criticality, SeniorApprovedBy, Senior Approval Status, Business Value, Value Area

25. **Require Rules for Closed**
    - A work item state changes to ..  State is Closed
    - Then:
      - Make required ... - Acceptance Criteria
      - Clear the value of ...  Description
      - Make required ... - Description
      - Make read only ... Assigned To, Priority, Effort, Business Value, Time Criticality, Value Area, SeniorApprovedBy, Senior Approval Status

26. **Require Rules for Reopened**
    - A work item state changes to ..  State is Reopened
    - Then:
      - Clear the value of ...  Description
      - Make required ... - Description, Priority, Reopen Reason
      - Make read only ... Assigned To, Effort, Time Criticality, SeniorApprovedBy, Senior Approval Status, Business Value

27. **Require Reason for Rejected**
    - A work item state changes to ..  State is Rejected
    - Then:
      - Clear the value of ...  Description
      - Make required ... - Description, Rejection Reason
      - Make read only ... Assigned To, Priority, Effort, Business Value, Time Criticality, Value Area, SeniorApprovedBy, Senior Approval Status

28. **Require Reason for Deferred**
    - A work item state changes to ..  State is Deferred
    - Then:
      - Clear the value of ...  Description
      - Make required ... - Description, Deferral Reason, Priority
      - Make read only ... Assigned To, Effort, Business Value, Time Criticality, Value Area, SeniorApprovedBy, Senior Approval Status
```
