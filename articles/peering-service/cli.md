---
title: Register a Peering Service connection using CLI
description: Learn how to register a Peering Service connection using Azure CLI
services: peering-service
author: ypitsch
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/04/2019
ms.author: v-meravi
---

# Register the Peering Service connection using Azure CLI

*Peering Service* is a networking service that aims at enhancing customer connectivity to Microsoft Cloud services such as Office 365, Dynamics 365, SaaS services, Azure or any Microsoft services accessible via public internet. In this article, you will learn how to register a *Peering Service* connection using Azure CLI.

If you don't have an Azure subscription, create an [account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) now.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

If you choose to install and use the CLI locally, this article requires that you are running the Azure CLI version 2.0.28 or later. To find the version, run `az --version`. If you need to install or upgrade, see [Install Azure CLI](/cli/azure/install-azure-cli).

## Register the Peering Service

## Prerequisites  

### Azure account

A valid and active Microsoft Azure account. This account is required to set up the Peering Service connection. Peering Service is a resource within Azure subscriptions.

For instructions to sign in to your Azure account via CLI, refer step 1 of this article.

### Connectivity provider

   - You can work with an Internet Service provider or Internet Exchange Partner to obtain Peering Service to connect your network with Microsoft network.

   - Ensure the connectivity providers are certified.

### 1. Sign in to your Azure account and select your subscription

To begin your configuration, sign in to your Azure account. If you use the CloudShell "Try It", you are signed in automatically. Use the following examples to help you connect:

```azurecli
az login
```

Check the subscriptions for the account.

```azurecli-interactive
az account list
```

Select the subscription for which you want to register the *Peering Service* connection.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

If you don't already have a resource group, you must create one before you register your Peering Service connection. You can create a resource group by running the following command:

```azurecli-interactive
az group create -n MyResourceGroup -l "West US"
```

### 2. Register your subscription with the resource provider and feature flag

Before proceeding to the steps of registering the *Peering Service* using Azure CLI, you need to register your subscription with the resource provider and feature flag using CLI. The CLI commands are specified below:

```azurecli

az feature register --namespace Microsoft.Peering --name AllowPeeringService

```

### 3. Register the Peering Service

Register the Peering Service using the below set of commands using Azure CLI. This example registers the Peering Service named myPeeringService:

```azurecli-interactive
az peering service create : Create peering service\
  --location -l \
  --name myPeeringService\
  --resource-group -g MyResourceGroup\
  --peering-service-location\
  --peering-service-provider\
  --tags
```

### 3. Register the Prefix

Register the Prefix that is provided by the connectivity provider by executing the following commands via Azure CLI. This example registers the Prefix named myPrefix.

```azurecli-interactive
az peering service prefix create \
  --name  myPrefix\
  --peering-service-name myPeeringService\
  --resource-group  -g myResourceGroup\
```

## Next steps

To learn more about Peering Service concepts, see [Peering Service Connection](connection.md).

To measure the telemetry, see [Measure connection telemetry](measure-connection-telemetry.md).

To register the connection using PowerShell, see [Peering Service connection](powershell.md).

To register the connection using Azure portal, see [Peering Service connection](azure-portal.md).
