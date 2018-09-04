---
title: Azure DNS alias records overview
description: Overview of support for alias records in Microsoft Azure DNS.
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 9/25/2018
ms.author: victorh
---

# Azure DNS alias records overview

Azure DNS alias records are a custom record type that allows you to reference other Azure resources from within your DNS zone for both forward and reverse lookups. For example, can create an alias record that references an Azure Public IP address instead of creating an A record. Since your alias record points to the service instance (an Azure Public IP address), the alias record set seamlessly updates itself during DNS resolution. It points to the service instance, which has the actual IP address associated with it.

Other Azure services benefit from this Azure DNS integration too. For example, Azure Traffic Manager can use CNAMEs to direct your traffic to the Traffic Manager profile. However, since you can't create a CNAME for a domain apex (for example, contoso.com) it is not possible to redirect direct contoso.com traffic to Traffic Manager. But you can use an alias record to solve this problem, because you can create an alias record for the domain apex name and point the alias record to the Traffic Manager profile.

> [!NOTE]
> Alias records for the A or AAAA record types for Traffic Manager are only supported for External Endpoint types. You must provide the IPv4 or IPv6 address (ideally static IPs) as appropriate for external endpoints in Traffic Manager.
