# az-arm-basic-templates
Brief walkthrough on the ARM templates used by the beginners

## Index for different user-cases
### [Brief Introduction _(Before you start)_](#before-we-start)
### [Resource creation](#resource-creation)
### [Resource creation with parameters](#resource-creation-parameters)
### Creation of a resource using functions
### Creation of a resource using variables
### How to get output for your ARM templates?
### Usage of exported templates
### Usage of quick start templates
### Resource creation with tags
### [External parameter file usage for resources](#external_parameters_reference)

--
--
--
--
--
--
--


### <a name="before-we-start"></a>Brief Introduction (_Before you start_)
To push these ARM templates to our Azure environment, we will rely on **PowerShell** & have the following **Azure Subscription details**, ready before we kick-off. 

:memo: Pointer:

I have shown the simulations for my subscription name. Feel free to use your subscription too :blush:

|Property|Definition|
|---|---|
|**Azure Subscription**|Pay-as-you-go|
|**Subscription Name**|_nags-azure-subscription_|
|**Resource Group Name**|_azure-lab-rg-01_|
|**Powershell Cmdlet**|_New-AzResourceGroupDeployment_|

Check your Powershell environment:

```
PS C:\WINDOWS\system32> (Get-AzContext).Subscription.Name
nags-azure-subscription

PS C:\WINDOWS\system32> (Get-AzContext).Subscription.id
XXXXXXXXX-YYYY-ABCD-EFGH-ABCDEFXYZ722

PS C:\WINDOWS\system32> (Get-AzContext).Subscription.State
Enabled
```

Before we create a new resource (within our resource group) using: _New-AzResourceGroupDeployment_, its advisable to familiarize with this cmdlet. You can choose its help function like given below.

```
PS C:\WINDOWS\system32> help New-AzResourceGroupDeployment -ShowWindow
```
|Property|Definition|
|---|---|
|Folder|[1-foundation-arm-creation](./1-foundation-arm-creation)|
|File|_azuredeploy.json_|

You can define your configuration within the _azuredeploy.json_ file, navigate to the folder where its stored, and then invoke the "WhatIf" parameter to get a preview on the consequences of running the cmdlet (a dry run)

```
PS C:\Users\nagarjun k\Documents\az-journey\arm\a-basic\1-foundation-arm-creation> New-AzResourceGroupDeployment \
-Name "filecheck" -ResourceGroupName "azure-lab-rg-01"  -TemplateFile .\azuredeploy.json -Verbose -WhatIf
What if: Performing the operation "Creating Deployment" on target "azure-lab-rg-01".

```

### <a name="resource-creation"></a>Resource creation
|Property|Definition|
|---|---|
|Folder|[2-resource-creation](./2-resource-creation)|
|File|_azuredeploy.json_|

Run this command to create a simple **storageaccount** in your resource group

**Command:**
```
PS C:\Users\nagarjun k\Documents\az-journey\arm\a-basic\2-resource-creation> New-AzResourceGroupDeployment \
-Name "createresource" -ResourceGroupName "azure-lab-rg-01"  -TemplateFile .\azuredeploy.json -verbose
```

**Output:**
```
VERBOSE: Performing the operation "Creating Deployment" on target "azure-lab-rg-01".
VERBOSE: 16:53:23 - Template is valid.
VERBOSE: 16:53:26 - Create template deployment 'createresource'
VERBOSE: 16:53:26 - Checking deployment status in 5 seconds
VERBOSE: 16:53:31 - Checking deployment status in 5 seconds
VERBOSE: 16:53:37 - Resource Microsoft.Storage/storageAccounts 'storageaccountvsc012' provisioning status is running
VERBOSE: 16:53:37 - Checking deployment status in 14 seconds
VERBOSE: 16:53:51 - Checking deployment status in 5 seconds
VERBOSE: 16:53:57 - Resource Microsoft.Storage/storageAccounts 'storageaccountvsc012' provisioning status is succeeded


DeploymentName          : createresource
ResourceGroupName       : azure-lab-rg-01
ProvisioningState       : Succeeded
Timestamp               : 18-04-2020 11:23:53
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :
```

### <a name="resource-creation-parameters"></a>Resource creation with parameters
|Property|Definition|
|---|---|
|Folder|[3-resource-creation-with-parameter](./3-resource-creation-with-parameter)|
|File|_azuredeploy-with-config-params.json_|

azuredeploy-with-config-params.json
Run this command to create a simple **storageaccount** by using a custom file: _azuredeploy-with-config-params.json_, where you are defining a few parameters for resource creation (ex: _account name, sku, location_)

**Command:**
```
PS C:\Users\nagarjun k\Documents\az-journey\arm\a-basic\3-resource-creation-with-parameter> New-AzResourceGroupDeployment \
-Name "resourcewithparameter" -ResourceGroupName "azure-lab-rg-01"  -TemplateFile .\azuredeploy-with-config-params.json \
-verbose
```

**Output:**
```
VERBOSE: Performing the operation "Creating Deployment" on target "azure-lab-rg-01".
VERBOSE: 16:59:10 - Template is valid.
VERBOSE: 16:59:12 - Create template deployment 'resourcewithparameter'
VERBOSE: 16:59:12 - Checking deployment status in 5 seconds
VERBOSE: 16:59:18 - Resource Microsoft.Storage/storageAccounts 'vscstorageaccount0123' provisioning status is running
VERBOSE: 16:59:18 - Checking deployment status in 17 seconds
VERBOSE: 16:59:35 - Checking deployment status in 5 seconds
VERBOSE: 16:59:41 - Resource Microsoft.Storage/storageAccounts 'vscstorageaccount0123' provisioning status is succeeded


DeploymentName          : resourcewithparameter
ResourceGroupName       : azure-lab-rg-01
ProvisioningState       : Succeeded
Timestamp               : 18-04-2020 11:29:40
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          storageName      String                     vscstorageaccount0123
                          storageSKU       String                     Standard_LRS
                          location         String                     southindia

Outputs                 :
DeploymentDebugLogLevel :
```



### <a name="external_parameters_reference"></a>External parameter file usage for resources
