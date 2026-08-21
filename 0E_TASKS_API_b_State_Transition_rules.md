```sh

# Each definition lists the only destinations that remain allowed. The script
# automatically creates a disallowValue action for every other state.
$ruleDefinitions = @(
    @{
        name    = 'Restrict From New'
        source  = 'New'
        allowed = @('To Do', 'Removed')
    }
    @{
        name    = 'Restrict From To Do'
        source  = 'To Do'
        allowed = @('In Progress', 'Removed')
    }
    @{
        name    = 'Restrict From In Progress'
        source  = 'In Progress'
        allowed = @('On Hold', 'Done', 'Removed')
    }
    @{
        name    = 'Restrict From On Hold'
        source  = 'On Hold'
        allowed = @('In Progress', 'Removed')
    }
    @{
        name    = 'Restrict From Done'
        source  = 'Done'
        allowed = @()
    }
    @{
        name    = 'Restrict From Removed'
        source  = 'Removed'
        allowed = @()
    }
)

# Read existing rules so the script can safely be run again. Existing rules
# with the same name are left unchanged.
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

    $notAllowed = @(
        $requiredStates | Where-Object { $_ -notin $definition.allowed }
    )

    $actions = @(
        $notAllowed | ForEach-Object {
            @{
                actionType  = 'disallowValue'
                targetField = 'System.State'
                value       = $_
            }
        }
    )

    $rule = @{
        name       = $definition.name
        isDisabled = $false
        conditions = @(
            @{
                conditionType = 'whenWas'
                field         = 'System.State'
                value         = $definition.source
            }
        )
        actions = $actions
    }

    $ruleBody = $rule | ConvertTo-Json -Depth 10

    # Display the JSON and reject invalid $-prefixed request enum values.
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
        Write-Host "Error creating rule '$($definition.name)': $($_.Exception.Message)" `
            -ForegroundColor Red

        if ($_.ErrorDetails.Message) {
            Write-Host "Azure DevOps response: $($_.ErrorDetails.Message)"
        }
        elseif ($_.Exception.Response -and $_.Exception.Response.GetResponseStream) {
            $reader = [System.IO.StreamReader]::new(
                $_.Exception.Response.GetResponseStream()
            )
            try {
                Write-Host "Azure DevOps response: $($reader.ReadToEnd())"
            }
            finally {
                $reader.Dispose()
            }
        }

        throw
    }
}
```