## Run in PowerShell

First run `0D_BUGS_a_setup.md`, then run this entire block. The script
validates all requested mappings before making any rule changes. It stops
without partial updates when a rule exceeds Azure DevOps's action limit.

```powershell
$configuredStates = @(
    'New',
    'Triaged',
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
            'Triaged', 'Ready for Dev', 'Dev In Progress', 'Code Review',
            'Ready for QA', 'QA In Progress', 'Removed', 'On Hold', 'Closed'
        )
    }
    @{
        name = 'Restrict From Triaged'
        source = 'Triaged'
        allowed = @('Ready for Dev', 'Dev In Progress', 'On Hold', 'Removed', 'Rejected', 'Deferred', 'Closed', 'Reopened')
    }
    @{
        name = 'Restrict From Ready for Dev'
        source = 'Ready for Dev'
        allowed = @('Dev In Progress', 'Triaged', 'On Hold', 'Removed', 'Duplicate', 'Closed', 'Rejected', 'Code Review', 'Reopened')
    }
    @{
        name = 'Restrict From Dev In Progress'
        source = 'Dev In Progress'
        allowed = @('Code Review', 'Ready for Dev', 'Ready for QA', 'On Hold', 'Removed', 'Duplicate', 'Closed', 'Rejected')
    }
    @{
        name = 'Restrict From Code Review'
        source = 'Code Review'
        allowed = @('Ready for QA', 'Dev In Progress', 'On Hold', 'Removed', 'Duplicate', 'Closed', 'Rejected', 'Reopened')
    }
    @{
        name = 'Restrict From Ready for QA'
        source = 'Ready for QA'
        allowed = @('QA In Progress', 'Code Review', 'On Hold', 'Removed', 'Duplicate', 'Closed', 'Rejected', 'Reopened')
    }
    @{
        name = 'Restrict From QA In Progress'
        source = 'QA In Progress'
        allowed = @(
            'Ready for Pre-prod', 'Ready for QA', 'Code Review', 'Removed', 'Duplicate', 'Closed', 'Rejected', 'Reopened'
        )
    }
    @{
        name = 'Restrict From On Hold'
        source = 'On Hold'
        allowed = @(
            'Ready for Dev', 'Dev In Progress', 'Code Review', 'Ready for QA',
            'QA In Progress', 'Removed', 'Duplicate', 'Closed', 'Rejected'
        )
    }
    @{
        name = 'Restrict From Ready for Pre-prod'
        source = 'Ready for Pre-prod'
        allowed = @('Ready for Prod', 'Code Review', 'QA In Progress', 'On Hold', 'Removed', 'Duplicate', 'Closed', 'Rejected')
    }
    @{
        name = 'Restrict From Ready for Prod'
        source = 'Ready for Prod'
        allowed = @('Triaged','Closed', 'Ready for Pre-prod', 'On Hold', 'Removed', 'Duplicate', 'Closed', 'Rejected', 'Reopened')
    }
    @{
        name = 'Restrict From Closed'
        source = 'Closed'
        allowed = @('New', 'Triaged','Reopened', 'Removed', 'Rejected', 'Deferred', 'Duplicate', 'Closed', 'Rejected', 'Ready for Pre-prod')
    }
    @{
        name = 'Restrict From Reopened'
        source = 'Reopened'
        allowed = @(
            'Ready for Dev', 'Dev In Progress', 'Code Review', 'Ready for QA',
            'QA In Progress', 'Closed', 'Duplicate', 'Rejected'
        )
    }
    @{
        name = 'Restrict From Rejected'
        source = 'Rejected'
        allowed = @('New', 'Triaged', 'Removed', 'Deferred', 'Duplicate', 'Closed', 'Reopened', 'Ready for Pre-prod')
    }
    @{
        name = 'Restrict From Deferred'
        source = 'Deferred'
        allowed = @('New', 'Triaged', 'Ready for Dev', 'New', 'Removed', 'Duplicate', 'Closed', 'Rejected', 'Ready for Pre-prod')
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
    throw "Missing Bugs states: $($missingStates -join ', ')"
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
