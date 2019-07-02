# Azure - Palo Alto VMSeries Firewall into existing environment

[<img src="http://azuredeploy.net/deploybutton.png"/>](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fwahidsaleemi%2Fazure3micmanaged%2Fmaster%2FAzureDeploy.json)

This template was created to support the deployment of a 3 interface Palo Alto Networks firewall into an existing Microsoft Azure environment that has the following items already deployed:

- Azure Virtual Network - with at least 3 subnets
- Resource Group for Firewall (or created at time of deployment)

## FEATURES

- The firewall deploys with 3 interfaces.  1 MGMT (with Public IP) and 2 data plane into an existing environment.
- It is possible to choose the version of software the firewall is running. 7.1 or 8.0 (Latest)
- The deployment SKU can also be choosen during deployment.  BYOL, Bundle1 or Bundle2 are the available options.
- Static IP addresses assignment is used for all the firewall interfaces.

The following Storage Account types are supported for Managed Disks:

    - Standard_LRS
    - Premium_LRS
    - StandardSSD_LRS

The following VM sizes are supported:

    - Standard_D3
    - Standard_D4
    - Standard_D3_v2
    - Standard_D4_v2
    - Standard_A4
    - Standard_DS3_v2
    - Standard_DS4_v2

## DEPLOYMENT

When deploying, specify the password in SecureString format (PowerShell only), or by using Key Vault (PowerShell and CLI).

### PowerShell

```powershell
  $secPassword = ConvertTo-SecureString -String "a-strong-password" -AsPlainText -Force
  New-AzureRmResourceGroupDeployment -ResourceGroupName rg-firewall -TemplateFile .\azuredeploy.json `
    -TemplateParameterFile .\azuredeploy.parameters.json -adminPassword $secPassword -Verbose
```

### CLI

For CLI, the password must be stored as a secret in Azure Key Vault. See [Azure Docs](https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-template-deploy-cli#sample-template). Then deploy as normal:

```java
  az group deployment create --name exampledeployment --resource-group rg-firewall \
  --template-file azuredeploy.json --parameters @azuredeploy.parameters.json
```

## CHANGELOG

[2018-07-17]

- Removed references to Storage Accounts in this README.md
- Removed "-preview" from the schema URL and corrected it to point to Parameters schema.
- Removed Storage Account from parameters and template file, and added managedDiskType parameter.
- Removed apiVersion variable as its only used once and some tools (VSCode) complain about the value.
- Removed virtualNetworkAddressPrefix parameter. It is not used.
- Removed Subnet variable, it is not used. Also, this template requires an existing VNET and Subnets.
- Changed compute api version to 2018-06-01. Updated network api to 2017-10-01.
- Removed the defaultValue of the VNET from 192.168.0.0/16, it is not used.
- Removed SubnetPrefix parameters and nsgName parameters, not used.
- Changed nicName variable from "eth" to "Nic" since a single NIC could have multiple IP configs (eth0, eth1)
- Changed NIC names to be more descriptive instead of using numbers (0,1,2)
- Changed publicIPAddressName to use variables in Resource section to avoid mistakes if users enter upper case characters (not allowed)
- Changed OS disk size to 63 (from 60), to align with S6/P6 Managed Disk SKU.
- Minor formatting and corrections to README.md (like 3 interfaces instead of 4)
- Best Practice: Added tags to all resources.
- Best Practice: Re-arranged key/value pairs in Resources section to align with Azure Docs (name, type, apiVersion, etc.) and for consistency.
- Best Practice: Added an Availability Set. Even with 1 VM, since you can't add this later and it is not billed anyway.
- Best Practice: Added a Resouce Lock to prevent accidental deletion of the firewall since this type of device is usually considered critical.

End