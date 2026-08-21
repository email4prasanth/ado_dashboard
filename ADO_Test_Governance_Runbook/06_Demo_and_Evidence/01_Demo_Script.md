# Demonstration Script for Deva

Target duration: 15 to 20 minutes.

## 1. Prove isolation

1. Show the organization name `inlai-projects`.
2. Show the private project `Scrum-Governance-Test`.
3. Show **Scrum Hybrid Governance → Projects**.
4. Confirm only the test project uses the process.
5. Explain that no production repository, pipeline, release, or environment was touched.

## 2. Show the hierarchy

Create or open:

```text
Epic: Governance Demonstration
Feature: Controlled Work Item Delivery
PBI: Implement Mandatory Bug Information
Task: Configure Bug Rule
```

Show the parent-child relationships.

## 3. Show controlled PBI workflow

1. Attempt to move the PBI to Approved with missing fields.
2. Show Azure DevOps blocking the change.
3. Fill Description, Acceptance Criteria, Priority, and Effort.
4. Move to Approved successfully.
5. Attempt In Progress without Assigned To.
6. Assign an owner and move successfully.
7. Move to On Hold and demonstrate mandatory Governance Reason.

## 4. Show Bug governance

1. Create a test Bug.
2. Attempt to move it to In Progress.
3. Show Repro Steps, Platform, Priority, and Assigned To becoming mandatory.
4. Complete the fields and move it successfully.
5. Show Resolution Notes required for Done.

## 5. Show senior approval

1. Open a Priority-1 PBI as an ordinary contributor.
2. Show that Approved By is read-only.
3. Open it as an Authorized Approver.
4. Record approval.
5. Complete the controlled transition.

## 6. Show limited access

1. Sign in as the Board Contributor.
2. Show that the user can update Boards but cannot administer the process or access Repos.
3. Sign in as the Board Reader.
4. Show that the user can view but cannot edit/move work items.

## 7. Request decisions

Ask Deva to confirm:

- Final workflow states.
- Mandatory fields for each transition.
- Who belongs to Authorized Approvers.
- Whether Bugs should be managed with PBIs or Tasks.
- Whether Test Plans/Test Cases are in the next phase.
- Named business owner and ADO system administrator.

Do not propose production rollout until these decisions and test evidence are approved.

