# az-arm-basic-templates
Brief walkthrough on the ARM templates used by the beginners

## Index for different user-cases
### [1. Brief Introduction _(Before you start)_](#before-we-start)
### [2. Resource creation](#new-resource-creation)
### [3. Resource creation with parameters](#resource-creation-parameters)
### [4. Resource creation with functions](#resource-creation-functions)
### [5. Resource creation with variables](#resource-creation-variables)
### [6. How to get output for your ARM templates?](#arm-generate-output)
### [7. Usage of exported templates](#exported-templates)
### [8. Usage of quick start templates](#quick-start-templates)
### [9. Resource creation with tags](#resource-creation-tags)
### [10. External parameter file usage for resources](#external-parameters-reference)

--
--
--
--
--
--
--


### <a name="before-we-start"></a>1. Brief Introduction (_Before you start_)
To push these ARM templates to our Azure environment, we will rely on **PowerShell** & have the following **Azure Subscription details**, ready before we kick-off. 

:pushpin: **Pointer:**

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
PS C:\Users\nagarjun k\Documents\az-journey\arm\a-basic\1-foundation-arm-creation> 
New-AzResourceGroupDeployment -Name "filecheck" -ResourceGroupName "azure-lab-rg-01" \
-TemplateFile .\azuredeploy.json -Verbose -WhatIf

What if: Performing the operation "Creating Deployment" on target "azure-lab-rg-01".

```

### <a name="new-resource-creation"></a>2. Resource creation
|Property|Definition|
|---|---|
|Folder|[2-resource-creation](./2-resource-creation)|
|File|_azuredeploy.json_|

Run this command to create a simple **storageaccount** in your resource group

**Command:**
```
PS C:\Users\nagarjun k\Documents\az-journey\arm\a-basic\2-resource-creation> 
New-AzResourceGroupDeployment -Name "createresource" -ResourceGroupName "azure-lab-rg-01" \
-TemplateFile .\azuredeploy.json -verbose
```

**Output:**
```
VERBOSE: Performing the operation "Creating Deployment" on target "azure-lab-rg-01".
VERBOSE: 16:53:23 - Template is valid.
VERBOSE: 16:53:26 - Create template deployment 'createresource'
VERBOSE: 16:53:37 - Resource Microsoft.Storage/storageAccounts 'storageaccountvsc012' provisioning status is running
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

### <a name="resource-creation-parameters"></a>3. Resource creation with parameters
|Property|Definition|
|---|---|
|Folder|[3-resource-creation-with-parameter](./3-resource-creation-with-parameter)|
|File|_azuredeploy-with-config-params.json_|

Run this command to create a simple **storageaccount** by using a custom file: _azuredeploy-with-config-params.json_, where you are defining a few parameters for resource creation (ex: _account name, sku, location_)

**Extract from the json file:**
```
/* parameter definition */
"storageSKU": {
     "type": "string",
     "defaultValue": "Standard_LRS",
     "allowedValues": [
       "Standard_LRS",
       "Standard_GRS",
       "Standard_GZRS",
       "Standard_RAGZRS"
     ]
   },
   ...
  /* resource definition */
   "sku": {
       "name": "[parameters('storageSKU')]" 
     },
   ```

**Command:**
```
PS C:\Users\nagarjun k\Documents\az-journey\arm\a-basic\3-resource-creation-with-parameter> 
New-AzResourceGroupDeployment -Name "resourcewithparameter" -ResourceGroupName "azure-lab-rg-01" \
-TemplateFile .\azuredeploy-with-config-params.json -Verbose
```

