# Preparation and Safety Check

## Required access

Ask the testing-organization owner for only the permissions needed to create the proof of concept:

- Access level: Basic or higher for the person configuring the process.
- Collection permission: Create process = Allow.
- Collection permission: Edit process = Allow.
- Collection permission: Create new projects = Allow.
- Project Administrator membership for the new test project.

Do not request access to production repositories, pipelines, service connections, releases, or environments.

## Record the baseline

Before making a change:

1. Sign in to Azure DevOps manually.
2. Open the organization selector.
3. Confirm that the selected organization is `inlai-projects`.
4. Open **Organization settings → Process**.
5. Capture a screenshot of the process list.
6. Record whether `Scrum Hybrid` exists in this testing organization.
7. Open the organization Projects page.
8. Capture a screenshot of the existing project list.
9. Record the current date, operator, and organization in the following table.

| Item | Value |
|---|---|
| Organization | inlai-projects |
| Operator | |
| Date/time | |
| Scrum Hybrid exists? | Yes / No |
| Existing project count | |
| Change ticket/reference | |

## Mandatory stop conditions

Stop without saving if any of the following is true:

- The organization is not `inlai-projects`.
- The screen displays a project connected to the 17 existing repositories.
- `Scrum Hybrid Governance` already exists and its owner/purpose is unknown.
- You cannot see which projects use the process you intend to edit.
- You are asked to change a shared process in the production organization.

## Important scope facts

- A process is configured at organization level, not repository level.
- Every project assigned to an inherited process receives changes made to that process.
- A project can contain multiple repositories, but a Boards-process change is assigned to the project.
- The safe method is to create a new inherited process and a new private test project.

