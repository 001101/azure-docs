---
title: Create an Azure Media Services account - CLI 2.0 | Microsoft Docs
description: Follow the steps of this quickstart to create an Azure Media Services account.
services: media-services
documentationcenter: ''
author: juliako
manager: cflower
editor: ''

ms.service: media-services
ms.workload: 
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/04/2018
ms.author: juliako
---

# Quickstart: Create an Azure Media Services account

> [!NOTE]
> The latest version of Azure Media Services (2018-03-30) is in preview. This version is also called v3.

To access APIs that enable you to store, encrypt, encode, manage, and stream media content in Azure, you need to create a Media Services account. When creating a Media Services account, you need to supply the ID of an Azure Storage account resource. The specified storage account is attached to your Media Services account. This storage account resource has to be located in the same geographic region as the Media Services account. For more information, see [Storage accounts](storage-account-concept.md).

This quickstart describes the steps for creating a new Azure Media Services account using CLI 2.0.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## Log in to Azure

Log in to the Azure portal at http://portal.azure.com.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## Log in to Azure

Log in to the [Azure portal](http://portal.azure.com) and launch **CloudShell** to execute CLI commands, as shown in the next steps.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

## Set the Azure subscription

In the following command, provide the Azure subscription ID that you want to use for the Media Services account. TODO: link that shows where to get subscription ID from Portal

```azurecli-interactive
az account set --subscription <mySubscriptionId>
```

## Create an Azure Resource Group

The following command creates a resource group in which you want to have the Storage and Media Services account. Substitute the myresourcegroup placeholder with the name you want to use for your resource group.

```azurecli-interactive
az group create -n <myresourcegroup> -l westus2
```

## Create an Azure Storage account

The following command creates the Storage account that is going to be associated with the Media Services Account (primary). Substitute the storageaccountforams placeholder. TODO: what type of Storage account is this? Classic? v2?

```azurecli-interactive
az storage account create -n <storageaccountforams> -g <myresourcegroup>

```

## Create an Azure Media Services account

Below you can find the Azure CLI commands that creates a new Media Services account. You just need to replace the following highlighted values:

* myamsaccountname
* myresourcegroup
* storageaccountforams

```azurecli-interactive
az ams create -n <myamsaccountname> -g <myresourcegroup> --storage-account <storageaccountforams>
```

## <a id="access_api" />Get necessary values to access Media Services API

To start programming against Media Services, you need supply the following values:

```azurecli-interactive
az ams  ???
```

## Clean up resources

If you no longer need any of the resources in your resource group, including the Media Services account you created in this Quickstart, delete the resource group.

In the **CloudShell**, execute the following command:

```azurecli-interactive
az group delete --name myResourceGroup
```

## Next steps

> [!div class="nextstepaction"]
> [Stream a file](stream-files-dotnet-quickstart.md)
