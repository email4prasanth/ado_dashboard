# Azure DevOps API rules: Feature state transitions

## State mapping

| Feature state |
| --- |
| New |
| Approved |
| In Progress |
| On Hold |
| Ready to Release |
| Completed |
| Removed |

## Transition rules

| Rule name | Source state | Allowed destinations | Restricted destinations |
| --- | --- | --- | --- |
| Restrict From New | New | Approved, Removed | New, In Progress, On Hold, Ready to Release, Completed |
| Restrict From Approved | Approved | In Progress, Removed | New, Approved, On Hold, Ready to Release, Completed |
| Restrict From In Progress | In Progress | On Hold, Ready to Release, Completed, Removed | New, Approved, In Progress |
| Restrict From On Hold | On Hold | In Progress, Removed | New, Approved, On Hold, Ready to Release, Completed |
| Restrict From Ready to Release | Ready to Release | Completed, Removed | New, Approved, In Progress, On Hold, Ready to Release |
| Restrict From Completed | Completed | None (end state) | New, Approved, In Progress, On Hold, Ready to Release, Completed, Removed |
| Restrict From Removed | Removed | None (end state) | New, Approved, In Progress, On Hold, Ready to Release, Completed, Removed |

For every rule, select **A work item state moved from...** under **When** and
choose the source state. Under **Then**, add **Restrict the state transition
to...** for every restricted destination shown in the table.

## Run in PowerShell

First run the setup block in `0B_FEATURE_API_a_Setup.md`. Then run this entire
code block in the same PowerShell session. Do not execute partial selections.

```powershell
# Refresh the Feature states from Azure DevOps.
$states = Invoke-RestMethod -Uri $statesUri -Method Get -Headers $headers
$stateNames = @($states.value.name)

$featureStates = @(
    'New',
    'Approved',
    'In Progress',
    'On Hold',
    'Ready to Release',
    'Completed',
    'Removed'
)

$missingFeatureStates = @(
    $featureStates | Where-Object { $_ -notin $stateNames }
)

if ($missingFeatureStates.Count -gt 0) {
    throw "Missing Feature states: $($missingFeatureStates -join ', ')"
}

$ruleDefinitions = @(
    @{
        name    = 'Restrict From New'
        source  = 'New'
        allowed = @('Approved', 'Removed')
    }
    @{
        name    = 'Restrict From Approved'
        source  = 'Approved'
        allowed = @('In Progress', 'Removed')
    }
    @{
        name    = 'Restrict From In Progress'
        source  = 'In Progress'
        allowed = @('On Hold', 'Ready to Release', 'Completed', 'Removed')
    }
    @{
        name    = 'Restrict From On Hold'
        source  = 'On Hold'
        allowed = @('In Progress', 'Removed')
    }
    @{
        name    = 'Restrict From Ready to Release'
        source  = 'Ready to Release'
        allowed = @('Completed', 'Removed')
    }
    @{
        name    = 'Restrict From Completed'
        source  = 'Completed'
        allowed = @()
    }
    @{
        name    = 'Restrict From Removed'
        source  = 'Removed'
        allowed = @()
    }
)

# Existing rules with the same name are skipped.
$existingRules = Invoke-RestMethod -Uri $rulesUri -Method Get -Headers $headers

foreach ($definition in $ruleDefinitions) {
    $existingRule = $existingRules.value |
        Where-Object { $_.name -eq $definition.name } |
        Select-Object -First 1

    if ($existingRule) {
        Write-Host "Rule already exists; skipped: $($definition.name)" `
            -ForegroundColor Yellow
        continue
    }

    $notAllowedStates = @(
        $featureStates | Where-Object { $_ -notin $definition.allowed }
    )

    $actions = @(
        $notAllowedStates | ForEach-Object {
            @{
                actionType  = 'disallowValue'
                targetField = 'System.State'
                value       = $_
            }
        }
    )

    # The condition requires the exact display name returned by Azure DevOps.
    $sourceState = $states.value |
        Where-Object { $_.name -eq $definition.source } |
        Select-Object -First 1
    $sourceConditionValue = [string]$sourceState.name

    if ([string]::IsNullOrWhiteSpace($sourceConditionValue)) {
        throw "Condition value could not be resolved for '$($definition.source)'."
    }

    $rule = @{
        name       = $definition.name
        isDisabled = $false
        conditions = @(
            @{
                conditionType = 'whenWas'
                field         = 'System.State'
                value         = $sourceConditionValue
            }
        )
        actions = $actions
    }

    $ruleBody = $rule | ConvertTo-Json -Depth 10
    $ruleBody

    try {
        $result = Invoke-RestMethod `
            -Uri $rulesUri `
            -Method Post `
            -Headers $headers `
            -ContentType 'application/json' `
            -Body $ruleBody

        Write-Host "Rule created successfully: $($result.name)" `
            -ForegroundColor Green
        Write-Host "Rule ID: $($result.id)"
    }
    catch {
        Write-Host "Error creating rule '$($definition.name)': $($_.Exception.Message)" `
            -ForegroundColor Red

        if ($_.ErrorDetails.Message) {
            Write-Host "Azure DevOps response: $($_.ErrorDetails.Message)"
        }

        throw
    }
}
```
