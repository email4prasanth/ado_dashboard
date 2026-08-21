# Create the Custom Governance Fields

## Open the process

```text
Organization settings
→ Process
→ Scrum Hybrid Governance
→ Work Item Types
```

## Product Backlog Item fields

Open Product Backlog Item → **Layout → New field** and create:

### Governance Reason

```text
Name: Governance Reason
Type: Picklist (string)
Required: No
Default: None
Allow users to enter their own values: No
```

Values:

```text
Awaiting dependency
Resource constraint
Environment unavailable
Business decision
Blocked
Deferred
Cancelled
Duplicate
Out of scope
Rejected
```

Do not set `Proposed` as a default. A default can remain after the state changes and produce an incorrect explanation.

### Senior Approval Status

```text
Name: Senior Approval Status
Type: Picklist (string)
Required: No
Default: None
Values: Pending, Approved, Rejected
```

### Approved By

```text
Name: Approved By
Type: Identity
Required: No
Default: None
```

## Bug fields

Open Bug → **Layout → New field**.

Create `Platform`:

```text
Type: Picklist (string)
Required: No
Default: None
Example values: Web, Android, iOS, API, Database, Infrastructure, Other
```

Create `Resolution Notes`:

```text
Type: Text (multiple lines) or HTML, depending on the available UI option
Required: No
Default: None
```

## Reuse fields where appropriate

If `Governance Reason` already exists at organization level:

1. Choose **Use an existing field** rather than creating a duplicate.
2. Add the existing field to Epic, Feature, Task, and Bug as required.

## Verification

1. Open a new PBI form in the test project.
2. Confirm that the governance fields appear.
3. Open a new Bug form.
4. Confirm that Platform and Resolution Notes appear.
5. Do not mark fields globally required in field Options; conditional rules will control when they become required.

