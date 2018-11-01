﻿---
title: Serve content from Azure Storage in App Service on Linux
description: How to configure and serve content from Azure Storage in Azure App Service on Linux.
author: msangapu
manager: jeconnoc

ms.service: app-service
ms.workload: web
ms.topic: article
ms.date: 11/01/2018
ms.author: msangapu
---
# Serve content from Azure Storage in App Service on Linux

This guide shows how to serve static content in App Service on Linux by using [Azure Storage](/azure/storage/). The benefits of moving content to Azure Storage include: more bandwidth to the web app, content portability, and multiple methods of transferring content. In this guide, you learn how to do it by [configuring custom storage](https://blogs.msdn.microsoft.com/appserviceteam/2018/09/24/announcing-bring-your-own-storage-to-app-service/).

## Prerequisites

- An existing web app (App Service on Linux or Web App for Containers).
- [Azure CLI](/cli/azure/install-azure-cli) (2.0.46 or later).

## Create Azure Storage

> [!NOTE]
> This is non-default storage and it's a separate cost than the web app.
>

Create an Azure [Azure storage account](https://docs.microsoft.com/en-us/azure/storage/common/storage-quickstart-create-account?tabs=azure-cli).

```azurecli
#Create Storage Account
az storage account create --name <storage_account_name> --resource-group myResourceGroup

#Create Storage Container
az storage container create --name <storage_container_name> --account-name <storage_account_name>
```

## Upload files

To upload a local directory to the storage account, you use the [`az storage blob upload-batch`](https://docs.microsoft.com/cli/azure/storage/blob?view=azure-cli-latest#az-storage-blob-upload-batch) command like the following example:

```azurecli
az storage blob upload-batch -d <full_path_to_local_directory> --account-name <storage_account_name> --account-key "<access_key>" -s <source_location_name>
```

## Link storage

> [!CAUTION]
> Linking an existing directory in a web app to a storage account will delete the directory contents. If you are migrating files for an existing app, make a backup of your app and its content before you begin.
>

To mount a storage account to a directory in your App Service app, you use the [`az webapp config storage-account add`](https://docs.microsoft.com/cli/azure/webapp/config/storage-account?view=azure-cli-latest#az-webapp-config-storage-account-add) command. Storage Type can be AzureBlob or AzureFiles. You use AzureBlob for this container.

```azurecli
az webapp config storage-account add --resource-group <group_name> --name <app_name> --custom-id <custom_id> --storage-type AzureBlob --share-name <share_name> --account-name <storage_account_name> --access-key "<access_key>" --mount-path <mount_path_directory>
```

You should do this for any other directories you want to be linked to a storage account.

## Verify

Once a storage container is linked to a web app, you can verify this by running the following command:

```azurecli
az webapp conf storage-account list --resource-group <group_name> --name <app_name>
```

## Next steps

- [Configure web apps in Azure App Service](https://docs.microsoft.com/en-us/azure/app-service/web-sites-configure).
