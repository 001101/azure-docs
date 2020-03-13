---
title: Managed virtual network in Azure Synapse Analytics
description: An article that explains Managed virtual network in Azure Synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics 
ms.topic: Synapse Managed VNet
ms.date: 03/12/2020
ms.author: ronytho
ms.reviewer: jrasnick
---



# Azure Synapse Analytics Managed Virtual Network (preview)
<!---Required: 
For the H1 - that's the primary heading at the top of the article - use the format "What is <service>?"
You can also use this in the TOC if your service name doesn't cause the phrase to wrap.
--->
This article will explain Managed Virtual Network in Azure Synapse Analytics
## Managed workspace VNet
When you create your Synapse workspace, you can choose to associate it to a VNet. The VNet associated with your workspace is managed by Azure Synapse. This VNet is called a Managed workspace VNet. 
Managed workspace VNet provides you value in four ways. First, with a Managed workspace VNet you can offload the burden of managing the VNet to Synapse. Second, you don't have to configure inbound NSG rules on your own VNets to allow Synapse management traffic to enter your VNet. Misconfiguration of these NSG rules causes service disruption for customers. Thirdly, you don't need to create a subnet for your Spark clusters based on peak load. Finally, Managed workspace VNet along with Managed private endpoints protects against data exfiltration. You can only create Managed private endpoints in a workspace that has a Managed workspace VNet associated with it.

Creating a workspace with a Managed workspace VNet associated with it ensures that your workspace is network isolated from other workspaces. Azure Synapse provides various analytic capabilities in a workspace: Data integration, Apache Spark, SQL pools, and SQL on-demand. If your workspace has a Managed workspace VNet, Data integration and Spark resources are deployed in it. A Managed workspace VNet also provides user-level isolation for Spark activities because each Spark cluster is in its own subnet. SQL pools and SQL on-demand are multi-tenant capabilities and therefore reside outside of the Managed workspace VNet. Intra-workspace communication to SQL pools and SQL on-demand use Azure private links. These private links are automatically created for you when you create a workspace with a Managed workspace VNet associated to it.

[!IMPORTANT]
You cannot change this workspace configuration after the workspace is created. For example, you cannot reconfigure a workspace that does not have a Managed workspace VNet associated with it and associate a VNet to it. Similarly, you cannot reconfigure a workspace with a Managed workspace VNet associated to it and disassociate the VNet from it.

## Create a Synapse workspace with a Managed workspace VNet

To create a Synapse workspace that has a Managed workspace VNet associated with it, select the **Security + networking** tab in Azure portal and check the **Enable managed virtual network** checkbox. If you leave the checkbox unchecked, then your workspace won't have a VNet associated with it. 

[!IMPORTANT]
**You can only use private links in a workspace that has a Managed workspace VNet**.

![Enable Managed workspace VNet](../media/security/enable-managed-vnet-1.png).

[!NOTE] All outbound traffic from the Managed workspace VNet will be blocked in the future. It's recommended that you connect to all your data sources using Managed private endpoints.   

<!--- Need to add next steps here.
## Next steps
--->
