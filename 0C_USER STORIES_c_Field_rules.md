# User Story field-validation API rules

This document creates or updates User Story field rules 15–28. It resolves
field reference names, attaches requested organization fields when they are
missing from the User Story type, and enables every saved rule.

The UI condition **A work item state changes to...** is represented by
`conditionType = 'when'` with `field = 'System.State'`.

## Rules

| No. | Rule | State | Actions |
| ---: | --- | --- | --- |
| 15 | Require Description for New | New | Require Description |
| 16 | Require Rules for Refinement | Refinement | Require Description, Acceptance Criteria, Priority |
| 17 | Require Rules for Ready for Dev | Ready for Dev | Require Assigned To, Priority, Effort; read-only SeniorApprovedBy, Senior Approval Status |
| 18 | Require Rules for Dev In Progress | Dev In Progress | Clear Priority and Time Criticality; require Priority, Time Criticality, Effort; read-only Assigned To, SeniorApprovedBy, Senior Approval Status, Business Value |
| 19 | Require Rules for Code Review | Code Review | Require Description, Acceptance Criteria, Priority; read-only Assigned To, Effort, Time Criticality, SeniorApprovedBy, Senior Approval Status |
| 20 | Require Rules for Ready for QA | Ready for QA | Require Acceptance Criteria, Priority; read-only Assigned To, Effort, Time Criticality, SeniorApprovedBy, Senior Approval Status |
| 21 | Require Rules for QA In Progress | QA In Progress | Require Description, Acceptance Criteria, Priority; read-only Assigned To, Effort, Time Criticality, SeniorApprovedBy, Senior Approval Status |
| 22 | Require Reason for On Hold | On Hold | Clear and require Description; read-only Assigned To, Priority, Time Criticality, SeniorApprovedBy, Senior Approval Status, Effort, Value Area, Business Value |
| 23 | Require Rules for Ready for Pre-prod | Ready for Pre-prod | Require Description, Acceptance Criteria, Priority; read-only Assigned To, Effort, Time Criticality, SeniorApprovedBy, Senior Approval Status, Business Value |
| 24 | Require Rules for Ready for Prod | Ready for Prod | Require Description, Acceptance Criteria; read-only Assigned To, Priority, Effort, Time Criticality, SeniorApprovedBy, Senior Approval Status, Business Value, Value Area |
| 25 | Require Rules for Closed | Closed | Require Acceptance Criteria; clear and require Description; read-only Assigned To, Priority, Effort, Business Value, Time Criticality, Value Area, SeniorApprovedBy, Senior Approval Status |
| 26 | Require Rules for Reopened | Reopened | Clear Description; require Description, Priority, Reopen Reason; read-only Assigned To, Effort, Time Criticality, SeniorApprovedBy, Senior Approval Status, Business Value |
| 27 | Require Reason for Rejected | Rejected | Clear Description; require Description, Rejection Reason; read-only Assigned To, Priority, Effort, Business Value, Time Criticality, Value Area, SeniorApprovedBy, Senior Approval Status |
| 28 | Require Reason for Deferred | Deferred | Clear Description; require Description, Deferral Reason, Priority; read-only Assigned To, Effort, Business Value, Time Criticality, Value Area, SeniorApprovedBy, Senior Approval Status |

Azure DevOps permits at most 10 actions per rule. The script automatically
creates a `(continued)` rule for definitions containing 11 actions.

## Run in PowerShell

First run `0C_USER STORIES_a_setup.md`, then run this entire block in the same
PowerShell session.

```powershell
# Derive endpoints and refresh states and fields.
$witBaseUri = $rulesUri -replace '/rules\?.*$', ''
$fieldsUri = "$witBaseUri/fields?api-version=7.1"
$organizationFieldsUri = "https://dev.azure.com/$organization/_apis/wit/fields?api-version=7.1"

$states = Invoke-RestMethod `
    -Uri $statesUri -Method Get -Headers $headers -ErrorAction Stop
$fieldsResponse = Invoke-RestMethod `
    -Uri $fieldsUri -Method Get -Headers $headers -ErrorAction Stop
$organizationFieldsResponse = Invoke-RestMethod `
    -Uri $organizationFieldsUri -Method Get -Headers $headers -ErrorAction Stop

