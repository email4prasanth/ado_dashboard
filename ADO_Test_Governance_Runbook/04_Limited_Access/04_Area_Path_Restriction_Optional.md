# Optional Area Path Restriction

Use this only if the demonstration must show that a user can access one team's work items but not another team's work items.

## Create area paths

1. Open **Project settings → Project configuration → Areas**.
2. Under the project root, create:

```text
Scrum-Governance-Test\Governance-Team
Scrum-Governance-Test\Restricted-Team
```

## Limit the Board Contributor

1. Select the `Governance-Team` area.
2. Open **Security**.
3. Select `Governance Board Contributors`.
4. Set:

```text
View work items in this node: Allow
Edit work items in this node: Allow
```

5. Select the `Restricted-Team` area.
6. Open **Security**.
7. Set:

```text
View work items in this node: Deny
Edit work items in this node: Deny
```

## Configure the team

1. Open **Project settings → Teams**.
2. Select the Governance test team.
3. Open **Iterations and areas** or **Team configuration**.
4. Select `Governance-Team` as the team's area path.

## Warning

Explicit Deny normally overrides Allow inherited through other groups. Apply it only to the test group and exact test area path.

