---
title: IP addresses in Azure App Service | Microsoft Docs
description: Describes how inbound and outbound IP addresses are used in App Service and how to find information on them for your app.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''

ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 16/04/2018
ms.author: cephalin
---

# Inbound and outbound IP addresses in Azure App Service

[Azure App Service](app-service-web-overview.md) is a multi-tenented service, except for [App Service Environments](environment/intro.md). Apps that are not in the [Isolated tier](https://azure.microsoft.com/pricing/details/app-service/) share network infrastructure with other apps. As a result, the inbound and outbound IP addresses of an app can be different, and can even change in certain situations. 

[App Service Environments](environment/intro.md) use dedicated network infrastructures, so apps running in an App Service environment get static, dedicated IP addresses both for inbound and outbound connections.

## When inbound IP changes

Regardless of the number of scaled-out instances, each app has a single inbound IP address. The inbound IP address can change when you perform one of the following actions:

- Delete and recreate an app.
- Change from a higher pricing tier back to the **Shared** tier. 
- Delete an existing SSL binding, such as during certificate renewal (see [Renew certificates](app-service-web-tutorial-custom-ssl.md#renew-certificates)).

## Get static inbound IP

Sometimes you might want a dedicated, static IP address for your app. To get a static inbound IP address, you need to configure an [IP-based SSL binding](app-service-web-tutorial-custom-ssl.md#bind-your-ssl-certificate). If you don't actually need SSL functionality to secure your app, you can even upload a self-signed certificate for this binding. In an IP-based SSL binding, App Service binds the certificate to the IP address itself, so it provisions a static IP address to make this happen. 

## When outbound IPs change

Regardless of the number of scaled-out instances, each app has four outbound IP address at any given time. Any outbound connection from the App Service app, such as to a back end database, uses one of the four outbound IP address as the origin IP address. You can't determine ahead of time which IP address a given app instance will use to make the outbound connection, so your back end service must open its firewall to all the outbound IP addresses of your app.

The set of outbound IP addresses for your app can change when you perform one of the following actions:

- Move your app to another App Service plan.
- Scale your app between the lower tiers (**Basic**, **Standard**, and **Premium**) and the **Premium V2** tier.

However, you can find the set of all possible outbound IP addresses your app can use, regardless of pricing tiers, by looking for the `possibleOutbountIPAddresses` property. See [Find outbound IPs](#find-outbound-ips).
 

## Find outbound IPs

To find the outbound IP addresses used by your app in the current pricing tier, run the following command in the [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query outboundIpAddresses --output tsv
```

To find all possible outbound IP addresses for your app, regardless of pricing tiers, run the following command in the [Cloud Shell](../cloud-shell/quickstart.md).

```azurecli-interactive
az webapp show --resource-group <group_name> --name <app_name> --query possibleOutboundIpAddresses --output tsv
```

## Next steps

Learn how to restrict inbound traffic by source IP addresses.

> [!div class="nextstepaction"]
> [Static IP restrictions](app-service-ip-addresses.md)