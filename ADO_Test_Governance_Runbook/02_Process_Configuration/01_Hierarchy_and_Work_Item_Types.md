# Configure the Hierarchy and Work Item Types

## Standard hierarchy

Use the standard Scrum hierarchy for the first proof of concept:

```text
Epic
└── Feature
    └── Product Backlog Item
        └── Task
```

Do not rename Product Backlog Item to User Story in the first test. Mixing PBI and User Story names makes rules, queries, reports, and training inconsistent.

## Bug configuration

1. Open `Scrum-Governance-Test`.
2. Open **Project settings → Teams**.
3. Select the test team.
4. Open the team's **Backlogs** or **Team configuration** settings.
5. Locate **Working with bugs**.
6. Choose one model and record it:

```text
Recommended: Bugs are managed with requirements.
```

This makes Bugs visible with Product Backlog Items on the product backlog and board.

## Test Case treatment

- Do not make Test Case a normal child in the delivery hierarchy.
- Link Test Cases to PBIs or Bugs using the testing/tested-by relationship available in Azure DevOps.
- Use Test Plans only when licensing and the demonstration scope permit it.

## Separate task types

Do not create Dev Task, QA Task, and Documentation Task work item types initially.

Use the standard Task and its `Activity` field:

```text
Development
Testing
Documentation
Deployment
Design
Requirements
```

Only create separate work item types after the proof of concept if reporting or form differences cannot be satisfied by Activity.

