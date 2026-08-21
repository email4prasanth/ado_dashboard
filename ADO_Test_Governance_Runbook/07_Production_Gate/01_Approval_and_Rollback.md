# Approval Gate and Test Rollback

## Production gate

The proof of concept does not authorize production changes.

Before a separate production implementation is considered, obtain:

- Deva's written approval of hierarchy, workflow, fields, and rules.
- Named business process owner.
- Named Azure DevOps system administrator.
- List of projects that would use the process.
- Confirmation of how many repositories exist in each affected project.
- Impact review for boards, queries, dashboards, Analytics, integrations, and existing work items.
- Exported or documented current-state configuration.
- Tested migration and rollback procedure.
- Approved maintenance/change window.

## Safe rollback inside the test project

Prefer disabling an individual rule before deleting it:

1. Confirm organization `inlai-projects`.
2. Open **Organization settings → Process → Scrum Hybrid Governance**.
3. Select the affected work item type.
4. Open **Rules**.
5. Open the rule `...` menu.
6. Select **Disable**.
7. Retest the work item.
8. Record why the rule was disabled.

For a problematic custom state:

1. Move all test work items out of that state.
2. Update board-column mapping.
3. Hide or remove only the custom state.
4. Retest the board.

## Do not use process switching as the first rollback

Changing a project to another process can leave incompatible fields, states, board mappings, or work items. In the test project, first disable the faulty rule or hide the faulty custom element.

## Test cleanup

Do not delete the test project or process until evidence and approval decisions are retained.

If cleanup is authorized:

1. Record all assigned projects for `Scrum Hybrid Governance`.
2. Confirm it is assigned only to `Scrum-Governance-Test`.
3. Export or capture required evidence.
4. Ask the test-organization owner to follow the organization's retention procedure.
5. Record what was removed and whether Azure DevOps provides a recovery window.

