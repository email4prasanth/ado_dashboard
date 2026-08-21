- Azure DevOps Governance Model
```sh
Project: Ai-Projects
│
├── Epic
│   │
│   ├── Feature
│   │   │
│   │   ├── User Story
│   │   │   ├── Dev Task
│   │   │   ├── QA Task
│   │   │   ├── Documentation Task
│   │   │   ├── Bug
│   │   │   └── Test Case
│   │   │
│   │   │   Linked Artifacts:
│   │   │   • Commits
│   │   │   • Pull Requests
│   │   │   • Builds
│   │   │   • Releases
│   │   │
│   │   ├── User Story
│   │   ├── Feature Bug
│   │   └── Test Plan
│   │
│   │   Supporting Artifacts:
│   │   • Design Document
│   │   • Architecture Diagram
│   │
│   └── Feature
│
└── Release Milestone
```
- workflow
```sh
Epic, Feature, User Story, Test Plan:
New → Approved → In Progress → On Hold → Completed → Removed

Dev Task, QA Task, Documentation Task, Bug, Test Case:
New → In Progress → On Hold → Completed → Removed
```
