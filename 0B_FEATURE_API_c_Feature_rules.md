# Feature field-validation API rules

This document creates or updates Feature rules 8–14. The PowerShell resolves
each field's Azure DevOps reference name before building the rule payload.

The actual workflow state is **Ready to Release**. That exact name is used in
the API condition even where earlier notes said “Ready for Release.”

For these REST requests, the UI condition **A work item state changes to...**
is represented by `conditionType = 'when'`, `field = 'System.State'`, and the
state display name as `value`.

## Rule definitions

### 8. Require Description for New

- When state is **New**
- Make **Description** required

### 9. Require Business Case for Approved

- When state is **Approved**
- Make **Assigned To**, **SeniorApprovedBy**, **Senior Approval Status**, and
  **Business Value** required

### 10. Require Rules for In Progress

- When state is **In Progress**
- Clear **Priority** and **Time Criticality**
- Make **Priority**, **Time Criticality**, and **Effort** required
- Make **Assigned To**, **SeniorApprovedBy**, **Senior Approval Status**, and
  **Business Value** read-only

### 11. Require Reason for On Hold

- When state is **On Hold**
- Clear **Description**, then make it required
- Make **Assigned To**, **Priority**, **Time Criticality**,
  **SeniorApprovedBy**, **Senior Approval Status**, **Effort**, **Value Area**,
  and **Business Value** read-only

### 12. Require Rules for Ready to Release

- When state is **Ready to Release**
- Clear **Description**
- Make **Description** and **Acceptance Criteria** required
- Make **Assigned To**, **Priority**, **Effort**, **Business Value**,
  **Time Criticality**, **Value Area**, **SeniorApprovedBy**, and
  **Senior Approval Status** read-only

### 13. Require Rules for Completed

- When state is **Completed**
- Make **Acceptance Criteria** required
- Clear **Description**, then make it required
- Make **Assigned To**, **Priority**, **Effort**, **Business Value**,
  **Time Criticality**, **Value Area**, **SeniorApprovedBy**, and
  **Senior Approval Status** read-only

### 14. Require Reason for Removed

- When state is **Removed**
- Clear **Description**
- Make **Description** and **Removal Reason** required
- Make **Assigned To**, **Priority**, **Effort**, **Business Value**,
  **Time Criticality**, **Value Area**, **SeniorApprovedBy**, and
  **Senior Approval Status** read-only

Azure DevOps permits at most 10 actions in one rule. Rules 12–14 therefore use
a primary rule and a `(continued)` rule with the same state condition. The two
parts together apply every action listed above.

If a requested field already exists in the organization but is not attached
to Feature, the script attaches it to Feature before creating the rules. This
is required for **Removal Reason** when that field is missing from Feature.

## Run in PowerShell

First run `0B_FEATURE_API_a_Setup.md`. Then run this entire block in the same
PowerShell session. Existing rules with matching names are updated and enabled;
missing rules are created.

