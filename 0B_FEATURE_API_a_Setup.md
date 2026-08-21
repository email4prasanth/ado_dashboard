

## Run in PowerShell

```powershell
# Required configuration
$pat = 'YOUR-PERSONAL-ACCESS-TOKEN'
$organization = 'inlai-projects'

# Create the authorization header.
$base64AuthInfo = [Convert]::ToBase64String(
    [Text.Encoding]::ASCII.GetBytes(":$pat")
)
$headers = @{
    Authorization = "Basic $base64AuthInfo"
    Accept        = 'application/json'
}
# API URL to list all processes
$uri = "https://dev.azure.com/$organization/_apis/process/processes?api-version=7.0"

# Make the API call
$result = Invoke-RestMethod -Uri $uri -Headers @{Authorization=("Basic $base64AuthInfo")} -Method Get

# Output the list of processes with their IDs and Names
$result.value | Select-Object name, id, typeId
$processId = $result.value | Where-Object { $_.name -eq "Scrum Hybrid Governance" } | Select-Object -ExpandProperty id

# Discover the real Feature reference name. An inherited Feature commonly uses
# Microsoft.VSTS.WorkItemTypes.Feature rather than ScrumHybridGovernance.Feature.
$witUri = "https://dev.azure.com/$organization/_apis/work/processes/$processId/workItemTypes?api-version=7.1"
$workItemTypes = Invoke-RestMethod -Uri $witUri -Method Get -Headers $headers
$Feature = $workItemTypes.value | Where-Object { $_.name -eq 'Feature' }

if (-not $Feature) {
    $workItemTypes.value | Format-Table name, referenceName, customizationType
    throw 'Feature was not found in the selected process.'
}

$witRefName = $Feature.referenceName
Write-Host "Feature reference name: $witRefName"

# Verify that every state used by the rules exists.
$statesUri = "https://dev.azure.com/$organization/_apis/work/processes/$processId/workItemTypes/$witRefName/states?api-version=7.1"
$states = Invoke-RestMethod -Uri $statesUri -Method Get -Headers $headers
$states.value | Format-Table name, stateCategory, customizationType

$requiredStates = @('New', 'Approved', 'In Progress', 'On Hold', 'Done', 'Removed')
$stateNames = @($states.value.name)
$missingStates = @($requiredStates | Where-Object { $_ -notin $stateNames })

if ($missingStates.Count -gt 0) {
    throw "Missing Feature states: $($missingStates -join ', ')"
}

$rulesUri = "https://dev.azure.com/$organization/_apis/work/processes/$processId/workItemTypes/$witRefName/rules?api-version=7.1"