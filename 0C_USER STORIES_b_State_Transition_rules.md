# Azure DevOps API rule: User Story transition from New

## 1. Restrict From New

- Under **When**, select **A work item state moved from...** and choose
  **New**.
- Under **Then**, select **Restrict the state transition to...** for every
  state except:
  - Refinement
  - Ready for Dev
  - Dev In Progress
  - Code Review
  - Ready for QA
  - QA In Progress
  - Removed

These seven states are the only allowed destinations from **New**.

## Run in PowerShell

First run `0C_USER STORIES_a_setup.md`. Then run this entire block in the same
PowerShell session so `$statesUri`, `$rulesUri`, and `$headers` are available.

```powershell
$ruleName = 'Restrict From New'
$sourceStateName = 'New'
$allowedStates = @(
    'Refinement',
    'Ready for Dev',
    'Dev In Progress',
    'Code Review',
    'Ready for QA',
    'QA In Progress',
    'Removed'
)

# Refresh the actual User Story states.
$states = Invoke-RestMethod `
    -Uri $statesUri `
    -Method Get `
    -Headers $headers `
    -ErrorAction Stop

$stateNames = @($states.value.name)
$requiredStates = @($sourceStateName) + $allowedStates
$missingStates = @(
    $requiredStates | Where-Object { $_ -notin $stateNames }
)

if ($missingStates.Count -gt 0) {
    throw "Missing User Story states: $($missingStates -join ', ')"
}

# Every destination not explicitly allowed receives a disallowValue action.
$notAllowedStates = @(
    $stateNames | Where-Object { $_ -notin $allowedStates }
)

if ($notAllowedStates.Count -eq 0) {
    throw 'No restricted destination states were found.'
}

Write-Host "Allowed from New: $($allowedStates -join ', ')" `
    -ForegroundColor Green
Write-Host "Restricted from New: $($notAllowedStates -join ', ')" `
    -ForegroundColor Cyan

# Find an existing rule by name or by its equivalent state-transition behavior.
# Azure DevOps rejects two disallowValue rules with the same whenWas condition,
# even when the rule names differ.
$existingRules = Invoke-RestMethod `
    -Uri $rulesUri `
    -Method Get `
    -Headers $headers `
    -ErrorAction Stop

$matchingRules = @(
    foreach ($candidate in $existingRules.value) {
        $hasSourceCondition = @(
            $candidate.conditions | Where-Object {
                ([string]$_.conditionType).TrimStart('$') -eq 'whenWas' -and
                $_.field -eq 'System.State' -and
                $_.value -eq $sourceStateName
            }
        ).Count -gt 0

        $hasRestrictedStateAction = @(
            $candidate.actions | Where-Object {
                ([string]$_.actionType).TrimStart('$') -eq 'disallowValue'
            }
        ).Count -gt 0

        if ($candidate.name -eq $ruleName -or
            ($hasSourceCondition -and $hasRestrictedStateAction)) {
            $candidate
        }
    }
)

if ($matchingRules.Count -gt 1) {
    $matchingRules | Format-Table id, name, isDisabled
    throw "Multiple transition-restriction rules were found for '$sourceStateName'."
}

$actions = @(
    $notAllowedStates | ForEach-Object {
        @{
            actionType  = 'disallowValue'
            targetField = 'System.State'
            value       = $_
        }
    }
)

$rule = @{
    name       = $ruleName
    isDisabled = $false
    conditions = @(
        @{
            conditionType = 'whenWas'
            field         = 'System.State'
            value         = $sourceStateName
        }
    )
    actions = $actions
}

$ruleBody = $rule | ConvertTo-Json -Depth 10
$ruleBody

$requestMethod = 'Post'
$requestUri = $rulesUri
$operation = 'created'

if ($matchingRules.Count -eq 1) {
    $existingRule = $matchingRules[0]
    $rulesBaseUri = $rulesUri -replace '\?.*$', ''
    $requestUri = "$rulesBaseUri/$($existingRule.id)?api-version=7.1"
    $requestMethod = 'Put'
    $operation = "updated from '$($existingRule.name)'"
}

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
    Write-Host "Rule ID: $($result.id)"
}
catch {
    $statusCode = 0
    if ($null -ne $_.Exception.Response) {
        $statusCode = [int]$_.Exception.Response.StatusCode
    }

    if ($statusCode -eq 304) {
        Write-Host "Rule already matches; unchanged: $ruleName" `
            -ForegroundColor Yellow
        return
    }

    Write-Host "Error saving rule '$ruleName': $($_.Exception.Message)" `
        -ForegroundColor Red

    if ($_.ErrorDetails.Message) {
        Write-Host "Azure DevOps response: $($_.ErrorDetails.Message)"
    }

    throw
}
```
