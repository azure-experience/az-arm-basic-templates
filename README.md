# az-arm-basic-templates
Brief walkthrough on the ARM templates used by the beginners

## Index for different user-cases
### [Brief Introduction](#before-we-start)
### [Creation of a resource](#resource-creation)
### Creation of a resource with a parameter
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


### <a name="before-we-start"></a>Brief Introduction
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

Additionally, you can define your configuration within the _azuredeploy.json_ file, navigate to the folder where its stored, and then invoke the "WhatIf" parameter to get a preview on the consequences of running the cmdlet (a dry run)

```
PS C:\Users\nagarjun k\Documents\az-journey\arm\a-basic\1-foundation-arm-creation> New-AzResourceGroupDeployment \
-Name "filecheck" -ResourceGroupName "azure-lab-rg-01"  -TemplateFile .\azuredeploy.json -Verbose -WhatIf
What if: Performing the operation "Creating Deployment" on target "azure-lab-rg-01".

```

### <a name="resource-creation"></a>Resource creation
|Property|Definition|
|---|---|
|Folder|


...


...



### <a name="external_parameters_reference"></a>External parameter file usage for resources
