# Process Enhancement Proposal
- Introduce a dedicated Azure DevOps process to improve work item lifecycle management, traceability, reporting, QA visibility, and release governance without impacting existing production projects.
- current situation:
```sh
Current Process: Scrum Hybrid (default)

Projects using it:
✓ AI Projects
✓ ManageMyHealthNZ
(and 14 others)

Requirement:
✓ Apply new workflow only to AI Projects
✗ Do NOT impact ManageMyHealthNZ
```
- Recommended Solution
```sh
Scrum
├── Scrum Hybrid (current)
└── Scrum Hybrid Governance (new)

AI Projects         → Scrum Hybrid Governance
ManageMyHealthNZ    → Scrum Hybrid
```
- URL - https://learn.microsoft.com/mt-mt/azure/devops/organizations/settings/work/manage-process?view=azure-devops&utm_source=chatgpt.com
- steps to implement
    1. Create New Process: Organization Settings → Process →  Scrum Hybrid (default) → Create inherited process → Scrum Hybrid Governance Enhanced workflow for governance, QA and release tracking →  create process.
    2. Verify Process Created: Organization Settings → Process
    3. Assign Only AI Projects: Organization Settings → Process → Scrum Hybrid (default)→ Projects → AI Projects → Change process → Scrum Hybrid Governance