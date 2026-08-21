# Feature High-Level Rules

# State Mapping Table for FEATURE
```sh
- Proposed
    - New 
    - Approved 
    
- In Progress
    - In Progress 
    
- Resolved
    - On Hold 
    - Ready for Release 
    
- Completed
    - Completed
    
- Removed
    - Removed 
```

## FEATURE - Restrict State Transition Rules

1. **Restrict From New**
   - Under When, select "A work item state moved from..." and choose Ready for New
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except Approved, Removed only

2. **Restrict From Approved**
   - Under When, select "A work item state moved from..." and choose Ready for Approved
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except In Progress, Removed only

3. **Restrict From In Progress**
   - Under When, select "A work item state moved from..." and choose Ready for In Progress
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except On Hold, Ready for Release, Completed, Removed only

4. **Restrict From On Hold**
   - Under When, select "A work item state moved from..." and choose Ready for On Hold
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except In Progress, Removed only

5. **Restrict From Ready for Release**
   - Under When, select "A work item state moved from..." and choose Ready for Ready for Release
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except Completed, Removed only

6. **Restrict From Completed**
   - Under When, select "A work item state moved from..." and choose Ready for Completed
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except No transitions (End State)

7. **Restrict From Removed**
   - Under When, select "A work item state moved from..." and choose Ready for Removed
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except No transitions (End State)

## FEATURE - Field Validation Rules

8. **Require Description for New**
   - A work item state changes to ..  State is New
   - Make required ... - Description

9. **Require Business Case for Approved**
   - A work item state changes to ..  State is Approved
   - Make required ... -Assigned To, SeniorApprovedBy, Senior Approval Status, Business Value

10. **Require Rules for In Progress**
    - A work item state changes to ..  State is In Progress
    - Then: 
      - Clear the value of ...  Priority, Time Criticality
      - Make required ... - Priority, Time Criticality, Effort
      - Make read only ... Assigned To, SeniorApprovedBy, Senior Approval Status, Business Value

11. **Require Reason for On Hold**
    - A work item state changes to ..  State is On Hold
    - Then:
      - Clear the value of ...  Description
      - Make required ... - Description
      - Make read only ... Assigned To, Priority, Time Criticality, SeniorApprovedBy, Senior Approval Status, Effort, Value Area, Business Value

12. **Require Rules for Ready for Release**
    - A work item state changes to ..  State is Ready for Release
    - Then:
      - Clear the value of ...  Description
      - Make required ... - Description, Acceptance Criteria, Release Notes
      - Make read only ... Assigned To, Priority, Effort, Business Value, Time Criticality, Value Area, SeniorApprovedBy, Senior Approval Status

13. **Require Rules for Completed**
    - A work item state changes to ..  State is Completed
    - Then:
      - Make required ... - Acceptance Criteria
      - Clear the value of ...  Description
      - Make required ... - Description
      - Make read only ... Assigned To, Priority, Effort, Business Value, Time Criticality, Value Area, SeniorApprovedBy, Senior Approval Status

14. **Require Reason for Removed**
    - A work item state changes to ..  State is Removed
    - Then:
      - Clear the value of ...  Description
      - Make required ... - Description, Removal Reason
      - Make read only ... Assigned To, Priority, Effort, Business Value, Time Criticality, Value Area, SeniorApprovedBy, Senior Approval Status