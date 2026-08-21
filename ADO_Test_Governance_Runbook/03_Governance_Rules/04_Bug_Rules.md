# Bug Rules

## BUG-01: Required information before work starts

Open:

```text
Organization settings
→ Process
→ Scrum Hybrid Governance
→ Bug
→ Rules
→ New rule
```

Configure:

```text
Name: BUG-01 Required information for In Progress

When:
A work item state changes to → In Progress

Then:
Make required → Assigned To
Make required → Repro Steps
Make required → Priority
Make required → Platform
```

This incorporates Deva's request to fill the reproduction steps, platform, and other mandatory details.

## BUG-02: Resolution required for Done

```text
Name: BUG-02 Resolution Notes required for Done

When:
A work item state changes to → Done

Then:
Make required → Resolution Notes
```

## BUG-03: Hold reason required

```text
Name: BUG-03 Governance Reason required for On Hold

When:
A work item state changes to → On Hold

Then:
Make required → Governance Reason
```

Add the existing `Governance Reason` field to the Bug layout before creating BUG-03.

## Verification

Verify the same rule from:

- The Bug work item form.
- Dragging a Bug card between board columns.

The card move should be rejected when a mandatory field is empty.