$userStoryFields = @($fieldsResponse.value)
$organizationFields = @($organizationFieldsResponse.value)

if ($userStoryFields.Count -eq 0) {
    throw "No User Story fields were returned by '$fieldsUri'."
}

$requiredStates = @(
    'New', 'Refinement', 'Ready for Dev', 'Dev In Progress', 'Code Review',
    'Ready for QA', 'QA In Progress', 'On Hold', 'Ready for Pre-prod',
    'Ready for Prod', 'Closed', 'Reopened', 'Rejected', 'Deferred'
)
$stateNames = @($states.value.name)
$missingStates = @($requiredStates | Where-Object { $_ -notin $stateNames })
if ($missingStates.Count -gt 0) {
    throw "Missing User Story states: $($missingStates -join ', ')"
}

# Logical rule definitions. REST action names must not include a $ prefix.
$logicalRules = @(
    @{
        name = 'Require Description for New'; state = 'New'; actions = @(
            @{ type = 'makeRequired'; field = 'Description' }
        )
    }
    @{
        name = 'Require Rules for Refinement'; state = 'Refinement'; actions = @(
            @{ type = 'makeRequired'; field = 'Description' }
            @{ type = 'makeRequired'; field = 'Acceptance Criteria' }
            @{ type = 'makeRequired'; field = 'Priority' }
        )
    }
    @{
        name = 'Require Rules for Ready for Dev'; state = 'Ready for Dev'; actions = @(
            @{ type = 'makeRequired'; field = 'Assigned To' }
            @{ type = 'makeRequired'; field = 'Priority' }
            @{ type = 'makeRequired'; field = 'Effort' }
            @{ type = 'makeReadOnly'; field = 'SeniorApprovedBy' }
            @{ type = 'makeReadOnly'; field = 'Senior Approval Status' }
        )
    }
    @{
        name = 'Require Rules for Dev In Progress'; state = 'Dev In Progress'; actions = @(
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
        name = 'Require Rules for Code Review'; state = 'Code Review'; actions = @(
            @{ type = 'makeRequired'; field = 'Description' }
            @{ type = 'makeRequired'; field = 'Acceptance Criteria' }
            @{ type = 'makeRequired'; field = 'Priority' }
            @{ type = 'makeReadOnly'; field = 'Assigned To' }
            @{ type = 'makeReadOnly'; field = 'Effort' }
            @{ type = 'makeReadOnly'; field = 'Time Criticality' }
            @{ type = 'makeReadOnly'; field = 'SeniorApprovedBy' }
            @{ type = 'makeReadOnly'; field = 'Senior Approval Status' }
        )
    }
    @{
        name = 'Require Rules for Ready for QA'; state = 'Ready for QA'; actions = @(
            @{ type = 'makeRequired'; field = 'Acceptance Criteria' }
            @{ type = 'makeRequired'; field = 'Priority' }
            @{ type = 'makeReadOnly'; field = 'Assigned To' }
            @{ type = 'makeReadOnly'; field = 'Effort' }
            @{ type = 'makeReadOnly'; field = 'Time Criticality' }
            @{ type = 'makeReadOnly'; field = 'SeniorApprovedBy' }
            @{ type = 'makeReadOnly'; field = 'Senior Approval Status' }
        )
    }
    @{
        name = 'Require Rules for QA In Progress'; state = 'QA In Progress'; actions = @(
            @{ type = 'makeRequired'; field = 'Description' }
            @{ type = 'makeRequired'; field = 'Acceptance Criteria' }
            @{ type = 'makeRequired'; field = 'Priority' }
            @{ type = 'makeReadOnly'; field = 'Assigned To' }
            @{ type = 'makeReadOnly'; field = 'Effort' }
            @{ type = 'makeReadOnly'; field = 'Time Criticality' }
            @{ type = 'makeReadOnly'; field = 'SeniorApprovedBy' }
            @{ type = 'makeReadOnly'; field = 'Senior Approval Status' }
        )
    }
    @{
        name = 'Require Reason for On Hold'; state = 'On Hold'; actions = @(
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
        name = 'Require Rules for Ready for Pre-prod'; state = 'Ready for Pre-prod'; actions = @(
            @{ type = 'makeRequired'; field = 'Description' }
            @{ type = 'makeRequired'; field = 'Acceptance Criteria' }
            @{ type = 'makeRequired'; field = 'Priority' }
            @{ type = 'makeReadOnly'; field = 'Assigned To' }
            @{ type = 'makeReadOnly'; field = 'Effort' }
            @{ type = 'makeReadOnly'; field = 'Time Criticality' }
            @{ type = 'makeReadOnly'; field = 'SeniorApprovedBy' }
            @{ type = 'makeReadOnly'; field = 'Senior Approval Status' }
            @{ type = 'makeReadOnly'; field = 'Business Value' }
        )
    }
    @{
        name = 'Require Rules for Ready for Prod'; state = 'Ready for Prod'; actions = @(
            @{ type = 'makeRequired'; field = 'Description' }
            @{ type = 'makeRequired'; field = 'Acceptance Criteria' }
            @{ type = 'makeReadOnly'; field = 'Assigned To' }
            @{ type = 'makeReadOnly'; field = 'Priority' }
            @{ type = 'makeReadOnly'; field = 'Effort' }
            @{ type = 'makeReadOnly'; field = 'Time Criticality' }
            @{ type = 'makeReadOnly'; field = 'SeniorApprovedBy' }
            @{ type = 'makeReadOnly'; field = 'Senior Approval Status' }
            @{ type = 'makeReadOnly'; field = 'Business Value' }
            @{ type = 'makeReadOnly'; field = 'Value Area' }
        )
    }
    @{
        name = 'Require Rules for Closed'; state = 'Closed'; actions = @(
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
        name = 'Require Rules for Reopened'; state = 'Reopened'; actions = @(
            @{ type = 'setValueToEmpty'; field = 'Description' }
            @{ type = 'makeRequired'; field = 'Description' }
            @{ type = 'makeRequired'; field = 'Priority' }
            @{ type = 'makeRequired'; field = 'Reopen Reason' }
            @{ type = 'makeReadOnly'; field = 'Assigned To' }
            @{ type = 'makeReadOnly'; field = 'Effort' }
            @{ type = 'makeReadOnly'; field = 'Time Criticality' }
            @{ type = 'makeReadOnly'; field = 'SeniorApprovedBy' }
            @{ type = 'makeReadOnly'; field = 'Senior Approval Status' }
            @{ type = 'makeReadOnly'; field = 'Business Value' }
        )
    }
    @{
        name = 'Require Reason for Rejected'; state = 'Rejected'; actions = @(
            @{ type = 'setValueToEmpty'; field = 'Description' }
            @{ type = 'makeRequired'; field = 'Description' }
            @{ type = 'makeRequired'; field = 'Rejection Reason' }
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
        name = 'Require Reason for Deferred'; state = 'Deferred'; actions = @(
            @{ type = 'setValueToEmpty'; field = 'Description' }
            @{ type = 'makeRequired'; field = 'Description' }
            @{ type = 'makeRequired'; field = 'Deferral Reason' }
            @{ type = 'makeRequired'; field = 'Priority' }
            @{ type = 'makeReadOnly'; field = 'Assigned To' }
            @{ type = 'makeReadOnly'; field = 'Effort' }
            @{ type = 'makeReadOnly'; field = 'Business Value' }
            @{ type = 'makeReadOnly'; field = 'Time Criticality' }
            @{ type = 'makeReadOnly'; field = 'Value Area' }
            @{ type = 'makeReadOnly'; field = 'SeniorApprovedBy' }
            @{ type = 'makeReadOnly'; field = 'Senior Approval Status' }
        )
    }
)

# Split logical rules into API rules of at most 10 actions.
$apiRules = @(
    foreach ($logicalRule in $logicalRules) {
        for ($offset = 0; $offset -lt $logicalRule.actions.Count; $offset += 10) {
            $last = [Math]::Min($offset + 9, $logicalRule.actions.Count - 1)
            $partName = $logicalRule.name
            if ($offset -gt 0) {
                $partName = "$($logicalRule.name) (continued)"
            }

            @{
                name = $partName
                state = $logicalRule.state
                actions = @($logicalRule.actions[$offset..$last])
            }
        }
    }
)

# Derive every required field from the actions.
$requiredFieldNames = @(
    $logicalRules | ForEach-Object { $_.actions } |
        ForEach-Object { $_.field } | Sort-Object -Unique
)

# Attach organization fields that are requested but missing from User Story.
foreach ($fieldName in $requiredFieldNames) {
    if (@($userStoryFields | Where-Object { $_.name -eq $fieldName }).Count -gt 0) {
        continue
    }

    $organizationMatches = @(
        $organizationFields | Where-Object { $_.name -eq $fieldName }
    )
    if ($organizationMatches.Count -ne 1) {
        throw "Field '$fieldName' is missing from User Story and could not be uniquely resolved in the organization."
    }

    $addFieldBody = @{
        referenceName = [string]$organizationMatches[0].referenceName
        defaultValue = ''
        allowGroups = $false
    } | ConvertTo-Json -Depth 5

    $addedField = Invoke-RestMethod `
        -Uri $fieldsUri -Method Post -Headers $headers `
        -ContentType 'application/json' -Body $addFieldBody -ErrorAction Stop
    Write-Host "Field attached: $($addedField.name)" -ForegroundColor Green
}

# Refresh fields after attachment.
$fieldsResponse = Invoke-RestMethod `
    -Uri $fieldsUri -Method Get -Headers $headers -ErrorAction Stop
$userStoryFields = @($fieldsResponse.value)

# Resolve canonical reference names, with an organization-level fallback.
$fieldReferences = @{}
foreach ($fieldName in $requiredFieldNames) {
    $witMatch = @($userStoryFields | Where-Object { $_.name -eq $fieldName })
    if ($witMatch.Count -ne 1) {
        throw "Expected one User Story field named '$fieldName'; found $($witMatch.Count)."
    }

    $referenceName = [string]$witMatch[0].referenceName
    if ([string]::IsNullOrWhiteSpace($referenceName)) {
        $orgMatch = @($organizationFields | Where-Object { $_.name -eq $fieldName })
        if ($orgMatch.Count -eq 1) {
            $referenceName = [string]$orgMatch[0].referenceName
        }
    }

    if ([string]::IsNullOrWhiteSpace($referenceName)) {
        throw "Field '$fieldName' has no reference name."
    }
    $fieldReferences[$fieldName] = $referenceName
    Write-Host "$fieldName -> $referenceName" -ForegroundColor DarkCyan
}

$currentRules = Invoke-RestMethod `
    -Uri $rulesUri -Method Get -Headers $headers -ErrorAction Stop

foreach ($definition in $apiRules) {
    if ($definition.actions.Count -gt 10) {
        throw "Rule '$($definition.name)' exceeds the 10-action limit."
    }

    $unresolvedFields = @(
        $definition.actions |
            Where-Object {
                [string]::IsNullOrWhiteSpace(
                    [string]$fieldReferences[$_.field]
                )
            } |
            ForEach-Object { $_.field } |
            Sort-Object -Unique
    )

    if ($unresolvedFields.Count -gt 0) {
        throw "Rule '$($definition.name)' has unresolved target fields: $($unresolvedFields -join ', '). Run the entire block from the first line."
    }

    $ruleActions = @(
        foreach ($action in $definition.actions) {
            @{
                actionType = $action.type
                targetField = $fieldReferences[$action.field]
                value = ''
            }
        }
    )

    $rule = @{
        name = $definition.name
        isDisabled = $false
        conditions = @(
            @{ conditionType = 'when'; field = 'System.State'; value = $definition.state }
        )
        actions = $ruleActions
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
        $rulesBaseUri = $rulesUri -replace '\?.*$', ''
        $requestUri = "$rulesBaseUri/$($matchingRules[0].id)?api-version=7.1"
        $requestMethod = 'Put'
        $operation = 'updated and enabled'
    }

    $ruleBody = $rule | ConvertTo-Json -Depth 10

    $missingTargets = @(
        $rule.actions | Where-Object {
            [string]::IsNullOrWhiteSpace([string]$_.targetField)
        }
    )
    if ($missingTargets.Count -gt 0) {
        throw "Rule '$($definition.name)' contains an empty action.targetField."
    }

    Write-Host "Request JSON for: $($definition.name)" -ForegroundColor Cyan
    $ruleBody

    try {
        $result = Invoke-RestMethod `
            -Uri $requestUri -Method $requestMethod -Headers $headers `
            -ContentType 'application/json' -Body $ruleBody -ErrorAction Stop
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
