# Azure DevOps API rule: Require Feature Description for New

## Rule 8: Require Description for New

- Under **When**, select **A work item state changes to...** and choose
  **New**.
- Under **Then**, select **Make required...** and choose **Description**.

For this REST endpoint, use `conditionType = 'when'` with `System.State` set
to `New`. The action uses `makeRequired` on `System.Description`.

## Run in PowerShell

First run the setup block in `0B_FEATURE_API_a_Setup.md`. Then run this entire
code block in the same PowerShell session so `$headers`, `$states`, and
`$rulesUri` are available.

```powershell
$ruleName = 'Require Description for New'

# Confirm that New is an available Feature state.
$newState = $states.value |
    Where-Object { $_.name -eq 'New' } |
    Select-Object -First 1

if (-not $newState) {
    throw "Feature state 'New' was not found. Run the setup block again."
}

# Make the script safe to rerun by skipping an existing rule with this name.
$existingRules = Invoke-RestMethod -Uri $rulesUri -Method Get -Headers $headers
$existingRule = $existingRules.value |
    Where-Object { $_.name -eq $ruleName } |
    Select-Object -First 1

if ($existingRule) {
    Write-Host "Rule already exists; skipped: $ruleName" -ForegroundColor Yellow
}

if (-not $existingRule) {
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
                targetField = 'System.Description'
                value       = ''
            }
        )
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
        Write-Host "Error creating rule '$ruleName': $($_.Exception.Message)" `
            -ForegroundColor Red

        if ($_.ErrorDetails.Message) {
            Write-Host "Azure DevOps response: $($_.ErrorDetails.Message)"
        }

        throw
    }
}
```
