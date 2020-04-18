# az-arm-basic-templates
Brief walkthrough on the ARM templates used by the beginners

## Index for different user-cases
### [Brief Introduction](#introduction)
### Creation of a resource
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


### <a name="introduction"></a>Brief Introduction
To push these ARM templates to our Azure environment, we will rely on **PowerShell** & have the following **Azure Subscription details**, ready before we kick-off.

|Property|Definition|
|---|---|
|Azure Subscription|Pay-as-you-go|
|Subscription Name|nags-azure-subscription|
|Resource Group Name|azure-lab-rg-01|

Check your Powershell environment:

```
PS C:\WINDOWS\system32> (Get-AzContext).Subscription.Name
nags-azure-subscription

PS C:\WINDOWS\system32> (Get-AzContext).Subscription.id
XXXXXXXXX-YYYY-ABCD-EFGH-ABCDEFXYZ722

PS C:\WINDOWS\system32> (Get-AzContext).Subscription.State
Enabled
```


...


...



### <a name="external_parameters_reference"></a>External parameter file usage for resources