**Output:**
```
VERBOSE: Performing the operation "Creating Deployment" on target "azure-lab-rg-01".
VERBOSE: 16:59:10 - Template is valid.
VERBOSE: 16:59:12 - Create template deployment 'resourcewithparameter'
VERBOSE: 16:59:18 - Resource Microsoft.Storage/storageAccounts 'vscstorageaccount0123' provisioning status is running
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

### <a name="resource-creation-functions"></a>4. Resource creation with functions
|Property|Definition|
|---|---|
|Folder|[4-resource-creation-with-functions](./4-resource-creation-with-functions)|
|File|_azuredeploy.json_|

Run this command to create a simple **storageaccount** by using in-built ARM functions (in this example: _"resourceGroup().location"_) to automatically deduce the location of storage account based on resource group's location

**Command:**
```
PS C:\Users\nagarjun k\Documents\az-journey\arm\a-basic\4-resource-creation-with-functions>
New-AzResourceGroupDeployment -Name "createresourcewithfunctions" -ResourceGroupName "azure-lab-rg-01" \
-TemplateFile .\azuredeploy.json -verbose
```

**Output:**
```
VERBOSE: Performing the operation "Creating Deployment" on target "azure-lab-rg-01".
VERBOSE: 17:05:34 - Template is valid.
VERBOSE: 17:05:36 - Create template deployment 'createresourcewithfunctions'
VERBOSE: 17:05:41 - Resource Microsoft.Storage/storageAccounts 'vscstorageaccount0123' provisioning status is running
VERBOSE: 17:06:03 - Resource Microsoft.Storage/storageAccounts 'vscstorageaccount0123' provisioning status is succeeded

DeploymentName          : createresourcewithfunctions
ResourceGroupName       : azure-lab-rg-01
ProvisioningState       : Succeeded
Timestamp               : 18-04-2020 11:36:03
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name             Type                       Value
                          ===============  =========================  ==========
                          storageName      String                     vscstorageaccount0123
                          storageSKU       String                     Standard_LRS
                          location         String                     centralindia

Outputs                 :
DeploymentDebugLogLevel :
```

### <a name="resource-creation-variables"></a>5. Resource creation with variables
|Property|Definition|
|---|---|
|Folder|[5-resource-creation-with-variables](./5-resource-creation-with-variables)|
|File|_azuredeploy.json_|

Run this command to create a simple **storageaccount** by using a custom input flag: _myStoragePrefix_, which is defined as a variable within the json file. This command is useful when you wish to (externally) inject custom flags for your resources

**Command:**
```
PS C:\Users\nagarjun k\Documents\az-journey\arm\a-basic\5-resource-creation-with-variables>
New-AzResourceGroupDeployment -Name "createresourcewithvariables" -ResourceGroupName "azure-lab-rg-01" \
-mystoragePrefix "azstorage" -TemplateFile .\azuredeploy.json -Verbose
```

**Output:**
```
VERBOSE: Performing the operation "Creating Deployment" on target "azure-lab-rg-01".
VERBOSE: 17:11:25 - Template is valid.
VERBOSE: 17:11:27 - Create template deployment 'createresourcewithvariables'
VERBOSE: 17:11:37 - Resource Microsoft.Storage/storageAccounts 'azstoragebxmueijtaz47c' provisioning status is running
VERBOSE: 17:12:02 - Resource Microsoft.Storage/storageAccounts 'azstoragebxmueijtaz47c' provisioning status is succeeded

DeploymentName          : createresourcewithvariables
ResourceGroupName       : azure-lab-rg-01
ProvisioningState       : Succeeded
Timestamp               : 18-04-2020 11:41:58
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name               Type                       Value
                          =================  =========================  ==========
                          mystoragePrefix    String                     azstorage
                          storageSKU         String                     Standard_LRS
                          location           String                     centralindia

Outputs                 :
DeploymentDebugLogLevel :
```

### <a name="arm-generate-output"></a>6. How to get output for your ARM templates?
|Property|Definition|
|---|---|
|Folder|[6-resource-creation-with-output](./6-resource-creation-with-output)|
|File|_azuredeploy.json_|

Sometimes it becomes imperative to know the properties of the resources being created. Hence its essential to generate output & identify the associated properties with the AZ resources. 

The below command helps to create output associated with your storage account.(ex: _encryption, status & storage time_)

**Command:**
```
PS C:\Users\nagarjun k\Documents\az-journey\arm\a-basic\6-resource-creation-with-output>
New-AzResourceGroupDeployment -Name "createresourcewithoutput" -ResourceGroupName "azure-lab-rg-01" \
-mystoragePrefix "azstorage" -TemplateFile .\azuredeploy.json -Verbose
```

**Output:**
```
VERBOSE: Performing the operation "Creating Deployment" on target "azure-lab-rg-01".
VERBOSE: 17:15:11 - Template is valid.
VERBOSE: 17:15:13 - Create template deployment 'createresourcewithoutput'
VERBOSE: 17:15:18 - Resource Microsoft.Storage/storageAccounts 'azstoragebxmueijtaz47c' provisioning status is succeeded

