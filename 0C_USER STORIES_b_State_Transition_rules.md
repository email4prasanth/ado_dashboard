# Azure DevOps API rules: User Story state transitions

## Configured states

| State |
| --- |
| New |
| Refinement |
| Ready for Dev |
| Dev In Progress |
| Code Review |
| Ready for QA |
| QA In Progress |
| On Hold |
| Ready for Pre-prod |
| Ready for Prod |
| Closed |
| Reopened |
| Rejected |
| Deferred |
| Removed |

## Requested transition rules

| Rule | Source | Allowed destinations | Restricted actions needed* |
| --- | --- | --- | ---: |
| Restrict From New | New | Refinement, Ready for Dev, Dev In Progress, Code Review, Ready for QA, QA In Progress, Removed | 7 |
| Restrict From Refinement | Refinement | Ready for Dev, Removed, Rejected, Deferred | 10 |
| Restrict From Ready for Dev | Ready for Dev | Dev In Progress, Refinement, On Hold, Removed | 10 |
| Restrict From Dev In Progress | Dev In Progress | Code Review, Ready for Dev, On Hold, Removed | 10 |
| Restrict From Code Review | Code Review | Ready for QA, Dev In Progress, On Hold, Removed | 10 |
| Restrict From Ready for QA | Ready for QA | QA In Progress, Code Review, On Hold, Removed | 10 |
| Restrict From QA In Progress | QA In Progress | Ready for Pre-prod, Ready for QA, Code Review, Removed | 10 |
| Restrict From On Hold | On Hold | Ready for Dev, Dev In Progress, Code Review, Ready for QA, QA In Progress, Removed | 8 |
| Restrict From Ready for Pre-prod | Ready for Pre-prod | Ready for Prod, QA In Progress, On Hold, Removed | 10 |
| Restrict From Ready for Prod | Ready for Prod | Closed, Ready for Pre-prod, On Hold, Removed | 10 |
| Restrict From Closed | Closed | Reopened, Removed, Rejected, Deferred | 10 |
| Restrict From Reopened | Reopened | Ready for Dev, Dev In Progress, Code Review, Ready for QA, QA In Progress, Closed | 8 |
| Restrict From Rejected | Rejected | New, Refinement, Removed, Deferred | 10 |
| Restrict From Deferred | Deferred | Refinement, Ready for Dev, New, Removed | 10 |

\*The source state itself is excluded because remaining in the same state is
not a transition.

## Azure DevOps limitation

Azure DevOps permits at most 10 actions in one rule. It also rejects multiple
rules that use the same `whenWas` condition and `disallowValue` action type, so
a transition restriction cannot be split into continuation rules.

The revised mappings in this document require no more than 10 actions per rule,
so all 14 rules can be represented as inherited-process rules.

## Run in PowerShell

First run `0C_USER STORIES_a_setup.md`, then run this entire block. The script
validates all requested mappings before making any rule changes. It stops
without partial updates when a rule exceeds Azure DevOps's action limit.

