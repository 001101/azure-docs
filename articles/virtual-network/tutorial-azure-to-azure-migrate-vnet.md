---
title: Move Azure Virtual Network to another Azure region  
service: virtual-network
description: Use Azure Resource Manager template to move Azure Virtual Network from one Azure region to another.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: allensu
ms.custom: MVC
---

# Move Azure Virtual Network to another region

There are various scenarios in which you'd want to move your existing Azure Virtual Networks (VNETs) from one region to another. For example, you may want to create a Virtual Network with the same address space and options for testing and availability of your existing virtual network. You may also want to move a production virtual network to another region as part of disaster recovery planning.

You can use an Azure Resource Manager template to complete the move of the Virtual Network to another region by exporting the Virtual Network to a template, modifying the parameters to match the destination region, and then deploy the template to the new region.  For more information on Azure Resource Manager and templates, see [Quickstart: Create and deploy Azure Resource Manager templates by using the Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)

In this tutorial, you will:

> [!div class="checklist"]
> 
> * Verify prerequisites for the move
> * Prepare the source Virtual Network and the target region
> * Test the configuration and perform the move
> * Delete the resources in the source region
> 
> [!NOTE]
> This tutorial shows you how to move an Azure Virtual Network from one region to another.

## Prerequisites

- Make sure that the Azure Virtual Network is in the Azure region from which you want to move.

- Verify account permissions. If you created your free Azure account, you're the administrator of your subscription. If you're not the subscription administrator, work with the administrator to assign the permissions that you need. To export a virtual network and deploy a template to create a virtual network in another region, you'll need the following permissions:

  - Permissions to create a virtual network in Azure resources. The Network Contributor built-in role has these permissions, which include:
    - Permission to create and manage networks
    - Permission to create and manage resource group deployments

- Virtual network peerings won't be recreated and will fail if they're still present in the template.  You will have to remove any Virtual network peers before exporting the template and then re-establish the peers after the move of the virtual network.
    
- Identify the source networking layout and all the resources that you're currently using. This includes but isn't limited to load balancers, network security groups (NSGs), and public IPs.

- Verify that your Azure subscription allows you to create virtual networks in the target region that's used. Contact support to enable the required quota.

- Make sure that your subscription has enough resources to support the addition of Virtual Networks for this process.  See [Azure subscription and service limits, quotas, and constraints](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## Prepare and Move
The following steps show how to prepare the virtual network for the move using an Azure Resource Manager template, and move the virtual network to the target region using the portal and a script.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### Export the template and deploy from a script

1. Log in to Azure

    Log in to your Azure subscription with the [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) command and follow the on-screen directions:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. Obtain the subscription ID where you wish to deploy the target virtual network with [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0):

    ```azurepowershell-interactive
    Get-AzSubscription
    ```
3. Login to the [Azure portal](http://portal.azure.com) > **Resource Groups**.
4. Locate the Resource Group that contains the source virtual network and click on it.
5. Select > **Settings** > **Export template**.
6. Choose **Download** in the **Export template** blade.
7. Locate the .zip file downloaded from the portal containing the template and unzip to a folder of your choice.  In this zip file is the .json files needed for the template and a shell script and PowerShell script to deploy the template.
8. To edit the parameter of the virtual network name, open the **parameters.json** file that you unzipped into the folder of your choice in the text editor of your choice:
    
    ```json
    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "virtualNetworks_myVNET1_name": {
                "value": "null"
            }
        }
    }
    ```
9. Change the **null** value in the .json file to a name of your choice for the target VNET. Save the parameters.json file. Ensure you enclose the name in quotes.

10. To edit the target region where the VNET will be moved, open the **template.json** file that you unzipped into the folder of your choice in the text editor of your choice:
    
           ```json
            "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "TARGET REGION",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },
            ```

    
    
11. Edit the location in the **template.json** file to the target region. To obtain region location codes, you can use the Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) by running the following command:

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. Save the **template.json** file.

13. Change to the directory where you unzipped the template files and saved the parameters.json file and run the following command to deploy the template and virtual network into the target region:

    ```azurepowershell-interactive

    ./deploy.ps1 -subscription "Azure Subscription" -resourceGroupName myresourcegroup -resourceGroupLocation targetregion
    
    ```

## Discard 

After the deployment, if you wish to start over or discard the virtual network in the target, delete the resource group that was created in the target and the moved virtual network will be deleted.  To remove the resource group, use [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzureResourceGroup -Name <resource-group-name>

```

## Clean up

To commit the changes and complete the move of the virtual network, delete the source virtual network or resource group, use [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) or [Remove-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0):

```azurepowershell-interactive

Remove-AzureResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>

```

## Next steps

In this tutorial, you moved an Azure Virtual Network from one region to another and cleaned up the source resources.  To learn more about moving resources between regions and disaster recovery in Azure, refer to:


- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Move Azure VMs to another region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
