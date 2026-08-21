State Mapping Table for TASKS
```sh
- Proposed
    - New 
    - To Do 
    
- In Progress
    - In Progress 
    
- Resolved
    - On Hold 
    
- Done
    - Done 
    
- Removed
    - Removed 
```
## TASKS - Restrict State Transition Rules

1. **Restrict From New**
   - Under When, select "A work item state moved from..." and choose Ready for  New
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except  To Do, Removed

2. **Restrict From To Do**
   - Under When, select "A work item state moved from..." and choose Ready for  To Do
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except  In Progress, Removed

3. **Restrict From In Progress**
   - Under When, select "A work item state moved from..." and choose Ready for  In Progress
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except  On Hold, Done, Removed

4. **Restrict From On Hold**
   - Under When, select "A work item state moved from..." and choose Ready for  On Hold
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except  In Progress, Removed

5. **Restrict From Done**
   - Under When, select "A work item state moved from..." and choose Ready for  Done
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except  (No transitions - End State)

6. **Restrict From Removed**
   - Under When, select "A work item state moved from..." and choose Ready for  Removed
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except  (No transitions - End State)


- under layout click new filed -- 
creat new field 
### Senior Approval Status
```text
Name: Senior Approval Status
Type: Picklist (string)
Required: No
Default: None
Values: Pending, To Do, Rejected
```
or use exisitng field Senior Approval Status and add.

### SeniorTo DoBy
```text
Name: SeniorTo DoBy
Type: Identity
Required: No
Default: None
Values: No
```
or use exisitng field SeniorTo DoBy and add.

## TASKS - Field Validation Rules
7. **Require Description for New**
   - A work item state changes to ..  State is New
   - Make required ... Description

8. **Require Business Case for To Do**
   - A work item state changes to ..  State is To Do
   - Make required ... Assigned To
   - Make required ... SeniorTo DoBy
   - Make required ... Senior Approval Status

9. **Require Rules for In Progress**
   - A work item state changes to ..  State is In Progress
   - Clear the value of ... Priority
   - Make required ... Priority
   - Clear the value of ... Time Criticality
   - Make required ... Time Criticality
   - Make required ... Effort
   - Make read only ... Assigned To
   - Make read only ... SeniorTo DoBy
   - Make read only ... Senior Approval Status
   - Make read only ... Business Value
   
10. **Require Reason for On Hold**
   - A work item state changes to ..  State is On Hold
   - Clear the value of ... Description
   - Make required ... Description
   - Make read only ... Assigned To
   - Make read only ... Priority
   - Make read only ... Time Criticality
   - Make read only ... SeniorTo DoBy
   - Make read only ... Senior Approval Status
   - Make read only ... Effort
   - Make read only ... Value Area
   - Make read only ... Business Value

11. **Require Rules for Done**
   - A work item state changes to ..  State is In Done
   - Make required ... Acceptance Criteria
   - Clear the value of ... Description
   - Make required ... Description
   - Make read only ... Assigned To
   - Make read only ... Priority
   - Make read only ... Effort
   - Make read only ... Business Value
   - Make read only ... Time Criticality
   - Make read only ... Value Area
   - Make read only ... SeniorTo DoBy
   - Make read only ... Senior Approval Status


12. **Require Reason for Removed**
   - A work item state changes to ..  State is Removed
   - Clear the value of ... Description
   - Make required ... Description
   - Make read only ... Assigned To
   - Make read only ... Priority
   - Make read only ... Effort
   - Make read only ... Business Value
   - Make read only ... Time Criticality
   - Make read only ... Value Area
   - Make read only ... SeniorTo DoBy
   - Make read only ... Senior Approval Status