# Epic field-validation API rules

This document creates or updates Epic field-validation rules 7–12. It resolves
each field's Azure DevOps reference name from its display name before building
the rule payload.

For all state conditions, the REST API uses `conditionType = 'when'`. The
Azure DevOps UI may describe this as **A work item state changes to...**, but
this endpoint rejects `whenStateChangedTo` with error `VS1640105`.

## Rule definitions

### 7. Require Description for New

- When state is **New**
- Make **Description** required

### 8. Require Business Case for Approved

- When state is **Approved**
- Make **Assigned To** required
- Make **SeniorApprovedBy** required
- Make **Senior Approval Status** required

### 9. Require Rules for In Progress

- When state is **In Progress**
- Clear **Priority**, then make it required
- Clear **Time Criticality**, then make it required
- Make **Effort** required
- Make **Assigned To**, **SeniorApprovedBy**, **Senior Approval Status**, and
  **Business Value** read-only

### 10. Require Reason for On Hold

- When state is **On Hold**
- Clear **Description**, then make it required
- Make **Assigned To**, **Priority**, **Time Criticality**,
  **SeniorApprovedBy**, **Senior Approval Status**, **Effort**, **Value Area**,
  and **Business Value** read-only

### 11. Require Rules for Done

- When state is **Done**
- Make **Acceptance Criteria** required
- Clear **Description**, then make it required
- Make **Assigned To**, **Priority**, **Effort**, **Business Value**,
  **Time Criticality**, **Value Area**, **SeniorApprovedBy**, and
  **Senior Approval Status** read-only

### 12. Require Reason for Removed

- When state is **Removed**
- Clear **Description**, then make it required
- Make **Assigned To**, **Priority**, **Effort**, **Business Value**,
  **Time Criticality**, **Value Area**, **SeniorApprovedBy**, and
  **Senior Approval Status** read-only

## Run in PowerShell

Run the complete block after `$rulesUri` and `$headers` have been
initialized for the Scrum Hybrid Governance Epic. Existing rules with the same
name are updated and enabled; missing rules are created.

Always restart at the first line of the block after an error. Do not continue
with stale `$epicFields` or `$fieldReferences` values from a failed run.

