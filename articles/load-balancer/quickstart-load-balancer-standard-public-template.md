---
title: "Quickstart: Create a Standard load balancer - Azure Resource Manager template"
titlesuffix: Azure Load Balancer
description: This quickstart shows how to create a Standard Load Balancer by using the Azure Resource Manager template.
services: load-balancer
documentationcenter: na
author: KumudD
manager: twooley
Customer intent: I want to create a Standard Load Balancer by using an Azure Resource Manager template so that I can load balance internet traffic to VMs.
ms.service: load-balancer
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/30/2019
ms.author: kumud
ms.custom: mvc
---

# Quickstart: Create a Standard Load Balancer to load balance VMs by using an Azure Resource Manager template

Load balancing provides a high level of availability and scale by spreading incoming requests across multiple virtual machines (VMs). You can deploy an Azure Resource Manager template to create a load balancer that load balances VMs. This quickstart shows you how to load balance VMs by using Azure Standard Load Balancer.

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Create a Standard Load Balancer

In this section, you create a Standard Load Balancer that helps load balance VMs.

A Standard Load Balancer supports only a Standard Public IP address. When you create a Standard Load Balancer, you must also create a new Standard Public IP address that is configured as the front end for the Standard Load Balancer. By default, the front end is named **LoadBalancerFrontend**.

You can use many methods to create a Standard Load Balancer. In this quickstart, you use Azure PowerShell to deploy a [Resource Manager template](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-load-balancer-standard-create/azuredeploy.json). Resource Manager templates are JSON files that define the resources you need to deploy for your solution.

To understand the concepts associated with deploying and managing your Azure solutions, see [Azure Resource Manager documentation](/azure/azure-resource-manager/). To find more templates related to Azure Load Balancer, see [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network&pageNumber=1&sort=Popular).

To deploy the template, select **Try it** to open Azure Cloud Shell, and then paste the following PowerShell script into the shell window. To paste the code, right-click on the shell window, and then select **Paste**. For a list of regions that support Azure Availability Zones for Azure Virtual Machines, see [What are Availability Zones in Azure?](../availability-zones/az-overview.md).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name with 12 or less letters or numbers that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$adminUserName = Read-Host -Prompt "Enter the virtual machine administrator account name"
$adminPassword = Read-Host -Prompt "Enter the virtual machine administrator password" -AsSecureString

$resourceGroupName = "${projectName}rg"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/101-load-balancer-standard-create/azuredeploy.json"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -projectName $projectName -location $location -adminUsername $adminUsername -adminPassword $adminPassword

Write-Host "Press [ENTER] to continue."

```

 >[!NOTE]
 >The resource-group name is the project name with **rg** appended. You need the resource-group name in the next section. It takes a few minutes for the resources to be created.

## Test the load balancer

1. Sign in to the [Azure portal](https://portal.azure.com).
1. Select **Resource groups** from the left pane.
1. Select the resource group that you created in the previous section. The default resource group name is the project name with **rg** appended.
1. Select the load balancer. Its default name is the project name with **-lb** appended.
1. Copy only the IP address part of the public IP address, and then paste it into the address bar of your browser. The browser displays the default page of the Internet Information Services (IIS) web server.

   ![IIS Web server](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

To see the load balancer distribute traffic across all three VMs, you can force a refresh of your web browser from the client machine.

## Clean up resources

When you no longer need them, delete the resource group, the load balancer, and all related resources. To do so, go to the Azure portal, select the resource group that contains the load balancer, and then select **Delete resource group**.

## Next steps

In this quickstart, you created a Standard Load Balancer, attached VMs to it, configured the load-balancer traffic rule, did a health probe, and then tested the load balancer.

To learn more, continue to the tutorials for Load Balancer.

> [!div class="nextstepaction"]
> [Azure Load Balancer tutorials](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