```powershell
# Derive the Feature fields endpoint from the rules endpoint created by setup.
Write-Host "Target rules endpoint: $rulesUri" -ForegroundColor Cyan
$witBaseUri = $rulesUri -replace '/rules\?.*$', ''
$fieldsUri = "$witBaseUri/fields?api-version=7.1"

# Refresh states and fields to avoid stale values from earlier runs.
$states = Invoke-RestMethod `
    -Uri $statesUri `
    -Method Get `
    -Headers $headers `
    -ErrorAction Stop

$fieldsResponse = Invoke-RestMethod `
    -Uri $fieldsUri `
    -Method Get `
    -Headers $headers `
    -ErrorAction Stop

$organizationFieldsUri = "https://dev.azure.com/$organization/_apis/wit/fields?api-version=7.1"
$organizationFieldsResponse = Invoke-RestMethod `
    -Uri $organizationFieldsUri `
    -Method Get `
    -Headers $headers `
    -ErrorAction Stop

$featureFields = @($fieldsResponse.value)
$organizationFields = @($organizationFieldsResponse.value)
if ($featureFields.Count -eq 0) {
    throw "No Feature fields were returned by '$fieldsUri'."
}

function Resolve-FeatureFieldReference {
    param(
        [Parameter(Mandatory)]
        [string]$DisplayName,

        [Parameter(Mandatory)]
        [object[]]$AvailableFields,

        [Parameter(Mandatory)]
        [object[]]$OrganizationFields
    )

    $matches = @(
        $AvailableFields | Where-Object { $_.name -eq $DisplayName }
    )

    if ($matches.Count -eq 0) {
        Write-Host 'Available Feature fields:' -ForegroundColor Yellow
        $AvailableFields |
            Sort-Object name |
            Format-Table name, referenceName, type, required, readOnly
        throw "Feature field '$DisplayName' was not found."
    }

    if ($matches.Count -gt 1) {
        $matches | Format-Table name, referenceName, type
        throw "Multiple Feature fields named '$DisplayName' were found."
    }

    $referenceName = [string]$matches[0].referenceName

    # Some process-field responses contain the display name but omit the
    # reference name for custom fields. Resolve the canonical identifier from
    # the organization-wide field definitions in that case.
    if ([string]::IsNullOrWhiteSpace($referenceName)) {
        $organizationMatches = @(
            $OrganizationFields |
                Where-Object { $_.name -eq $DisplayName }
        )

        if ($organizationMatches.Count -gt 1) {
            $organizationMatches |
                Format-Table name, referenceName, type
            throw "Multiple organization fields named '$DisplayName' were found."
        }

        if ($organizationMatches.Count -eq 1) {
            $referenceName = [string]$organizationMatches[0].referenceName
        }
    }

    if ([string]::IsNullOrWhiteSpace($referenceName)) {
        $fieldJson = $matches[0] | ConvertTo-Json -Depth 10 -Compress
        throw "Feature field '$DisplayName' has no reference name. Field response: $fieldJson"
    }

    Write-Host "$DisplayName -> $referenceName" -ForegroundColor DarkCyan
    return $referenceName
}

$requiredStates = @(
    'New',
    'Approved',
    'In Progress',
    'On Hold',
    'Ready to Release',
    'Completed',
    'Removed'
)

$stateNames = @($states.value.name)
$missingStates = @(
    $requiredStates | Where-Object { $_ -notin $stateNames }
)

if ($missingStates.Count -gt 0) {
    throw "Missing Feature states: $($missingStates -join ', ')"
}

# Rules 12–14 are split because Azure DevOps allows at most 10 actions per rule.
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
            @{ type = 'makeRequired'; field = 'Business Value' }
        )
    }
    @{
        name    = 'Require Rules for In Progress'
        state   = 'In Progress'
        actions = @(
            @{ type = 'setValueToEmpty'; field = 'Priority' }
            @{ type = 'setValueToEmpty'; field = 'Time Criticality' }
            @{ type = 'makeRequired'; field = 'Priority' }
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
        name    = 'Require Rules for Ready to Release'
        state   = 'Ready to Release'
        actions = @(
            @{ type = 'setValueToEmpty'; field = 'Description' }
            @{ type = 'makeRequired'; field = 'Description' }
            @{ type = 'makeRequired'; field = 'Acceptance Criteria' }
            @{ type = 'makeReadOnly'; field = 'Assigned To' }
            @{ type = 'makeReadOnly'; field = 'Priority' }
            @{ type = 'makeReadOnly'; field = 'Effort' }
            @{ type = 'makeReadOnly'; field = 'Business Value' }
            @{ type = 'makeReadOnly'; field = 'Time Criticality' }
            @{ type = 'makeReadOnly'; field = 'Value Area' }
            @{ type = 'makeReadOnly'; field = 'SeniorApprovedBy' }
        )
    }
    @{
        name    = 'Require Rules for Ready to Release (continued)'
        state   = 'Ready to Release'
        actions = @(
            @{ type = 'makeReadOnly'; field = 'Senior Approval Status' }
        )
    }
    @{
        name    = 'Require Rules for Completed'
        state   = 'Completed'
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
        )
    }
    @{
        name    = 'Require Rules for Completed (continued)'
        state   = 'Completed'
        actions = @(
            @{ type = 'makeReadOnly'; field = 'Senior Approval Status' }
        )
    }
    @{
        name    = 'Require Reason for Removed'
        state   = 'Removed'
        actions = @(
            @{ type = 'setValueToEmpty'; field = 'Description' }
            @{ type = 'makeRequired'; field = 'Description' }
            @{ type = 'makeRequired'; field = 'Removal Reason' }
            @{ type = 'makeReadOnly'; field = 'Assigned To' }
            @{ type = 'makeReadOnly'; field = 'Priority' }
            @{ type = 'makeReadOnly'; field = 'Effort' }
            @{ type = 'makeReadOnly'; field = 'Business Value' }
            @{ type = 'makeReadOnly'; field = 'Time Criticality' }
            @{ type = 'makeReadOnly'; field = 'Value Area' }
            @{ type = 'makeReadOnly'; field = 'SeniorApprovedBy' }
        )
    }
    @{
        name    = 'Require Reason for Removed (continued)'
        state   = 'Removed'
        actions = @(
            @{ type = 'makeReadOnly'; field = 'Senior Approval Status' }
        )
    }
)

# Derive the required field list from the rules themselves. This prevents a
# stale, separately maintained field-name list from omitting Removal Reason or
# another action target.
$requiredFieldNames = @(
    $fieldRuleDefinitions |
        ForEach-Object { $_.actions } |
        ForEach-Object { $_.field } |
        Sort-Object -Unique
)

# Attach requested organization fields that are not yet part of Feature.
# The Process API requires the organization's canonical referenceName.
foreach ($fieldName in $requiredFieldNames) {
    $featureMatches = @(
        $featureFields | Where-Object { $_.name -eq $fieldName }
    )

    if ($featureMatches.Count -gt 0) {
        continue
    }

    $organizationMatches = @(
        $organizationFields | Where-Object { $_.name -eq $fieldName }
    )

    if ($organizationMatches.Count -eq 0) {
        throw "Field '$fieldName' is not attached to Feature and does not exist in the organization."
    }

    if ($organizationMatches.Count -gt 1) {
        $organizationMatches | Format-Table name, referenceName, type
        throw "Multiple organization fields named '$fieldName' were found."
    }

    $fieldReferenceName = [string]$organizationMatches[0].referenceName
    if ([string]::IsNullOrWhiteSpace($fieldReferenceName)) {
        throw "Organization field '$fieldName' has no reference name."
    }

    $addFieldBody = @{
        referenceName = $fieldReferenceName
        defaultValue  = ''
        allowGroups   = $false
    } | ConvertTo-Json -Depth 5

    $addedField = Invoke-RestMethod `
        -Uri $fieldsUri `
        -Method Post `
        -Headers $headers `
        -ContentType 'application/json' `
        -Body $addFieldBody `
        -ErrorAction Stop

    Write-Host "Field attached to Feature: $($addedField.name) -> $($addedField.referenceName)" `
        -ForegroundColor Green
}

