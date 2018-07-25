---
title: Migration from Azure Germany compute resources to global Azure
description: Provides help for migrating network resources
author: gitralf
ms.author: ralfwi 
ms.date: 7/20/2018
ms.topic: article
ms.custom: bfmigrate
---

# Networking

## Virtual Network (VNet)

Migration of VNets between Azure Germany and global Azure isn't supported at this time. The recommended approach is to create new virtual networks in the target region and migrate resources into those VNets.

### Next steps

Refresh your knowledge about VPN Gateways by following these [Step-by-step tutorials](../virtual-network/#step-by-step-tutorials).

### References

- [About Virtual networks](../virtual-network/virtual-networks-overview.md)
- [Planning Virtual networks](../virtual-network/virtual-network-vnet-plan-design-arm.md)









## Network Security Groups (NSG)

Migration of Network Security Groups between Azure Germany and global Azure isn't supported at this time. The recommended approach is to create new Network Security groups in the target region and apply the NSG rules to the new application environment.

### Next steps

Refresh your [knowledge about Network Security Groups](../virtual-network/security-overview.md#network-security-groups).

### References

- [About Network Security Groups](../virtual-network/security-overview.md)
- [Manage Network Security Groups](../virtual-network/manage-network-security-group.md)







## ExpressRoute

Migration of ExpressRoute between Azure Germany and global Azure isn't supported at this time. The recommended approach is to create new ExpressRoute circuits and new ExpressRoute gateway.

### Next steps

Refresh your knowledge about ExpressRoute by following these [Step-by-step tutorials](../expressroute/#schritt-für-schritt-tutorials).

### References

- [Create a new ExpressRoute gateway](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)
- [ExpressRoute locations and service providers](../expressroute/expressroute-locations.md)
- [More info about ExpressRoute gateway](../expressroute/expressroute-about-virtual-network-gateways.md)





## VPN Gateway

This service is also covered under [Security](./germany-migration-security.md#vpn-gateway).

Migration of Virtual Private Network (VPN) Gateways between Azure Germany and global Azure isn't supported at this time. The recommended approach is to create and configure a new VPN Gateway.

Collect information about your current VPN gateway configuration by using the portal or by using PowerShell. There's a set of cmdlets starting with `Get-AzureRmVirtualNetworkGateway\*`.

Don't forget to update your on-premise configuration and delete any existing rules for the old IP ranges once you updated your Azure network environment.

### Next steps

Refresh your knowledge about VPN Gateways by following these [Step-by-step tutorials](../vpn-gateway/#step-by-step-tutorials).

### References

- [Create Site-to-Site connection](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) with VPN gateway
- [Get-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/en-us/powershell/module/azurerm.network/get-azurermvirtualnetworkgateway?view=azurermps-6.5.0) PowerShell cmdlets
- Blog: [Create Site-to-site connection](https://blogs.technet.microsoft.com/ralfwi/2017/02/02/connecting-clouds/) between Azure Germany and global Azure
 






## Application Gateway

This service is also covered under [Security](./germany-migration-security.md#application-gateway)

Migration of Application Gateways between Azure Germany and global Azure isn't supported at this time. The recommended approach is to create and configure a new Gateway.

Collect information about your current gateway configuration by using the portal or by using PowerShell. There's a set of cmdlets starting with `Get-AzureRmApplicationGateway\*`.

### Next steps

Refresh your knowledge about Application Gateway by following these [Step-by-step tutorials](../application-gateway/#step-by-step-tutorials).

### References

- [Create Application Gateway](../application-gateway/quick-create-portal.md)
- [Get-AzureRmApplicationGateway](https://docs.microsoft.com/en-us/powershell/module/azurerm.network/get-azurermapplicationgateway?view=azurermps-6.5.0) PowerShell cmdlets














## Azure DNS

Export the DNS zone file and import it under the new subscription. Currently the only mechanism to export the zone file is by using Azure CLI.

When you're signed on to your source subscription in Azure Germany, configure CLI to use Resource Manager mode. Export the zone:

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

Example:

```azurecli
az network dns zone export -g "myresourcegroup" -n "contoso.com" -f "contoso.com.txt"
```

This command calls the Azure DNS service to export the zone "contoso.com" in the resource group "myresourcegroup". The output is stored as a BIND-compatible zone file in "contoso.com.txt" in the current folder.

After the export is done, delete the NS records from the zone file as NS records will be created fresh for the new region and subscription.

Now sign in to your target environment, create a new resource group (or choose an existing one) and import the previously created zone file:

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

Once the zone has been imported, you have to validate the zone by running the following command:

```azurecli
az network dns record-set list -g <resource group> -z <zone name>
```

Once the validation is done, contact your domain registrar and redelegate the NS records. Get NS record information by executing this command:

```azurecli
az network dns record-set ns list -g <resource group> -z --output json
```

### References

- [Azure DNS Overview](../dns/)
- [Azure DNS Step-by-Step tutorials](../dns/#step-by-step-tutorials)










## Network Watcher

This service is also covered under [Management Tools](./germany-migration-managementtools.md#network-watcher).

Migration of Network Watcher between Azure Germany and global Azure isn't supported at this time. The recommended approach is to create and configure a new Network Watcher. Afterwards, compare the results between old and new environment:

- [NSG Flow Logs](../network-watcher/network-watcher-nsg-flow-logging-portal.md)
- [Connection Monitor](../network-watcher/connection-monitor.md)

### Next steps

Refresh your knowledge about Network Watcher by following these [Step-by-step tutorials](../network-watcher/#step-by-step-tutorials).

### References

- [Network Watcher Overview](../network-watcher/network-watcher-monitoring-overview.md)













## Traffic Manager

This service is also covered under [Management Tools](./germany-migration-managementtools.md#network-watcher).

Traffic Manager profiles created in Azure Germany can't be migrated over to global Azure. Since you also migrate all the Traffic Manager endpoints to the target environment, you need to update the Traffic Manager profile anyway.

Traffic Manager can help you with a smooth migration. With Traffic Manager still running in the old environment, you can already define additional endpoints in the target environment. Once Target Manager runs in the new environment, you can still define endpoints in the old environment that you didn't migrate so far. This is known as [the Blue-Green scenario](https://azure.microsoft.com/en-us/blog/blue-green-deployments-using-azure-traffic-manager/). In short:

- Create a new Traffic Manager in Azure public.
- Define the endpoints still in Azure Germany.
- Change your DNS CNAME to the new Traffic Manager.
- Turn off the old Traffic Manager
- Migrate endpoint by endpoint to the new environment and change the Traffic Manager profile accordingly.

### Next steps

Refresh your knowledge about Traffic Manager by following these [Step-by-step tutorials](../traffic-manager/#step-by-step-tutorials).

### References

- [Traffic Manager overview](../traffic-manager/traffic-manager-overview.md)
- [Create a Traffic Manager profile](../traffic-manager/traffic-manager-create-profile.md)
- [Blue-Green scenario](https://azure.microsoft.com/en-us/blog/blue-green-deployments-using-azure-traffic-manager/)



















## Load Balancer

Migration of Load Balancers between Azure Germany and global Azure isn't supported at this time. The recommended approach is to create and configure a new Load Balancer.

### Next steps

Refresh your knowledge about Load Balancer by following these [Step-by-step tutorials](../load-balancer/#step-by-step-tutorials).

### References

- [Load balancer overview](../load-balancer/load-balancer-overview.md)
- [Create new load balancer](../load-balancer/quickstart-load-balancer-standard-public-portal.md)