DeploymentName          : createresourcewithoutput
ResourceGroupName       : azure-lab-rg-01
ProvisioningState       : Succeeded
Timestamp               : 18-04-2020 11:45:16
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name               Type                       Value
                          =================  =========================  ==========
                          mystoragePrefix    String                     azstorage
                          storageSKU         String                     Standard_LRS
                          location           String                     centralindia

Outputs                 :
                          Name               Type                       Value
                          =================  =========================  ==========
                          storageEndpoint    Object                     {
                            "services": {
                              "file": {
                                "enabled": true,
                                "lastEnabledTime": "2020-04-18T11:41:32.0837748Z"
                              },
                              "blob": {
                                "enabled": true,
                                "lastEnabledTime": "2020-04-18T11:41:32.0837748Z"
                              }
                            },
                            "keySource": "Microsoft.Storage"
                          }
                          storageStatus      String                     available
                          creationTime       String                     18-04-2020 11:41:32

DeploymentDebugLogLevel :
```

### <a name="exported-templates"></a>7. Usage of exported templates
|Property|Definition|
|---|---|
|Folder|[7-resource-creation-with-exported-template](./7-resource-creation-with-exported-template)|
|File|_azuredeploy-converged.json_|

We can create a AZ resource in the UI, and later export the template to create another resource from the ARM templates.

The below command helps to create both the **storage account & a virtual network** simultaneously

**Command:**
```
PS C:\Users\nagarjun k\Documents\az-journey\arm\a-basic\7-resource-creation-with-exported-template>
New-AzResourceGroupDeployment -Name "createresourcewithexportedtemplate" -ResourceGroupName "azure-lab-rg-01" \
-mystoragePrefix "azstorage" -TemplateFile .\azuredeploy-converged.json -Verbose
```

**Output:**
```
VERBOSE: Performing the operation "Creating Deployment" on target "azure-lab-rg-01".
VERBOSE: 17:30:27 - Template is valid.
VERBOSE: 17:30:30 - Create template deployment 'createresourcewithexportedtemplate'
VERBOSE: 17:30:35 - Resource Microsoft.Network/virtualNetworks 'az-lab-vnet-creation' provisioning status is running
VERBOSE: 17:30:51 - Resource Microsoft.Network/virtualNetworks/subnets 'az-lab-vnet-creation/subnetA' provisioning status is succeeded
VERBOSE: 17:30:51 - Resource Microsoft.Storage/storageAccounts 'azstoragebxmueijtaz47c' provisioning status is running
VERBOSE: 17:30:51 - Resource Microsoft.Network/virtualNetworks 'az-lab-vnet-creation' provisioning status is succeeded
VERBOSE: 17:30:56 - Resource Microsoft.Storage/storageAccounts 'azstoragebxmueijtaz47c' provisioning status is succeeded

DeploymentName          : createresourcewithexportedtemplate
ResourceGroupName       : azure-lab-rg-01
ProvisioningState       : Succeeded
Timestamp               : 18-04-2020 12:00:55
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name               Type                       Value
                          =================  =========================  ==========
                          mystoragePrefix    String                     azstorage
                          storageSKU         String                     Standard_LRS
                          location           String                     centralindia
                          vnet_name          String                     az-lab-vnet-creation

