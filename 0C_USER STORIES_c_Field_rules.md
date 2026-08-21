# Azure DevOps API rule: Require User Story Description for New

## 15. Require Description for New

- Under **When**, select **A work item state changes to...** and choose
  **New**.
- Under **Then**, select **Make required...** and choose **Description**.

The REST request represents this condition with `conditionType = 'when'`,
`System.State = New`, and a `makeRequired` action for `System.Description`.

## Run in PowerShell

First run `0C_USER STORIES_a_setup.md`. Then run this entire block in the same
PowerShell session so `$statesUri`, `$rulesUri`, and `$headers` are available.

```powershell
$ruleName = 'Require Description for New'
$stateName = 'New'
$targetField = 'System.Description'

# Refresh and validate the New state.
$states = Invoke-RestMethod `
    -Uri $statesUri `
    -Method Get `
    -Headers $headers `
    -ErrorAction Stop

$newState = $states.value |
    Where-Object { $_.name -eq $stateName } |
    Select-Object -First 1

if (-not $newState) {
    throw "User Story state '$stateName' was not found."
}

# Find an existing rule by name or equivalent behavior so rerunning the script
# updates it instead of creating a conflicting duplicate.
$currentRules = Invoke-RestMethod `
    -Uri $rulesUri `
    -Method Get `
    -Headers $headers `
    -ErrorAction Stop

$matchingRules = @(
    foreach ($candidate in $currentRules.value) {
        $hasStateCondition = @(
            $candidate.conditions | Where-Object {
                ([string]$_.conditionType).TrimStart('$') -in @(
                    'when', 'whenStateChangedTo'
                ) -and
                $_.field -eq 'System.State' -and
                $_.value -eq $stateName
            }
        ).Count -gt 0

        $hasRequiredAction = @(
            $candidate.actions | Where-Object {
                ([string]$_.actionType).TrimStart('$') -eq 'makeRequired' -and
                $_.targetField -eq $targetField
            }
        ).Count -gt 0

        if ($candidate.name -eq $ruleName -or
            ($hasStateCondition -and $hasRequiredAction)) {
            $candidate
        }
    }
)

if ($matchingRules.Count -gt 1) {
    $matchingRules | Format-Table id, name, isDisabled
    throw "Multiple equivalent rules were found for '$ruleName'."
}

$rule = @{
    name       = $ruleName
    isDisabled = $false
    conditions = @(
        @{
            conditionType = 'when'
            field         = 'System.State'
            value         = [string]$newState.name
        }
    )
    actions = @(
        @{
            actionType  = 'makeRequired'
            targetField = $targetField
            value       = ''
        }
    )
}

$requestMethod = 'Post'
$requestUri = $rulesUri
$operation = 'created'

if ($matchingRules.Count -eq 1) {
    $rulesBaseUri = $rulesUri -replace '\?.*$', ''
    $requestUri = "$rulesBaseUri/$($matchingRules[0].id)?api-version=7.1"
    $requestMethod = 'Put'
    $operation = 'updated and enabled'
}

$ruleBody = $rule | ConvertTo-Json -Depth 10
$ruleBody

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
