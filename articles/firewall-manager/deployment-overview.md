---
title: Azure Firewall Manager Preview deployment overview
description: Learn the high-level deployment steps required for Azure Firewall Manager Preview
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: overview
ms.date: 10/23/2019
ms.author: victorh
---

# Azure Firewall Manager Preview deployment overview

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

There's more than one way to deploy Azure Firewall Manager Preview, but the following general process is recommended.

## General deployment process

1. Create your hub and spoke architecture

   - Create a Secured Virtual Hub using Azure Firewall Manager and add virtual network connections.<br>*or*<br>
   - Create a Virtual WAN Hub and add virtual network connections.
2. Select security providers

   - Done while creating a Secured Virtual Hub.<br>*or*<br>
   - Convert an existing Virtual WAN Hub to Secure Virtual Hub.
3. Create a firewall policy and associate it with your hub

   - Applicable only if using Azure Firewall.
   - Third-party Network Security as a Service (NSaaS) policies are configured via partners management experience.
4. Configure route settings to route traffic to your secured hub

   - Easily route traffic to your secured hub for filtering and logging without User Defined Routes (UDR) on spoke Virtual Networks using the Secured Virtual Hub Route Setting page.

> [!NOTE]
> - You can't have more than one hub per region.
> - You can't have overlapping IP spaces for hubs in a vWAN.
> - Your hub VNet connections must be in the same region as the hub.

## Next steps

- [Tutorial: Secure your cloud network with Azure Firewall Manager Preview using the Azure portal](secure-cloud-network.md)