---
title: Azure File Sync on-premises firewall and proxy settings | Microsoft Docs
description: Azure File Sync on-premises network configuration
services: storage
documentationcenter: ''
author: fauhse
manager: klaasl
editor: tamram

ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/26/2018
ms.author: fauhse
---

# Azure File Sync proxy and firewall settings
Azure File Sync connects your on-premises servers to Azure storage, enabling multi-site synchronization and cloud tiering features. As such, an on-premises server must be connected to the internet. An IT admin needs to decide the best path for the server to take to reach into Azure cloud services.

This article will provide insight into specific requirements and options available to successfully and securely connect your server to Azure File Sync.

## Overview
Communication with Azure for the purposes of file sync is not monolithic. There are multiple services in Azure, all with individual endpoints to which the server must be enabled to communicate.

For instance:
- Azure Storage
- Azure File Sync
- Azure Resource Manager
- Authentication services

> [!Note]  
> With Azure File Sync all calls are made by the server (outbound). None of the involved services ever initiate communication to the server.

## Ports
All required communication uses port 443(SSL) exclusively.
Ensure that this port is open, outbound.

## Networks and special connections to Azure
The Azure File Sync agent has no requirements regarding special channels like [ExpressRoute](../../expressroute/expressroute-introduction.md), etc. to Azure.

File sync will work through any means available that allow reach into Azure, automatically adapting to various network characteristics like bandwidth, latency as well as offering admin control for fine-tuning. *some features are not yet available

## Proxy support
Azure File Sync currently supports machine-wide proxy settings. This proxy setting is transparent to the Azure File Sync agent as the entire traffic of the server is routed through this proxy.

Soon the Azure File Sync agent installer will allow for configuration of app-specific proxy settings. This will allow an admin to configure a proxy specifically for Azure File Sync traffic.

## Firewall
As mentioned before, port 443 needs to be open outbound. Based on policies in your datacenter, branch or region, further restricting traffic over this port to specific domains may be desired or required. Certain challenges come attached with that.

The following table describes the required domains for communication:

| Service | Domain | Usage |
|---------|----------------|------------------------------|
| **Azure Resource Manager** | https://management.azure.com | Any user call (like PowerShell) goes to/through this URL, incl. the initial server registration call. |
| **Azure Active Directory** | https://login.windows.net | Azure Resource Manager calls must be made by an authenticated user. To succeed, this URL is used for user authentication. |
| **Azure Active Directory** | https://graph.windows.net/ | A service principal in the Azure Active Directory of the used subscription for delegating a minimal set of rights to the Azure File Sync service. This must be done once, when Azure File Sync is set up for the fuirst time. This action must be performed by an authenticated user with subscription owner privileges. |
| **Azure Storage** | *.core.windows.net | When the server downloads a file (sync or recall in the cloud tiering case), then the server performs that more efficiently when talking directly to the Azure File Share in the Storage Account. The server has a SAS key that only allows for targeted file share access. |
| **Azure File Sync** | *.one.microsoft.com | After initial server registration, the server will be given a regional URL of the Azure File Sync service instance in that region. The server will use it to communicate directly and efficiently with the instance handling its sync. |

> [!Note]
> The subdomain under *.one.microsoft.com can change for the server. That is the reason it is not further defined here. If this instance experiences a service interruption, another region takes over (business continuity case). So for instance WestEurope has Kailani6.one.microsoft.com but that can change – as a result it is not recommended to limit traffic beyond the described level in the domain.

> [!Important]
> When allowing traffic to *.one.microsoft.com, traffic to more than just the sync service is possible from the server. There are many more Microsoft services available under a subdomain.

## Summary and risk limitation
The list earlier in this document contains the URLs Azure File Sync currently communicates with. Firewalls must be able to allow traffic outbound to these domains as well as responses from them. We strive to keep this list updated.

Setting up domain restricting firewall rules can be a measure to improve security. If these firewall configurations are used, one needs to keep in mind that URLs can change. Therefore it is a prudent measure to check the table in this document as part of a change management process from one Azure File Sync agent version to another on a test-deployment of the latest agent. This way you can ensure that your firewall is configured to allow traffic to domains the most recent agent requires.

## Next steps
- [Planning for an Azure File Sync deployment](storage-sync-files-planning.md)
- [Deploy Azure File Sync (preview)](storage-sync-files-deployment-guide.md)