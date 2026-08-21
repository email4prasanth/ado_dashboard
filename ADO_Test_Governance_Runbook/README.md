# Scrum Hybrid Governance - Manual Azure DevOps Runbook

## Purpose

This runbook explains how to build and demonstrate Scrum Hybrid Governance manually in the **testing Azure DevOps organization**.

It does not require scripts, REST APIs, Azure CLI, repository access, or pipeline changes.

## Safety boundary

Use only the testing organization:

```text
Expected organization: inlai-projects
Expected test project: Scrum-Governance-Test
Expected process: Scrum Hybrid Governance
```

Do not make any change in the Azure DevOps organization containing the existing production process and 17 repositories.

Every procedure begins with an organization and project verification checkpoint. Stop if the displayed organization or project is different.

## Runbook order

Complete the folders in this order:

1. [00_Guardrails](./00_Guardrails/01_Preparation_and_Safety_Check.md)
2. [01_Test_Project_Setup](./01_Test_Project_Setup/01_Create_Inherited_Process.md)
3. [02_Process_Configuration](./02_Process_Configuration/01_Hierarchy_and_Work_Item_Types.md)
4. [03_Governance_Rules](./03_Governance_Rules/01_Create_Custom_Fields.md)
5. [04_Limited_Access](./04_Limited_Access/01_Create_Access_Groups.md)
6. [05_Validation](./05_Validation/01_Functional_Test_Cases.md)
7. [06_Demo_and_Evidence](./06_Demo_and_Evidence/01_Demo_Script.md)
8. [07_Production_Gate](./07_Production_Gate/01_Approval_and_Rollback.md)
9. [08_Official_References](./08_Official_References/01_Microsoft_Documentation.md)

Before reusing anything from the original `Rule` folder, read
[Existing Rule Folder Disposition](./00_Guardrails/02_Existing_Rule_Folder_Disposition.md).

## Target governance model

```text
Epic
└── Feature
    └── Product Backlog Item
        └── Task

Bug       = tracked on the product backlog or taskboard, based on team setting
Test Case = linked to a requirement/bug through testing links; not used as a normal hierarchy child
```

Target states:

```text
Epic / Feature / Product Backlog Item
New → Approved → In Progress → Done
                   ↕
                 On Hold
Any active state → Removed

Task / Bug
New → In Progress → Done
          ↕
        On Hold
Any active state → Removed
```

## Definition of success

The proof of concept is successful only when:

- The test process is used by the test project only.
- No production project, repository, pipeline, release, or environment is changed.
- Mandatory-field rules work from both the form and board.
- Priority-1 approval can only be recorded by an authorized approver.
- A Board Contributor can update work items without repository or process-administration access.
- A Board Reader can view work items but cannot edit them.
- Evidence is captured for every validation case.
