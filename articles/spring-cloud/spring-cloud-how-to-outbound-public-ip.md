---
title: How to - View the static outbound public IP addresses 
description: How to view the static outbound public IP addresses to communicate with external resources, such as Database, Storage, Key Vault, etc.
author:  MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/17/2020
ms.custom: devx-track-java
---

# How to identify outbound public IP addresses in Azure Spring Cloud

This page will show you how to view the static outbound public IP addresses of your application, which are used to communicate with external resources, such as database, storage, key vault, etc.



## How IP addresses work in Azure Spring Cloud

An Azure Spring Cloud service has one or more outbound public IP addresses. The number of outbound public IP addresses may vary according to the tiers and other factors. 

The outbound public IP addresses usually constant and stay the same, but there are exceptions.



## When outbound IPs change

Each Azure Spring Cloud instance has a set number of outbound public IP addresses at any given time. Any outbound connection from the applications, such as to a back-end database, uses one of the outbound public IP addresses as the origin IP address. The IP address is selected randomly at runtime, so your back-end service must open its firewall to all the outbound IP addresses.

The number of outbound public IPs changes when you perform one of the following actions:

- Upgrade your Azure Spring Cloud instance between tiers.
- Raise a support ticket for more outbound public IPs for business needs.

## Find outbound IPs

To find the outbound public IP addresses currently used by your service instance in the Azure portal, click **Networking** in your instance's left-hand navigation pane. They are listed in the **Outbound IP addresses** field.

You can find the same information by running the following command in the Cloud Shell

```Azure CLI
az spring-cloud show --resource-group <group_name> --name <service_name> --query properties.networkProfile.outboundIPs.publicIPs --output tsv
```

## Next steps
> [!div class="nextstepaction"]
* [Learn more about managed identities for Azure resources](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Learn more about key vault in Azure Spring Cloud](articles\spring-cloud\spring-cloud-tutorial-managed-identities-key-vault.md)