```powershell
$configuredStates = @(
    'New',
    'Refinement',
    'Ready for Dev',
    'Dev In Progress',
    'Code Review',
    'Ready for QA',
    'QA In Progress',
    'On Hold',
    'Ready for Pre-prod',
    'Ready for Prod',
    'Closed',
    'Reopened',
    'Rejected',
    'Deferred',
    'Removed'
)

$ruleDefinitions = @(
    @{
        name = 'Restrict From New'
        source = 'New'
        allowed = @(
            'Refinement', 'Ready for Dev', 'Dev In Progress', 'Code Review',
            'Ready for QA', 'QA In Progress', 'Removed'
        )
    }
    @{
        name = 'Restrict From Refinement'
        source = 'Refinement'
        allowed = @('Ready for Dev', 'Removed', 'Rejected', 'Deferred')
    }
    @{
        name = 'Restrict From Ready for Dev'
        source = 'Ready for Dev'
        allowed = @('Dev In Progress', 'Refinement', 'On Hold', 'Removed')
    }
    @{
        name = 'Restrict From Dev In Progress'
        source = 'Dev In Progress'
        allowed = @('Code Review', 'Ready for Dev', 'On Hold', 'Removed')
    }
    @{
        name = 'Restrict From Code Review'
        source = 'Code Review'
        allowed = @('Ready for QA', 'Dev In Progress', 'On Hold', 'Removed')
    }
    @{
        name = 'Restrict From Ready for QA'
        source = 'Ready for QA'
        allowed = @('QA In Progress', 'Code Review', 'On Hold', 'Removed')
    }
    @{
        name = 'Restrict From QA In Progress'
        source = 'QA In Progress'
        allowed = @(
            'Ready for Pre-prod', 'Ready for QA', 'Code Review', 'Removed'
        )
    }
    @{
        name = 'Restrict From On Hold'
        source = 'On Hold'
        allowed = @(
            'Ready for Dev', 'Dev In Progress', 'Code Review', 'Ready for QA',
            'QA In Progress', 'Removed'
        )
    }
    @{
        name = 'Restrict From Ready for Pre-prod'
        source = 'Ready for Pre-prod'
        allowed = @('Ready for Prod', 'QA In Progress', 'On Hold', 'Removed')
    }
    @{
        name = 'Restrict From Ready for Prod'
        source = 'Ready for Prod'
        allowed = @('Closed', 'Ready for Pre-prod', 'On Hold', 'Removed')
    }
    @{
        name = 'Restrict From Closed'
        source = 'Closed'
        allowed = @('Reopened', 'Removed', 'Rejected', 'Deferred')
    }
    @{
        name = 'Restrict From Reopened'
        source = 'Reopened'
        allowed = @(
            'Ready for Dev', 'Dev In Progress', 'Code Review', 'Ready for QA',
            'QA In Progress', 'Closed'
        )
    }
    @{
        name = 'Restrict From Rejected'
        source = 'Rejected'
        allowed = @('New', 'Refinement', 'Removed', 'Deferred')
    }
    @{
        name = 'Restrict From Deferred'
        source = 'Deferred'
        allowed = @('Refinement', 'Ready for Dev', 'New', 'Removed')
    }
)

# Refresh the states returned by Azure DevOps.
$states = Invoke-RestMethod `
    -Uri $statesUri `
    -Method Get `
    -Headers $headers `
    -ErrorAction Stop
$stateNames = @($states.value.name)

$missingStates = @(
    $configuredStates | Where-Object { $_ -notin $stateNames }
)
if ($missingStates.Count -gt 0) {
    throw "Missing User Story states: $($missingStates -join ', ')"
}

# Calculate all rule actions before changing Azure DevOps.
$preparedRules = @(
    foreach ($definition in $ruleDefinitions) {
        $notAllowed = @(
            $stateNames | Where-Object {
                $_ -ne $definition.source -and
                $_ -notin $definition.allowed
            }
        )

        [pscustomobject]@{
            Definition = $definition
            NotAllowed = $notAllowed
            ActionCount = $notAllowed.Count
        }
    }
)

$unsupportedRules = @(
    $preparedRules | Where-Object { $_.ActionCount -gt 10 }
)

if ($unsupportedRules.Count -gt 0) {
    Write-Host 'Rules exceeding the Azure DevOps 10-action limit:' `
        -ForegroundColor Red
    $unsupportedRules |
        ForEach-Object {
            [pscustomobject]@{
                Rule = $_.Definition.name
                Source = $_.Definition.source
                RestrictedActions = $_.ActionCount
                Maximum = 10
            }
        } |
        Format-Table -AutoSize

    throw 'No rules were changed. Revise the allowed destinations or workflow states.'
}

$currentRules = Invoke-RestMethod `
    -Uri $rulesUri `
    -Method Get `
    -Headers $headers `
    -ErrorAction Stop

foreach ($prepared in $preparedRules) {
    $definition = $prepared.Definition

    $actions = @(
        $prepared.NotAllowed | ForEach-Object {
            @{
                actionType = 'disallowValue'
                targetField = 'System.State'
                value = $_
            }
        }
    )

    $rule = @{
        name = $definition.name
        isDisabled = $false
        conditions = @(
            @{
                conditionType = 'whenWas'
                field = 'System.State'
                value = $definition.source
            }
        )
        actions = $actions
    }

    $matchingRules = @(
        foreach ($candidate in $currentRules.value) {
            $sameCondition = @(
                $candidate.conditions | Where-Object {
                    ([string]$_.conditionType).TrimStart('$') -eq 'whenWas' -and
                    $_.field -eq 'System.State' -and
                    $_.value -eq $definition.source
                }
            ).Count -gt 0

            $sameActionType = @(
                $candidate.actions | Where-Object {
                    ([string]$_.actionType).TrimStart('$') -eq 'disallowValue'
                }
            ).Count -gt 0

            if ($candidate.name -eq $definition.name -or
                ($sameCondition -and $sameActionType)) {
                $candidate
            }
        }
    )

    if ($matchingRules.Count -gt 1) {
        throw "Multiple transition rules were found for '$($definition.source)'."
    }

    $requestMethod = 'Post'
    $requestUri = $rulesUri
    $operation = 'created'

    if ($matchingRules.Count -eq 1) {
        $rulesBaseUri = $rulesUri -replace '\?.*$', ''
        $requestUri = "$rulesBaseUri/$($matchingRules[0].id)?api-version=7.1"
        $requestMethod = 'Put'
        $operation = 'updated'
    }

    $ruleBody = $rule | ConvertTo-Json -Depth 10

    try {
        $result = Invoke-RestMethod `
            -Uri $requestUri `
            -Method $requestMethod `
            -Headers $headers `
            -ContentType 'application/json' `
            -Body $ruleBody `
            -ErrorAction Stop

        Write-Host "Rule $operation successfully: $($result.name)" `
            -ForegroundColor Green
    }
    catch {
        $statusCode = 0
        if ($null -ne $_.Exception.Response) {
            $statusCode = [int]$_.Exception.Response.StatusCode
        }

        if ($statusCode -eq 304) {
            Write-Host "Rule already matches: $($definition.name)" `
                -ForegroundColor Yellow
            continue
        }

        if ($_.ErrorDetails.Message) {
            Write-Host "Azure DevOps response: $($_.ErrorDetails.Message)"
        }
        throw
    }
}
```

Reference: [Azure DevOps work tracking process limits](https://learn.microsoft.com/en-us/azure/devops/organizations/settings/work/object-limits?view=azure-devops).
