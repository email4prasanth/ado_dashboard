State Mapping Table for EPIC
```sh
- Proposed
    - New (default)
    - Approved (default)
    
- In Progress
    - In Progress (default)
    
- Resolved
    - On Hold (default)
    
- Done
    - Done (default)
    
- Removed
    - Removed (default)
```
## EPIC - Restrict State Transition Rules

1. **Restrict From New**
   - Under When, select "A work item state moved from..." and choose Ready for  New
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except  Approved, Removed

2. **Restrict From Approved**
   - Under When, select "A work item state moved from..." and choose Ready for  Approved
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
Values: Pending, Approved, Rejected
```
or use exisitng field Senior Approval Status and add.

### SeniorApprovedBy
```text
Name: SeniorApprovedBy
Type: Identity
Required: No
Default: None
Values: No
```
or use exisitng field SeniorApprovedBy and add.

## EPIC - Field Validation Rules
7. **Require Description for New**
   - A work item state changes to ..  State is New
   - Make required ... Description

8. **Require Business Case for Approved**
   - A work item state changes to ..  State is Approved
   - Make required ... Assigned To
   - Make required ... SeniorApprovedBy
   - Make required ... Senior Approval Status

9. **Require Rules for In Progress**
   - A work item state changes to ..  State is In Progress
   - Clear the value of ... Priority
   - Make required ... Priority
   - Clear the value of ... Time Criticality
   - Make required ... Time Criticality
   - Make required ... Effort
   - Make read only ... Assigned To
   - Make read only ... SeniorApprovedBy
   - Make read only ... Senior Approval Status
   - Make read only ... Business Value
   
10. **Require Reason for On Hold**
   - A work item state changes to ..  State is On Hold
   - Clear the value of ... Description
   - Make required ... Description
   - Make read only ... Assigned To
   - Make read only ... Priority
   - Make read only ... Time Criticality
   - Make read only ... SeniorApprovedBy
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
   - Make read only ... SeniorApprovedBy
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
   - Make read only ... SeniorApprovedBy
   - Make read only ... Senior Approval Status