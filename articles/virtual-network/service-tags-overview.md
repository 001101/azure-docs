---
title: Azure service tags overview
titlesuffix: Azure Virtual Network
description: Learn about service tags. Service tags help minimize the complexity of security rule creation.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2019
ms.author: jispar
ms.reviewer: kumud
---

# Virtual network service tags 
<a name="network-service-tags"></a>

A service tag represents a group of IP address prefixes from a given Azure service. It helps to minimize the complexity of frequent updates to network security rules. You can use service tags to define network access controls on [network security groups](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) or [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags). You can use service tags in place of specific IP addresses when you create security rules. By specifying the service tag name (for example, **ApiManagement**) in the appropriate *source* or *destination* field of a rule, you can allow or deny the traffic for the corresponding service. Microsoft manages the address prefixes encompassed by the service tag and automatically updates the service tag as addresses change.

## Available service tags
The following table includes all the service tags available for use in [network security groups](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) rules.

The columns indicate whether the tag:

- Is suitable for rules that cover inbound or outbound traffic.
- Supports [regional](https://azure.microsoft.com/regions) scope.
- Is usable in [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags) rules.

By default, service tags reflect the ranges for the entire cloud. Some service tags also allow more granular control by restricting the corresponding IP ranges to a specified region. For example, the service tag **Storage** represents Azure Storage for the entire cloud, but **Storage.WestUS** narrows the range to only the storage IP address ranges from the WestUS region. The table indicates whether each service tag supports such regional scope.  

| Tag | Purpose | Can use inbound or outbound? | Can be regional? | Can use with Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Management traffic for APIM-dedicated deployments. | Both | No | Yes |
| **AppService**    | App Service. This tag is recommended for outbound security rules to Web App front ends. | Outbound | Yes | Yes |
| **AppServiceManagement** | Management traffic for deployments dedicated to App Service Environment. | Both | No | Yes |
| **AzureActiveDirectory** | Azure Active Directory. | Outbound | No | Yes |
| **AzureActiveDirectoryDomainServices** | Management traffic for deployments dedicated to Azure Active Directory Domain Services. | Both | No | Yes |
| **AzureBackup** |Azure Backup.<br/><br/>*Note:* This tag has a dependency on the **Storage** and **AzureActiveDirectory** tags. | Outbound | No | Yes |
| **AzureCloud** | All [datacenter public IP addresses](https://www.microsoft.com/download/details.aspx?id=41653). | Outbound | Yes | Yes |
| **AzureConnectors** | Logic Apps connectors for probe/back-end connections. | Inbound | Yes | Yes |
| **AzureContainerRegistry** | Azure Container Registry. | Outbound | Yes | Yes |
| **AzureCosmosDB** | Azure Cosmos DB. | Outbound | Yes | Yes |
| **AzureDataLake** | Azure Data Lake. | Outbound | No | Yes |
| **AzureHDInsight** | Azure HDInsight. | Inbound | Yes | No |
| **AzureIoTHub** | Azure IoT Hub. | Outbound | No | No |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Note:* This tag has a dependency on the **AzureActiveDirectory** tag. | Outbound | Yes | Yes |
| **AzureLoadBalancer** | The Azure infrastructure load balancer. The tag translates to the [virtual IP address of the host](security-overview.md#azure-platform-considerations) (168.63.129.16) where the Azure health probes originate. If you're not using the Azure Load Balancer, you can override this rule. | Both | No | No |
| **AzureMachineLearning** | Azure Machine Learning. | Outbound | No | Yes |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon, and custom metrics (GiG endpoints).<br/><br/>*Note:* For Log Analytics, this tag has a dependency on the **Storage** tag. | Outbound | No | Yes |
| **AzurePlatformDNS** | The basic infrastructure (default) DNS service.<br/><br>You can use this tag to disable the default DNS. Be cautious when you use this tag. We recommend that you read [Azure platform considerations](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). We also recommend that you perform testing before you use this tag. | Outbound | No | No |
| **AzurePlatformIMDS** | IMDS, which is a basic infrastructure service.<br/><br/>You can use this tag to disable the default IMDS. Be cautious when you use this tag. We recommend that you read [Azure platform considerations](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). We also recommend that you perform testing before you use this tag. | Outbound | No | No |
| **AzurePlatformLKM** | Windows licensing or key management service.<br/><br/>You can use this tag to disable the defaults for licensing. Be cautious when you use this tag. We recommend that you read [Azure platform considerations](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations). We also recommend that you perform testing before you use this tag. | Outbound | No | No |
| **AzureTrafficManaged** | Azure Traffic Manager probe IP addresses.<br/><br/>For more information on Traffic Manager probe IP addresses, see [Azure Traffic Manager FAQ](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Inbound | No | Yes |  
| **BatchNodeManagement** | Management traffic for deployments dedicated to Azure Batch. | Both | No | Yes |
| **CognitiveServicesManagement** | The address ranges for traffic for Cognitive Services. | Outbound | No | No |
| **Dynamics365ForMarketingEmail** | The address ranges for the marketing email service of Dynamics 365. | Outbound | Yes | No |
| **EventHub** | Azure EventHub service. | Outbound | Yes | Yes |
| **GatewayManager** | Management traffic for VPN/App Gateways dedicated deployments. | Inbound | No | No |
| **Internet** | The IP address space that is outside the virtual network and reachable by the public Internet.<br/><br/>The address range includes the [Azure owned public IP address space](https://www.microsoft.com/download/details.aspx?id=41653). | Both | No | No |
| **MicrosoftContainerRegistry** | Microsoft Container Registry service. | Outbound | Yes | Yes |
| **ServiceBus** | Azure Service Bus service using the Premium service tier. | Outbound | Yes | Yes |
| **ServiceFabric** | Service Fabric service. | Outbound | No | No |
| **Sql** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL, and Azure SQL Data Warehouse services.<br/><br/>*Note:* This tag represents the service, but not specific instances of the service. For example, the tag represents the Azure SQL Database service, but not a specific SQL database or server. | Outbound | Yes | Yes |
| **SqlManagement** | Management traffic for SQL dedicated deployments. | Both | No | Yes |
| **Storage** | Azure Storage service. <br/><br/>*Note:* The tag represents the service, but not specific instances of the service. For example, the tag represents the Azure Storage service, but not a specific Azure Storage account. | Outbound | Yes | Yes |
| **VirtualNetwork** | The virtual network address space (all IP address ranges defined for the virtual network), all connected on-premises address spaces, [peered](virtual-network-peering-overview.md) virtual networks, or virtual network connected to a [virtual network gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), the [virtual IP address of the host](security-overview.md#azure-platform-considerations) and address prefixes used on [user-defined routes](virtual-networks-udr-overview.md). Be aware that this tag may also contain default routes. | Both | No | No |

>[!NOTE]
>When working in the *Classic* (pre-Azure Resource Manager) environment, a select set of the above tags are supported.  These use an alternative spelling:

| Classic spelling | Equivalent Resource Manager tag |
|---|---|
| AZURE_LOADBALANCER | AzureLoadBalancer |
| INTERNET | Internet |
| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Service tags of Azure services denotes the address prefixes from the specific cloud being used. e.g. the underlying IP ranges corresponding to the **Sql** tag value will differ between the Azure Public cloud and the Azure China cloud.

> [!NOTE]
> If you implement a [virtual network service endpoint](virtual-network-service-endpoints-overview.md) for a service, such as Azure Storage or Azure SQL Database, Azure adds a [route](virtual-networks-udr-overview.md#optional-default-routes) to a virtual network subnet for the service. The address prefixes in the route are the same address prefixes, or CIDR ranges, as the corresponding service tag.



## Service tags in on-premises  
You can obtain the current service tag and range information to include as part of your on-premises firewall configurations.  This information is the current point-in-time list of the IP ranges corresponding to each service tag.  The information can be obtained programmatically or via JSON file download as follows.

### Service tag Discovery API (Public Preview)
You can programmatically retrieve the current list of service tags with IP address range details:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> While in Public Preview, the Discovery API may return information that is not as current as the JSON downloads (below).


### Discover service tags using downloadable JSON files 
You can download JSON files containing the current list of service tags with IP address range details. These are updated and published weekly.  Locations for each cloud are:

- [Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>A subset of this information has previously been published in XML files for [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure China](https://www.microsoft.com/download/details.aspx?id=42064) and [Azure Germany](https://www.microsoft.com/download/details.aspx?id=54770). These XML downloads will be deprecated by June 30, 2020 and will no longer be available after that date. Please migrate to using the Discovery API or JSON file downloads as described above.

### Tips 
- You can detect updates from one publishing to the next via increased *changeNumber* values within the JSON file. Each subsection (e.g. **Storage.WestUS**) has its own *changeNumber* that is incremented as changes occur.  The top level of the file's *changeNumber* is incremented when any of the subsections has changed.
- For examples of how to parse the service tag information (e.g. get all address ranges for Storage in WestUS), please refer to the [Service Tag Discovery API PowerShell](https://aka.ms/discoveryapi_powershell) documentation.

## Next steps
- Learn how to [Create a network security group](tutorial-filter-network-traffic.md).

