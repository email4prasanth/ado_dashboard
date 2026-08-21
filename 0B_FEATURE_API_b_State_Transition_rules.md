# Azure DevOps API rule: Feature state transition from New

This rule allows a Feature in **New** to transition only to **Approved** or
**Removed**.

## Azure DevOps UI configuration

1. Create a rule named **Restrict From New**.
2. Under **When**, select **A work item state moved from...** and choose
   **New**.
3. Under **Then**, select **Restrict the state transition to...** for each of
   these states:
   - New
   - In Progress
   - On Hold
   - Ready for Release
   - Done

Do not restrict **Approved** or **Removed**; these are the only allowed
destination states.

## Run in PowerShell

Run the setup in `0B_FEATURE_API_a_Setup.md` first, or run both code blocks in
the same PowerShell session. This script uses the `$headers`, `$rulesUri`, and
state information created by that setup.

```powershell
$ruleName = 'Restrict From New'
$sourceState = 'New'
$allowedStates = @('Approved', 'Removed')

# Read the states returned by the setup script and calculate every destination
# that must be restricted. This keeps Ready for Release and any other configured
# Feature state from being accidentally omitted.
$notAllowedStates = @(
    $stateNames | Where-Object { $_ -notin $allowedStates }
)

if ($notAllowedStates.Count -eq 0) {
    throw 'No restricted destination states were found.'
}

# Make the script safe to run repeatedly. A rule with the same name is not
# created a second time.
$existingRules = Invoke-RestMethod -Uri $rulesUri -Method Get -Headers $headers
$existingRule = $existingRules.value |
    Where-Object { $_.name -eq $ruleName } |
    Select-Object -First 1

if ($existingRule) {
    Write-Host "Rule already exists; skipped: $ruleName" -ForegroundColor Yellow
}
else {
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
                value         = $sourceState
            }
        )
        actions = $actions
    }

    $ruleBody = $rule | ConvertTo-Json -Depth 10

    # Display the request body and reject invalid $-prefixed enum values.
    $ruleBody
    if ($ruleBody -match '"(?:conditionType|actionType)"\s*:\s*"\$') {
        throw 'Rule JSON contains an invalid $-prefixed enum value.'
    }

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
