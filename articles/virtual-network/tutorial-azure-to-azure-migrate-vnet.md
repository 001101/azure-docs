---
title: Move Azure Virtual Network to another Azure region  
service: virtual-network
description: Use Azure Resource manager template to move Azure Virtual Network from one Azure region to another.
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: allensu
ms.custom: MVC
---

# Move Azure Virtual Network to another region

There are various scenarios in which you'd want to move your existing Azure Virtual Networks (VNETs) from one region to another. For example, you may want to create a Virtual Network with the same address space and options for testing and availability of your existing virtual network, or move a production virtual network to another region as part of disaster recovery planning.

You can use a Azure Resource manager template to complete the move of the Virtual Network to another region by exporting the Virtual Network to a template, modifying the parameters to match the destination region, and then deploy the template to the new region.  For more information on Azure Resource manager and templates, see [Quickstart: Create and deploy Azure Resource Manager templates by using the Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal)

In this tutorial, you will:

> [!div class="checklist"]
> 
> * Verify prerequisites for the move
> * Prepare the source Virtual Network and the target region
> * Test the configuration and perform the move
> * Delete the resources in the source region
> 
> [!NOTE]
> This tutorial shows you how to move an Azure Virtual Network from one region to another as is. 

## Prerequisites

- Make sure that the Azure Virtual Network is in the Azure region from which you want to move.
- Verify that your choice of [source region - target region combination is supported](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support), and make an informed decision about the target region.
- Make sure that you understand the [scenario architecture and components](azure-to-azure-architecture.md).
- Review the [support limitations and requirements](azure-to-azure-support-matrix.md).
- Verify account permissions. If you created your free Azure account, you're the administrator of your subscription. If you're not the subscription administrator, work with the administrator to assign the permissions that you need. To export a virtual network and deploy a template to create a virtual network in another region, you will need the following permissions:

        - Permissions to create a virtual network in Azure resources. The Network Contributor built-in role has these permissions, which include:
          - Permission to create and manage networks
          - Permission to create and manage resource group deployments


- Virtual network peerings will not be recreated and will fail if they are still present in the template.  Virtual network peers will have to be deleted and re-created once the Virtual Networks are exported to a template and moved.
    
- Identify the source networking layout and all the resources that you're currently using. This includes but isn't limited to load balancers, network security groups (NSGs), and public IPs.

- Verify that your Azure subscription allows you to create virtual networks in the target region that's used. Contact support to enable the required quota.

- Make sure that your subscription has enough resources to support the addition of Virtual Networks for this process.  See [Azure subscription and service limits, quotas, and constraints](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)


## Prepare and Move
The following steps shows how to prepare the virtual network for the move using a Azure Resource manager template, and move the virtual network to the target region using the portal and a script.

### Export the template and deploy from the portal in one step

1. Login to the [Azure Portal](http://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source virtual network and click on it.
3. Select > **Settings** > **Export template**.
4. You can choose to deploy the template now and choose the parameters in the portal or you can download the template and deploy with a script to the region and resource group of your choosing.  To deploy now in the portal, choose **Deploy**.
5. In the custom deployment blade under **Basics** > **Subscription**, choose the subscription where you wish to deploy the target virtual network.
6. Under **Basics** > **Resource Group**, choose **Create new** and name the resource group **myResourceGroupVNET-Move**.
7. Under **Basics** > **Location**, choose any other region except the region of the source virtual network.
8. Under **Settings** > **Virtual Networks_name**, enter a name for the target virtual network.
9. Check the box **I agree to the terms and conditions stated above.**
10. Click **Purchase**

### Export the template and deploy from a script

1. Login to the [Azure Portal](http://portal.azure.com) > **Resource Groups**.
2. Locate the Resource Group that contains the source virtual network and click on it.
3. Select > **Settings** > **Export template**.
4. Choose **Download** in the **Export template** blade.
5. Locate the .zip file downloaded from the portal containing the template and unzip to a folder of your choice.  In this zip file is the .json files needed for the template as well as a shell script and PowerShell script to deploy the template.
6. To edit the parameter of the virtual network name, open the **parameters.json** file that you unzipped into the folder of your choice in text editor of your choice.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
            "value": null
        }
    }
}
```
7. Change the **null** value in the .json file to a name of your choice for the target VNET, and save the parameters.json file.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

8. Log in to Azure

    Log in to your Azure subscription with the [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) command and follow the on-screen directions:
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

9. Obtain the subscription ID where you wish to deploy the target virtual network with [Get-AzSubscription](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-2.5.0):

    ```azurepowershell-interactive
    Get-AzSubscription
    ```
10. Change to the directory where you unzipped the template files and saved the parameters.json file and run the following command to deploy the template and virtual network into the target region:

    ```azurepowershell-interactive
    ./deploy.ps1 -subscription "Azure Subscription" -resourceGroupName myresourcegroup -resourceGroupLocation targetregion
    ```

## Discard 

After the deployment, if you wish to start over or discard the virtual network in the target, delete the resource group that was created in the target and the moved virtual network will be deleted.


## Clean up

To commit the changes and complete the move of the virtual network, delete the source virtual network or resource group.

## Next steps

In this tutorial, you moved a Azure Virtual network from one region to another and cleaned up the source resources.  To learn more about moving resources between regions and disaster recovery in Azure, refer to:


- [Set up disaster recovery after migration](azure-to-azure-quickstart.md)
- [Move resources to a new resource group or subscription](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [Move Azure VMs to another region](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