```powershell
# Confirm and derive the target endpoints.
Write-Host "Target rules endpoint: $rulesUri" -ForegroundColor Cyan
$witBaseUri = $rulesUri -replace '/rules\?.*$', ''
$fieldsUri = "$witBaseUri/fields?api-version=7.1"
Write-Host "Target fields endpoint: $fieldsUri" -ForegroundColor Cyan

# Load the fields currently attached to this Epic work item type.
$fieldsResponse = Invoke-RestMethod `
    -Uri $fieldsUri `
    -Method Get `
    -Headers $headers `
    -ErrorAction Stop

$epicFields = @($fieldsResponse.value)
if ($epicFields.Count -eq 0) {
    throw "No Epic fields were returned by '$fieldsUri'."
}

function Resolve-EpicFieldReference {
    param(
        [Parameter(Mandatory)]
        [string] $DisplayName,

        [Parameter(Mandatory)]
        [object[]] $AvailableFields
    )

    $matches = @(
        $AvailableFields | Where-Object { $_.name -eq $DisplayName }
    )

    if ($matches.Count -eq 0) {
        Write-Host "Available Epic fields:" -ForegroundColor Yellow
        $AvailableFields |
            Sort-Object name |
            Format-Table name, referenceName, type, required, readOnly
        throw "Epic field '$DisplayName' was not found."
    }

    if ($matches.Count -gt 1) {
        $matches | Format-Table name, referenceName, type
        throw "Multiple Epic fields named '$DisplayName' were found."
    }

    return $matches[0].referenceName
}

# Resolve all field names before changing any rules.
$requiredFieldNames = @(
    'Description',
    'Assigned To',
    'SeniorApprovedBy',
    'Senior Approval Status',
    'Priority',
    'Time Criticality',
    'Effort',
    'Business Value',
    'Value Area',
    'Acceptance Criteria'
)

$fieldReferences = @{}
foreach ($fieldName in $requiredFieldNames) {
    $fieldReferences[$fieldName] = Resolve-EpicFieldReference `
        -DisplayName $fieldName `
        -AvailableFields $epicFields
}

Write-Host 'Resolved Epic field references:' -ForegroundColor Cyan
$fieldReferences.GetEnumerator() |
    Sort-Object Name |
    Format-Table Name, Value

# Actions use the Azure DevOps REST enum names without a $ prefix:
# makeRequired, makeReadOnly, and setValueToEmpty.
$fieldRuleDefinitions = @(
    @{
        name    = 'Require Description for New'
        state   = 'New'
        actions = @(
            @{ type = 'makeRequired'; field = 'Description' }
        )
    }
    @{
        name    = 'Require Business Case for Approved'
        state   = 'Approved'
        actions = @(
            @{ type = 'makeRequired'; field = 'Assigned To' }
            @{ type = 'makeRequired'; field = 'SeniorApprovedBy' }
            @{ type = 'makeRequired'; field = 'Senior Approval Status' }
        )
    }
    @{
        name    = 'Require Rules for In Progress'
        state   = 'In Progress'
        actions = @(
            @{ type = 'setValueToEmpty'; field = 'Priority' }
            @{ type = 'makeRequired'; field = 'Priority' }
            @{ type = 'setValueToEmpty'; field = 'Time Criticality' }
            @{ type = 'makeRequired'; field = 'Time Criticality' }
            @{ type = 'makeRequired'; field = 'Effort' }
            @{ type = 'makeReadOnly'; field = 'Assigned To' }
            @{ type = 'makeReadOnly'; field = 'SeniorApprovedBy' }
            @{ type = 'makeReadOnly'; field = 'Senior Approval Status' }
            @{ type = 'makeReadOnly'; field = 'Business Value' }
        )
    }
    @{
        name    = 'Require Reason for On Hold'
        state   = 'On Hold'
        actions = @(
            @{ type = 'setValueToEmpty'; field = 'Description' }
            @{ type = 'makeRequired'; field = 'Description' }
            @{ type = 'makeReadOnly'; field = 'Assigned To' }
            @{ type = 'makeReadOnly'; field = 'Priority' }
            @{ type = 'makeReadOnly'; field = 'Time Criticality' }
            @{ type = 'makeReadOnly'; field = 'SeniorApprovedBy' }
            @{ type = 'makeReadOnly'; field = 'Senior Approval Status' }
            @{ type = 'makeReadOnly'; field = 'Effort' }
            @{ type = 'makeReadOnly'; field = 'Value Area' }
            @{ type = 'makeReadOnly'; field = 'Business Value' }
        )
    }
    @{
        name    = 'Require Rules for Done'
        state   = 'Done'
        actions = @(
            @{ type = 'makeRequired'; field = 'Acceptance Criteria' }
            @{ type = 'setValueToEmpty'; field = 'Description' }
            @{ type = 'makeRequired'; field = 'Description' }
            @{ type = 'makeReadOnly'; field = 'Assigned To' }
            @{ type = 'makeReadOnly'; field = 'Priority' }
            @{ type = 'makeReadOnly'; field = 'Effort' }
            @{ type = 'makeReadOnly'; field = 'Business Value' }
            @{ type = 'makeReadOnly'; field = 'Time Criticality' }
            @{ type = 'makeReadOnly'; field = 'Value Area' }
            @{ type = 'makeReadOnly'; field = 'SeniorApprovedBy' }
            @{ type = 'makeReadOnly'; field = 'Senior Approval Status' }
        )
    }
    @{
        name    = 'Require Reason for Removed'
        state   = 'Removed'
        actions = @(
            @{ type = 'setValueToEmpty'; field = 'Description' }
            @{ type = 'makeRequired'; field = 'Description' }
            @{ type = 'makeReadOnly'; field = 'Assigned To' }
            @{ type = 'makeReadOnly'; field = 'Priority' }
            @{ type = 'makeReadOnly'; field = 'Effort' }
            @{ type = 'makeReadOnly'; field = 'Business Value' }
            @{ type = 'makeReadOnly'; field = 'Time Criticality' }
            @{ type = 'makeReadOnly'; field = 'Value Area' }
            @{ type = 'makeReadOnly'; field = 'SeniorApprovedBy' }
            @{ type = 'makeReadOnly'; field = 'Senior Approval Status' }
        )
    }
)

# Refresh rules from this exact process and work item type.
$currentRules = Invoke-RestMethod `
    -Uri $rulesUri `
    -Method Get `
    -Headers $headers `
    -ErrorAction Stop

foreach ($definition in $fieldRuleDefinitions) {
    $ruleActions = @(
        foreach ($action in $definition.actions) {
            @{
                actionType  = $action.type
                targetField = $fieldReferences[$action.field]
                value       = ''
            }
        }
    )

    $missingTargets = @(
        $ruleActions | Where-Object {
            [string]::IsNullOrWhiteSpace($_.targetField)
        }
    )
    if ($missingTargets.Count -gt 0) {
        throw "Rule '$($definition.name)' contains an unresolved target field."
    }

    $rule = @{
        name       = $definition.name
        isDisabled = $false
        conditions = @(
            @{
                conditionType = 'when'
                field         = 'System.State'
                value         = $definition.state
            }
        )
        actions = $ruleActions
    }

    $ruleBody = $rule | ConvertTo-Json -Depth 10
    Write-Host "Request JSON for: $($definition.name)" -ForegroundColor Cyan
    $ruleBody

    if ($ruleBody -match '"(?:conditionType|actionType)"\s*:\s*"\$') {
        throw 'Rule JSON contains an invalid $-prefixed enum value.'
    }

    $matchingRules = @(
        $currentRules.value | Where-Object { $_.name -eq $definition.name }
    )

    if ($matchingRules.Count -gt 1) {
        $matchingRules |
            Format-Table id, name, isDisabled, customizationType, url
        throw "Multiple rules named '$($definition.name)' were found."
    }

    $requestMethod = 'Post'
    $requestUri = $rulesUri
    $operation = 'created'

    if ($matchingRules.Count -eq 1) {
        $existingRule = $matchingRules[0]
        $rulesBaseUri = $rulesUri -replace '\?.*$', ''
        $requestUri = "$rulesBaseUri/$($existingRule.id)?api-version=7.1"
        $requestMethod = 'Put'
        $operation = 'updated and enabled'
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
        Write-Host "Rule ID:  $($result.id)"
        Write-Host "Rule URL: $($result.url)"
    }
    catch {
        $statusCode = 0
        if ($null -ne $_.Exception.Response) {
            $statusCode = [int]$_.Exception.Response.StatusCode
        }

        $responseMessage = $_.ErrorDetails.Message

        # Azure DevOps can return 304 when a PUT payload is identical to the
        # existing rule. Treat it as success and continue with the next rule.
        if ($statusCode -eq 304) {
            Write-Host "Rule already matches; unchanged: $($definition.name)" `
                -ForegroundColor Yellow
            continue
        }

        # Azure DevOps rejects a POST when an equivalent rule already exists,
        # even if that rule is inherited or has a different name. Such a rule
        # may not be updateable through this work item type's endpoint, so
        # report the returned ID and continue.
        if ($statusCode -eq 400 -and $responseMessage -match 'VS1640126') {
            if ($responseMessage -match "Id '([^']+)'") {
                $duplicateRuleId = $Matches[1]
                Write-Host `
                    "Equivalent rule already exists: $($definition.name)" `
                    -ForegroundColor Yellow
                Write-Host "Existing rule ID: $duplicateRuleId"
                continue
            }
        }

        Write-Host "Error saving rule '$($definition.name)': $($_.Exception.Message)" `
            -ForegroundColor Red

        if ($responseMessage) {
            Write-Host "Azure DevOps response: $responseMessage"
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

Microsoft documentation:

- [Fields - List REST API](https://learn.microsoft.com/en-us/rest/api/azure/devops/processes/fields/list?view=azure-devops-rest-7.1)
- [Rules - Add REST API](https://learn.microsoft.com/en-us/rest/api/azure/devops/processes/rules/add?view=azure-devops-rest-7.1)
- [Rules - Update REST API](https://learn.microsoft.com/en-us/rest/api/azure/devops/processes/rules/update?view=azure-devops-rest-7.1)