Outputs                 :
                          Name               Type                       Value
                          =================  =========================  ==========
                          storageEndpoint    Object                     {
                            "services": {
                              "file": {
                                "enabled": true,
                                "lastEnabledTime": "2020-04-18T12:00:34.5706322Z"
                              },
                              "blob": {
                                "enabled": true,
                                "lastEnabledTime": "2020-04-18T12:00:34.5706322Z"
                              }
                            },
                            "keySource": "Microsoft.Storage"
                          }
                          storageStatus      String                     available
                          creationTime       String                     18-04-2020 12:00:34
                          vnetAddrSpace      Object                     {
                            "addressPrefixes": [
                              "10.5.0.0/16"
                            ]
                          }
                          vnetSubnet         Array                      [
                            {
                              "name": "subnetA",
                              "id": "/subscriptions/2f981ee7-6c60-4593-bc4b-82c9b050f722/resourceGroups/azure-lab-rg-01/providers/Microsoft.Network/virtualNetworks/az-lab-vnet-creation/subnets/subnetA",
                              "etag": "W/\"b029fb02-74c2-4fc5-a750-09043c43e2df\"",
                              "properties": {
                                "provisioningState": "Succeeded",
                                "addressPrefix": "10.5.0.0/24",
                                "delegations": [],
                                "privateEndpointNetworkPolicies": "Enabled",
                                "privateLinkServiceNetworkPolicies": "Enabled"
                              },
                              "type": "Microsoft.Network/virtualNetworks/subnets"
                            }
                          ]
                          vnetPeeringInfo    Array                      []

DeploymentDebugLogLevel :
```

### <a name="quick-start-templates"></a>8. Usage of quick start templates
|Property|Definition|
|---|---|
|Folder|[8-resource-creation-with-quick-template](./8-resource-creation-with-quick-template)|
|File|_azuredeploy.json_|

We can always rely on the "quick-start" templated already provided for Azure resources (within github). It could save you a lot of configuration time and speed up new resource creation of any flavor.

The below command helps to create both the **storage account & a web-site** simultaneously

**Command:**
```
PS C:\Users\nagarjun k\Documents\az-journey\arm\a-basic\8-resource-creation-with-quick-template>
New-AzResourceGroupDeployment -Name "createresourcewithquickstarttemplate" -ResourceGroupName "azure-lab-rg-01" \
-mystoragePrefix "azstorage" -TemplateFile .\azuredeploy.json -Verbose
```

**Output:**
```
VERBOSE: Performing the operation "Creating Deployment" on target "azure-lab-rg-01".
VERBOSE: 17:38:01 - Template is valid.
VERBOSE: 17:38:03 - Create template deployment 'createresourcewithquickstarttemplate'
VERBOSE: 17:38:14 - Resource Microsoft.Storage/storageAccounts 'azstoragebxmueijtaz47c' provisioning status is running
VERBOSE: 17:38:33 - Resource Microsoft.Web/serverfarms 'exampleplan' provisioning status is succeeded
VERBOSE: 17:38:38 - Resource Microsoft.Storage/storageAccounts 'azstoragebxmueijtaz47c' provisioning status is succeeded
VERBOSE: 17:38:44 - Resource Microsoft.Web/sites 'demoappbxmueijtaz47c' provisioning status is succeeded

DeploymentName          : createresourcewithquickstarttemplate
ResourceGroupName       : azure-lab-rg-01
ProvisioningState       : Succeeded
Timestamp               : 18-04-2020 12:08:44
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                  Type                       Value
                          ====================  =========================  ==========
                          mystoragePrefix       String                     azstorage
                          storageSKU            String                     Standard_LRS
                          location              String                     southindia
                          appServicePlanName    String                     exampleplan
                          webAppName            String                     demoapp
                          linuxFxVersion        String                     php|7.0

Outputs                 :
                          Name               Type                       Value
                          =================  =========================  ==========
                          storageEndpoint    Object                     {
                            "dfs": "https://azstoragebxmueijtaz47c.dfs.core.windows.net/",
                            "web": "https://azstoragebxmueijtaz47c.z30.web.core.windows.net/",
                            "blob": "https://azstoragebxmueijtaz47c.blob.core.windows.net/",
                            "queue": "https://azstoragebxmueijtaz47c.queue.core.windows.net/",
                            "table": "https://azstoragebxmueijtaz47c.table.core.windows.net/",
                            "file": "https://azstoragebxmueijtaz47c.file.core.windows.net/"
                          }