# Refresh after attachments so all resolution uses the current Feature schema.
$fieldsResponse = Invoke-RestMethod `
    -Uri $fieldsUri `
    -Method Get `
    -Headers $headers `
    -ErrorAction Stop
$featureFields = @($fieldsResponse.value)

$fieldReferences = @{}
foreach ($fieldName in $requiredFieldNames) {
    $fieldReferences[$fieldName] = Resolve-FeatureFieldReference `
        -DisplayName $fieldName `
        -AvailableFields $featureFields `
        -OrganizationFields $organizationFields
}

Write-Host 'Resolved Feature field references:' -ForegroundColor Cyan
$fieldReferences.GetEnumerator() |
    Sort-Object Name |
    Format-Table Name, Value

$currentRules = Invoke-RestMethod `
    -Uri $rulesUri `
    -Method Get `
    -Headers $headers `
    -ErrorAction Stop

foreach ($definition in $fieldRuleDefinitions) {
    if ($definition.actions.Count -gt 10) {
        throw "Rule '$($definition.name)' exceeds the 10-action limit."
    }

    $unresolvedFieldNames = @(
        $definition.actions |
            Where-Object {
                [string]::IsNullOrWhiteSpace(
                    [string]$fieldReferences[$_.field]
                )
            } |
            ForEach-Object { $_.field } |
            Sort-Object -Unique
    )

    if ($unresolvedFieldNames.Count -gt 0) {
        throw "Rule '$($definition.name)' has unresolved fields: $($unresolvedFieldNames -join ', ')"
    }

    $ruleActions = @(
        foreach ($action in $definition.actions) {
            @{
                actionType  = $action.type
                targetField = $fieldReferences[$action.field]
                value       = ''
            }
        }
    )

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
        Write-Host "Rule ID: $($result.id)"
    }
    catch {
        $statusCode = 0
        if ($null -ne $_.Exception.Response) {
            $statusCode = [int]$_.Exception.Response.StatusCode
        }

        $responseMessage = $_.ErrorDetails.Message

        if ($statusCode -eq 304) {
            Write-Host "Rule already matches; unchanged: $($definition.name)" `
                -ForegroundColor Yellow
            continue
        }

        if ($statusCode -eq 400 -and $responseMessage -match 'VS1640126') {
            Write-Host "Equivalent rule already exists: $($definition.name)" `
                -ForegroundColor Yellow
            Write-Host $responseMessage
            continue
        }

        Write-Host "Error saving rule '$($definition.name)': $($_.Exception.Message)" `
            -ForegroundColor Red

        if ($responseMessage) {
            Write-Host "Azure DevOps response: $responseMessage"
        }

        throw
    }
}
```

Azure DevOps limits custom rules to 10 actions: [work tracking process and
project limits](https://learn.microsoft.com/en-us/azure/devops/organizations/settings/work/object-limits?view=azure-devops).
