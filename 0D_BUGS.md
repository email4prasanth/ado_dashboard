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
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except Triaged, Ready for Dev, Dev In Progress, Code Review, Ready for QA, QA In Progress, Removed only

2. **Restrict From Triaged**
   - Under When, select "A work item state moved from..." and choose Ready for Triaged
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except Ready for Dev, Removed (Rejected, Duplicate, Deferred) only

3. **Restrict From Ready for Dev**
   - Under When, select "A work item state moved from..." and choose Ready for Ready for Dev
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except Dev In Progress, Triaged, Removed only

4. **Restrict From Dev In Progress**
   - Under When, select "A work item state moved from..." and choose Ready for Dev In Progress
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except Code Review, Ready for Dev, Removed only

5. **Restrict From Code Review**
   - Under When, select "A work item state moved from..." and choose Ready for Code Review
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except Ready for QA, Dev In Progress, Removed only

6. **Restrict From Ready for QA**
   - Under When, select "A work item state moved from..." and choose Ready for Ready for QA
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except QA In Progress, Code Review, Removed only

7. **Restrict From QA In Progress**
   - Under When, select "A work item state moved from..." and choose Ready for QA In Progress
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except Ready for Pre-prod, Ready for QA, Code Review, Removed only

8. **Restrict From On Hold**
   - Under When, select "A work item state moved from..." and choose Ready for On Hold
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except Ready for Dev, Dev In Progress, Code Review, Ready for QA, QA In Progress, Removed only

9. **Restrict From Ready for Pre-prod**
   - Under When, select "A work item state moved from..." and choose Ready for Ready for Pre-prod
   - Under Then, select "Restrict the state transition to..." should NOT be allowed except Ready for Prod, QA In Progress, Removed only

10. **Restrict From Ready for Prod**
    - Under When, select "A work item state moved from..." and choose Ready for Ready for Prod
    - Under Then, select "Restrict the state transition to..." should NOT be allowed except Closed, Ready for Pre-prod, Removed only

11. **Restrict From Closed**
    - Under When, select "A work item state moved from..." and choose Ready for Closed
    - Under Then, select "Restrict the state transition to..." should NOT be allowed except Reopened only

12. **Restrict From Reopened**
    - Under When, select "A work item state moved from..." and choose Ready for Reopened
    - Under Then, select "Restrict the state transition to..." should NOT be allowed except Ready for Dev, Dev In Progress, Code Review, Ready for QA, QA In Progress, Closed only

13. **Restrict From Rejected**
    - Under When, select "A work item state moved from..." and choose Ready for Rejected
    - Under Then, select "Restrict the state transition to..." should NOT be allowed except New, Triaged only

14. **Restrict From Duplicate**
    - Under When, select "A work item state moved from..." and choose Ready for Duplicate
    - Under Then, select "Restrict the state transition to..." should NOT be allowed except New, Triaged only

15. **Restrict From Deferred**
    - Under When, select "A work item state moved from..." and choose Ready for Deferred
    - Under Then, select "Restrict the state transition to..." should NOT be allowed except Triaged, Ready for Dev only

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