DeploymentDebugLogLevel :
```

### <a name="resource-creation-tags"></a>9. Resource creation with tags
|Property|Definition|
|---|---|
|Folder|[9-resource-creation-with-tags](./9-resource-creation-with-tags)|
|File|_azuredeploy.json_|

Creation of tags is a very important step to "group or cluster" your AZ resources. It can help you keep an eye on the cost & also various departments to which that resource could be allocated.

The below command helps to create both the **storage account & a web-site** simultaneously with **the tags defined** in the template file

**Command:**
```
PS C:\Users\nagarjun k\Documents\az-journey\arm\a-basic\9-resource-creation-with-tags> 
New-AzResourceGroupDeployment -Name "createresourcewithtags" -ResourceGroupName "azure-lab-rg-01" \
-mystoragePrefix "azstorage" -TemplateFile .\azuredeploy.json -Verbose
```

**Output:**
```
VERBOSE: Performing the operation "Creating Deployment" on target "azure-lab-rg-01".
VERBOSE: 17:42:07 - Template is valid.
VERBOSE: 17:42:09 - Create template deployment 'createresourcewithtags'
VERBOSE: 17:42:14 - Resource Microsoft.Storage/storageAccounts 'azstoragebxmueijtaz47c' provisioning status is running
VERBOSE: 17:42:32 - Resource Microsoft.Web/serverfarms 'exampleplan' provisioning status is succeeded
VERBOSE: 17:42:44 - Resource Microsoft.Web/sites 'demoappbxmueijtaz47c' provisioning status is succeeded
VERBOSE: 17:50:42 - Resource Microsoft.Storage/storageAccounts 'azstoragebxmueijtaz47c' provisioning status is succeeded


DeploymentName          : createresourcewithtags
ResourceGroupName       : azure-lab-rg-01
ProvisioningState       : Succeeded
Timestamp               : 18-04-2020 12:20:38
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name                  Type                       Value
                          ====================  =========================  ==========
                          mystoragePrefix       String                     azstorage
                          storageSKU            String                     Standard_LRS
                          location              String                     southindia
                          appServicePlanName    String                     exampleplan
                          webAppName            String                     demoapp
                          linuxFxVersion        String                     php|7.0
                          resourceTags          Object                     {
                            "environment": "dev",
                            "lab-simulation": "arm-template-creation"
                          }

Outputs                 :
                          Name               Type                       Value
                          =================  =========================  ==========
                          storageEndpoint    Object                     {
                            "dfs": "https://azstoragebxmueijtaz47c.dfs.core.windows.net/",
                            "web": "https://azstoragebxmueijtaz47c.z30.web.core.windows.net/",
                            "blob": "https://azstoragebxmueijtaz47c.blob.core.windows.net/",
                            "queue": "https://azstoragebxmueijtaz47c.queue.core.windows.net/",
                            "table": "https://azstoragebxmueijtaz47c.table.core.windows.net/",
                            "file": "https://azstoragebxmueijtaz47c.file.core.windows.net/"
                          }

