# Configure a Boards-Only Test Project

## Procedure

1. Open `Scrum-Governance-Test`.
2. Open **Project settings → Overview**.
3. Locate the service visibility controls.
4. Keep **Boards** enabled.
5. Turn off:

```text
Repos
Pipelines
Artifacts
```

6. Keep **Test Plans** off for the first workflow demonstration.
7. Enable Test Plans later only if you have the required license and need to demonstrate Test Cases.
8. Refresh the browser.
9. Confirm that the left navigation shows Boards and does not show the disabled services.
10. Capture a screenshot.

## Notes

- Service visibility is project-wide; it is not a substitute for individual permissions.
- Turning a service off hides it. It does not delete service data.
- Because this is a new test project, no production data should exist in these services.

