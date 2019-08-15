---
title: Azure Traffic Manager subnet override | Microsoft Docs
description: This article will help you understand how Traffic Manager subnet override can be used to override the routing method of a Traffic Manager profile to direct traffic to an endpoint based upon the end-user IP address via predefined IP range to endpoint mappings.
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/15/2019
ms.author: allensu
---

# Traffic Manager subnet override

Traffic Manager subnet override allows you to alter the routing method of a profile.  Adding a override will direct traffic based upon the end-user's IP address with a predefined IP range to endpoint mapping. 

## How subnet override works

When subnet overrides are added to a traffic manager profile, Traffic Manager will first check if there's a subnet override for the end user’s IP address. If one is found, the user’s DNS query will be directed to the corresponding endpoint.  When a mapping is not found, Traffic Manager will fall back to the profile’s original routing method. 

There are two types of routing profiles that support subnet overrides:

* **Geographic** - If Traffic Manager finds a subnet override for the DNS query's IP address, it will route the query to the endpoint whatever the health of the endpoint is.
* **Performance** - If Traffic Manager finds a subnet override for the DNS query's IP address, it will only route the traffic to the endpoint if it's healthy.  Traffic Manager will fall back to the performance routing heuristic if the subnet override endpoint isn't healthy.

## Create a Traffic Manager subnet override

To create a Traffic Manager subnet override, you can use Azure PowerShell or Azure CLI to add the subnets for the override to the Traffic Manager endpoint.

## Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

You can run the commands that follow in the [Azure Cloud Shell](https://shell.azure.com/powershell), or by running PowerShell from your computer. The Azure Cloud Shell is a free interactive shell. It has common Azure tools preinstalled and configured to use with your account. 
If you run PowerShell from your computer, you need the Azure PowerShell module, 1.0.0 or later. You can run `Get-Module -ListAvailable Az` to find the installed version. If you need to install or upgrade, see [Install Azure PowerShell module](/powershell/azure/install-az-ps). If you are running PowerShell locally, you also need to run `Login-AzAccount` to sign in to Azure.


1. **Retrieve the Traffic Manager endpoint:**

    To enable the subnet override, retrieve the endpoint you wish to add the override to and store it in a variable using [Get-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/get-aztrafficmanagerendpoint?view=azps-2.5.0).

    Replace the Name, ProfileName, and ResourceGroupName with the values of the endpoint that you're changing.

    ```powershell

    $TrafficManagerEndpoint = Get-AzTrafficManagerEndpoint -Name "contoso" -ProfileName "ContosoProfile" -ResourceGroupName "ResourceGroup" -Type AzureEndpoints

    ```
2. **Add the IP address range to the endpoint:**
    
    To add the IP address range to the endpoint, you'll use [Add-AzTrafficManagerIpAddressRange](https://docs.microsoft.com/powershell/module/az.trafficmanager/add-aztrafficmanageripaddressrange?view=azps-2.5.0&viewFallbackFrom=azps-2.4.0) to add the range.

    ```powershell

    ### Add a range of IPs ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "1.2.3.4" -Last "5.6.7.8"

    ### Add a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "9.10.11.0" -Scope 24

    ### Add a range of IPs with a subnet ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "12.13.14.0" -Last "12.13.14.31" -Scope 27

    ### Add a single IP address ###
    Add-AzTrafficManagerIPAddressRange -TrafficManagerEndpoint $TrafficManagerEndpoint -First "15.16.17.18"

    ```
    Once the ranges are added, use [Set-AzTrafficManagerEndpoint](https://docs.microsoft.com/powershell/module/az.trafficmanager/set-aztrafficmanagerendpoint?view=azps-2.5.0) to update the endpoint.

    ```powershell

    Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $TrafficManagerEndpoint

    ```

## Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

If you choose to install and use the CLI locally, this tutorial requires that you are running a version of the Azure CLI version 2.0.28 or later. To find the version, run `az --version`. If you need to install or upgrade, see [Install Azure CLI]( /cli/azure/install-azure-cli).

## Update the Traffic Manager endpoint with subnet override.
Use Azure CLI to update your endpoint with [az network traffic-manager endpoint update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update).

```azurecli

### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a single IP ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 15.16.17.18 \
    --type AzureEndpoints

```

## Next Steps
Learn more about Traffic Manager [traffic routing methods](traffic-manager-routing-methods.md).