DeploymentDebugLogLevel :
```

### <a name="external-parameters-reference"></a>10. External parameter file usage for resources
|Property|Definition|
|---|---|
|Folder|[10-resource-creation-with-external-parameters](./10-resource-creation-with-external-parameters)|
|File|_azuredeploy.json_|
|ParameterFile|_azuredeploy.parameters.dev.json_|

Sometimes it becomes necessary to create multiple AZ resources for different environments. To bring the element of re-usability, we can externalize the parameters (based on environments) & inject them as reference during resource creation

The below command helps to create both the **storage account & a web-site** simultaneously with **the template parameterfile for dev** environment

**Command:**
```
PS C:\Users\nagarjun k\Documents\az-journey\arm\a-basic\10-resource-creation-with-external-parameters>
New-AzResourceGroupDeployment -Name "createresourcewithexternalparameters" -ResourceGroupName "azure-lab-rg-01" \
-mystoragePrefix "azstorage" -TemplateFile .\azuredeploy.json \
-TemplateParameterFile .\azuredeploy.parameters.dev.json -Verbose
```

**Output:**
```
VERBOSE: Performing the operation "Creating Deployment" on target "azure-lab-rg-01".
VERBOSE: 18:17:25 - Template is valid.
VERBOSE: 18:17:27 - Create template deployment 'createresourcewithexternalparameters'
VERBOSE: 18:17:33 - Resource Microsoft.Storage/storageAccounts 'azstoragebxmueijtaz47c' provisioning status is running
VERBOSE: 18:17:33 - Resource Microsoft.Network/virtualNetworks 'dev12' provisioning status is running
VERBOSE: 18:17:47 - Resource Microsoft.Network/virtualNetworks/subnets 'dev12/subnet-dev' provisioning status is succeeded
VERBOSE: 18:17:47 - Resource Microsoft.Network/virtualNetworks 'dev12' provisioning status is succeeded
VERBOSE: 18:24:30 - Resource Microsoft.Storage/storageAccounts 'azstoragebxmueijtaz47c' provisioning status is succeeded


DeploymentName          : createresourcewithexternalparameters
ResourceGroupName       : azure-lab-rg-01
ProvisioningState       : Succeeded
Timestamp               : 18-04-2020 12:54:30
Mode                    : Incremental
TemplateLink            :
Parameters              :
                          Name               Type                       Value
                          =================  =========================  ==========
                          mystoragePrefix    String                     azstorage
                          storageSKU         String                     Standard_LRS
                          location           String                     southindia
                          vnet_name          String                     dev12
                          subnet_name        String                     subnet-dev
                          allAddrSpaces      Object                     {
                            "addressPrefixes": [
                              {
                                "name": "firstPrefix",
                                "addressPrefix": "10.7.0.0/16"
                              },
                              {
                                "name": "secondPrefix",
                                "addressPrefix": "10.8.0.0/16"
                              }
                            ],
                            "subnets": [
                              {
                                "name": "firstSubnet",
                                "addressPrefix": "10.7.0.0/24"
                              },
                              {
                                "name": "secondSubnet",
                                "addressPrefix": "10.8.0.0/24"
                              }
                            ]
                          }
                          resourceTags       Object                     {
                            "environment": "dev",
                            "lab-simulation": "arm-template-creation-dev"
                          }

Outputs                 :
                          Name               Type                       Value
                          =================  =========================  ==========
                          storageEndpoint    Object                     {
                            "services": {
                              "file": {
                                "enabled": true,
                                "lastEnabledTime": "2020-04-18T12:47:30.9451153Z"
                              },
                              "blob": {
                                "enabled": true,
                                "lastEnabledTime": "2020-04-18T12:47:30.9451153Z"
                              }
                            },
                            "keySource": "Microsoft.Storage"
                          }
                          storageStatus      String                     available
                          creationTime       String                     18-04-2020 12:47:30
                          vnetAddrSpace      Object                     {
                            "addressPrefixes": [
                              "10.7.0.0/16",
                              "10.8.0.0/16"
                            ]
                          }
                          vnetSubnet         Array                      [
                            {
                              "name": "subnet-dev",
                              "id": "/subscriptions/2f981ee7-6c60-4593-bc4b-82c9b050f722/resourceGroups/azure-lab-rg-01/providers/Microsoft.Network/virtualNetworks/dev12/subnets/subnet-dev",
                              "etag": "W/\"5b18c623-0a25-4172-9727-c5c1432a3b6c\"",
                              "properties": {
                                "provisioningState": "Succeeded",
                                "addressPrefix": "10.7.0.0/24",
                                "delegations": [],
                                "privateEndpointNetworkPolicies": "Enabled",
                                "privateLinkServiceNetworkPolicies": "Enabled"
                              },
                              "type": "Microsoft.Network/virtualNetworks/subnets"
                            }
                          ]
                          vnetPeeringInfo    Array                      []

DeploymentDebugLogLevel :
```
