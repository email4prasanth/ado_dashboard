# State Mapping Table bugs
```sh
- Proposed
    - New 
    - Triaged 
    
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
    
- Completed
    - Closed 
    
- Removed
    - Reopened  *
    - Rejected  *
    - Duplicate  *
    - Deferred  *
```
## BUG - Restrict State Transition Rules
```sh
1. **Restrict From New**
   - Under When, select "A work item state moved from..." and choose Ready for New
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except 'Triaged', 'Ready for Dev', 'Dev In Progress', 'Code Review', 'Ready for QA', 'QA In Progress', 'Removed', 'On Hold', 'Closed' only

2. **Restrict From Triaged**
   - Under When, select "A work item state moved from..." and choose Ready for Triaged
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except 'Ready for Dev', 'Dev In Progress', 'On Hold', 'Removed', 'Rejected', 'Deferred', 'Closed', 'Reopened'

3. **Restrict From Ready for Dev**
   - Under When, select "A work item state moved from..." and choose Ready for Ready for Dev
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except 'Dev In Progress', 'Triaged', 'On Hold', 'Removed', 'Duplicate', 'Closed', 'Rejected', 'Code Review', 'Reopened'

4. **Restrict From Dev In Progress**
   - Under When, select "A work item state moved from..." and choose Ready for Dev In Progress
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except 'Code Review', 'Ready for Dev', 'Ready for QA', 'On Hold', 'Removed', 'Duplicate', 'Closed', 'Rejected'

5. **Restrict From Code Review**
   - Under When, select "A work item state moved from..." and choose Ready for Code Review
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except 'Ready for QA', 'Dev In Progress', 'On Hold', 'Removed', 'Duplicate', 'Closed', 'Rejected', 'Reopened'

6. **Restrict From Ready for QA**
   - Under When, select "A work item state moved from..." and choose Ready for Ready for QA
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except 'QA In Progress', 'Code Review', 'On Hold', 'Removed', 'Duplicate', 'Closed', 'Rejected', 'Reopened'

7. **Restrict From QA In Progress**
   - Under When, select "A work item state moved from..." and choose Ready for QA In Progress
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except Ready for 'Ready for Pre-prod', 'Ready for QA', 'Code Review', 'Removed', 'Duplicate', 'Closed', 'Rejected', 'Reopened'

8. **Restrict From On Hold**
   - Under When, select "A work item state moved from..." and choose Ready for On Hold
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except 'Ready for Dev', 'Dev In Progress', 'Code Review', 'Ready for QA', 'QA In Progress', 'Removed', 'Duplicate', 'Closed', 'Rejected'

9. **Restrict From Ready for Pre-prod**
   - Under When, select "A work item state moved from..." and choose Ready for Ready for Pre-prod
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except 'Ready for Prod', 'Code Review', 'QA In Progress', 'On Hold', 'Removed', 'Duplicate', 'Closed', 'Rejected'

10. **Restrict From Ready for Prod**
    - Under When, select "A work item state moved from..." and choose Ready for Ready for Prod
    - Under Then, select "Restrict the state transition to..." should NOT be allowed except 'Triaged','Closed', 'Ready for Pre-prod', 'On Hold', 'Removed', 'Duplicate', 'Closed', 'Rejected', 'Reopened'

11. **Restrict From Closed**
    - Under When, select "A work item state moved from..." and choose Ready for Closed
    - Under Then, select "Restrict the state transition to..." should NOT be allowed except 'New', 'Triaged','Reopened', 'Removed', 'Rejected', 'Deferred', 'Duplicate', 'Closed', 'Rejected', 'Ready for Pre-prod'

12. **Restrict From Reopened**
    - Under When, select "A work item state moved from..." and choose Ready for Reopened
    - Under Then, select "Restrict the state transition to..." should NOT be allowed except 'Ready for Dev', 'Dev In Progress', 'Code Review', 'Ready for QA', 'QA In Progress', 'Closed', 'Duplicate', 'Rejected'

13. **Restrict From Rejected**
    - Under When, select "A work item state moved from..." and choose Ready for Rejected
    - Under Then, select "Restrict the state transition to..." should NOT be allowed except 'New', 'Triaged', 'Removed', 'Deferred', 'Duplicate', 'Closed', 'Reopened', 'Ready for Pre-prod'

14. **Restrict From Deferred**
    - Under When, select "A work item state moved from..." and choose Ready for Deferred
    - Under Then, select "Restrict the state transition to..." should NOT be allowed except 'New', 'Triaged', 'Ready for Dev', 'New', 'Removed', 'Duplicate', 'Closed', 'Rejected', 'Ready for Pre-prod'

```
## BUG - Field Validation Rules
```sh
16. **Require Description for New**
    - A work item state changes to ..  State is New
    - Make required ... - Description

17. **Require Rules for Triaged**
    - A work item state changes to ..  State is Triaged
    - Make required ... -Description, Priority, Severity, Assigned To

18. **Require Rules for Ready for Dev**
    - A work item state changes to ..  State is Ready for Dev
    - Then:
      - Make required ... - Assigned To, Priority, Severity, Effort
      - Make read only ... SeniorApprovedBy, Senior Approval Status

19. **Require Rules for Dev In Progress**
    - A work item state changes to ..  State is Dev In Progress
    - Then:
      - Clear the value of ...  Priority, Time Criticality
      - Make required ... - Priority, Time Criticality, Effort
      - Make read only ... Assigned To, SeniorApprovedBy, Senior Approval Status, Business Value

20. **Require Rules for Code Review**
    - A work item state changes to ..  State is Code Review
    - Then:
      - Make required ... - Description, Priority, Severity
      - Make read only ... Assigned To, Effort
```