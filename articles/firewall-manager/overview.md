---
title: What is Azure Firewall Manager Preview?
description: Learn about Azure Firewall Manager features
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 10/23/2019
ms.author: victorh
---

# What is Azure Firewall Manager Preview?

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure Firewall Manager Preview is a security management service that provides central security policy and route management for cloud-based security perimeters. It works with [Azure Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources), a  Microsoft-managed resource that lets you easily create hub and spoke architectures. When security and routing policies are associated with such a hub, it is referred to as a *[secured virtual hub](secured-virtual-hub.md)*. 

![firewall-manager](media/overview/firewall-manager-conceptual.png)

## Azure Firewall Manager Preview features

Azure Firewall Manager Preview offers the following features:

### Central Azure Firewall deployment and configuration​

You can centrally deploy and configure multiple Azure Firewall instances that span different Azure regions and subscriptions. 

### Hierarchical policies (global and local)​

You can use Azure Firewall Manager Preview to centrally manage Azure Firewall policies across multiple secured virtual hubs. Your central IT teams can author global firewall policies to enforce organization wide firewall policy across teams. Locally authored firewall policies allow a DevOps self-service model for better agility.

### Integrated with third-party NSaaS for advanced security

In addition to Azure Firewall, you can integrate third-party NSaaS (Network Security as a Service) providers to provide additional network protection for your VNet and branch Internet connections.

- VNet to Internet (V2I) traffic filtering

   Filter outbound virtual network traffic with your preferred third-party NSaaS provider.
- Branch to Internet (B2I) traffic filtering

   Leverage your Azure connectivity and global distribution to easily add third-party NSaaS filtering for branch to Internet scenarios.


### Centralized route management

Easily route traffic to your secured hub for filtering and logging without the need to manually set up User Defined Routes (UDR) on spoke virtual networks. You can use third-party NSaaS providers for Branch to Internet (B2I) traffic filtering, side by side with Azure Firewall for Branch to VNet (B2V), VNet to VNet (V2V) and VNet to Internet (V2I). You can also use third-party NSaaS providers for V2I traffic filtering as long as Azure Firewall is not required for B2V or V2V. 

## Known issues

Azure Firewall Manager Preview has the following known issues:

|Issue  |Description  |Mitigation  |
|---------|---------|---------|
|Manually created central VNets not supported|Currently, Azure Firewall Manager supports networks created with Virtual Hubs. Using your own manually created hub VNet is not yet supported.|For now, use Azure Firewall Manager with hub and spoke networks created with Virtual Hubs<br>Currently investigating.
|Third-party NSaaS filtering limitations|V2I traffic filtering with third-party NSaaS providers is not supported with Azure Firewall B2V and V2V.|It is possible to have two secured virtual hubs side by side, where one hub is for V2I with a third-party NSaaS providers and the other is with Azure Firewall for B2V and V2V.<br> Currently investigating.|
|Traffic splitting not currently supported|Office 365 and Azure Public PaaS traffic splitting is not currently supported. As such, selecting a third-party NSaaS provider for V2I or B2I also sends all Azure Public PaaS and Office 365 traffic via the partner service.|Currently investigating traffic splitting at the hub.
|One hub per region|You cannot have more than one hub per region|Create multiple virtual WANs in a region.

## Next steps

- Review [Azure Firewall Manager Preview deployment overview](deployment-overview.md)
- Learn about [secured Virtual Hubs](secured-virtual-hub.md).