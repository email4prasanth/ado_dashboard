# Azure DevOps API rules: Epic state transitions

These rules restrict the destination states available after an Epic moves
from each workflow state.

## Azure DevOps UI configuration

For each row below:

1. Create the rule using the name in the **Rule name** column.
2. Under **When**, select **A work item state moved from...** and choose the
   **Source state**.
3. Under **Then**, select **Restrict the state transition to...** once for
   every state in **Not allowed**.

| Rule name | Source state | Allowed | Not allowed |
| --- | --- | --- | --- |
| **Restrict From New** | New | Approved, Removed | New, In Progress, On Hold, Done |
| **Restrict From Approved** | Approved | In Progress, Removed | New, Approved, On Hold, Done |
| **Restrict From In Progress** | In Progress | On Hold, Done, Removed | New, Approved, In Progress |
| **Restrict From On Hold** | On Hold | In Progress, Removed | New, Approved, On Hold, Done |
| **Restrict From Done** | Done | None (end state) | New, Approved, In Progress, On Hold, Done, Removed |
| **Restrict From Removed** | Removed | None (end state) | New, Approved, In Progress, On Hold, Done, Removed |

## Epic field validation rule

### 7. Require Description for New

1. Under **When**, select **A work item state changes to...** and choose
   **New**.
2. Under **Then**, select **Make required...** and choose **Description**.

The REST request uses `whenStateChangedTo` for the condition and
`makeRequired` for the `System.Description` field. Use these bare enum values
without a `$` prefix.

## Run in PowerShell

```powershell
# Required configuration
$pat = 'YOUR-PERSONAL-ACCESS-TOKEN'
$organization = 'inlai-projects'
$processId = 'f930013b-904c-435a-8630-d04432eedac9'

# Create the authorization header.
$base64AuthInfo = [Convert]::ToBase64String(
    [Text.Encoding]::ASCII.GetBytes(":$pat")
)
$headers = @{
    Authorization = "Basic $base64AuthInfo"
    Accept        = 'application/json'
}

# Discover the real Epic reference name. An inherited Epic commonly uses
# Microsoft.VSTS.WorkItemTypes.Epic rather than ScrumHybridGovernance.Epic.
$witUri = "https://dev.azure.com/$organization/_apis/work/processes/$processId/workItemTypes?api-version=7.1"
$workItemTypes = Invoke-RestMethod -Uri $witUri -Method Get -Headers $headers
$epic = $workItemTypes.value | Where-Object { $_.name -eq 'Epic' }

if (-not $epic) {
    $workItemTypes.value | Format-Table name, referenceName, customizationType
    throw 'Epic was not found in the selected process.'
}

$witRefName = $epic.referenceName
Write-Host "Epic reference name: $witRefName"

# Verify that every state used by the rules exists.
$statesUri = "https://dev.azure.com/$organization/_apis/work/processes/$processId/workItemTypes/$witRefName/states?api-version=7.1"
$states = Invoke-RestMethod -Uri $statesUri -Method Get -Headers $headers
$states.value | Format-Table name, stateCategory, customizationType

$requiredStates = @('New', 'Approved', 'In Progress', 'On Hold', 'Done', 'Removed')
$stateNames = @($states.value.name)
$missingStates = @($requiredStates | Where-Object { $_ -notin $stateNames })

if ($missingStates.Count -gt 0) {
    throw "Missing Epic states: $($missingStates -join ', ')"
}

$rulesUri = "https://dev.azure.com/$organization/_apis/work/processes/$processId/workItemTypes/$witRefName/rules?api-version=7.1"