---
title: Automate the resizing of uploaded images using Event Grid | Microsoft Docs
description: Azure Event Grid can trigger on blob uploads in Azure Storage. You can use this to send uploaded image files to other services, such as Azure Functions, for resizing and other improvements.
services: event-grid
author: ggailey777
manager: cfowler
editor: ''

ms.service: event-grid
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: glenga
ms.custom: mvc
---
# Automate the resizing of uploaded images using Event Grid

Azure Event Grid is an eventing service for the cloud. Event Grid lets you send events raised by Azure services or third-party resources to an endpoint that can respond to the event. In this article, you use Event Grid to connect Azure Functions with Azure storage to support thumbnail generation for images uploaded from a sample app. You use the Azure CLI to create and configure the application topology. 

![Published web app in Edge browser](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png) 

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Create an Azure Storage account
> * Define blob containers in Azure Storage
> * Deploy serverless code using Azure Functions
> * Create a custom Event Grid topic 
> * Subscribe to an Event Grid topic
> * Deploy a web app to Azure

## Prerequisites

To complete this tutorial:

+ You must have an active Azure subscription.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

If you choose to install and use the CLI locally, this topic requires that you are running the Azure CLI version 2.0 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI 2.0]( /cli/azure/install-azure-cli). 

If you are not using Cloud Shell, you must first sign in using `az login`.

## Create a resource group

Create a resource group with the [az group create](/cli/azure/group#create). An Azure resource group is a logical container into which Azure resources like topics and subscriptions, function apps, and storage accounts are deployed and managed.

The following example creates a resource group named `myResourceGroup`.  

```azurecli-interactive
az group create --name myResourceGroup --location westeurope
```

## Create an Azure Storage account

The sample uploads images to a blob container in an Azure Storage account. Create a storage account in the resource group you created by using the [az storage account create](/cli/azure/storage/account#create) command.

In the following command, substitute your own globally unique storage account name where you see the `<storage_account>` placeholder. Storage account names must be between 3 and 24 characters in length and may contain numbers and lowercase letters only.

```azurecli-interactive
az storage account create --name <storage_account> \
--location westeurope --resource-group myResourceGroup \
--sku Standard_LRS
```

## Configure storage

The app uses two blob containers. The _images_ container is where  the app uploads full-resolution images. The function uploads resized image thumbnails to the _thumbs_ container. Get the storage account key by using the [storage account keys list](/cli/azure/storage/account/keys#list) command. You then use this key to create two containers using the [az storage container create](/cli/azure/storage/container#create) command. 

In this case, `<storage_account>` is the name of the storage account you created.

```azurecli-interactive
storageaccount=<storage_account>

storageAccountKey=$(az storage account keys list -g myResourceGroup \
-n $storageaccount --query [0].value --output tsv)

az storage container create -n images --account-name $storageaccount \ 
--account-key $storageAccountKey

az storage container create -n thumbs --account-name $storageaccount \ 
--account-key $storageAccountKey

echo "Make a note of your storage account key..."
echo $storageaccountkey
```
Make a note of your storage account name and key. The sample app uses these settings to connect to the storage account to upload images.

## Create a function app  

You must have a function app to host the execution of your functions. The function app provides an environment for serverless execution of your function code. Create a function app by using the [az functionapp create](/cli/azure/functionapp#create) command. 

In the following command, substitute your own unique function app name where you see the `<function_app_name>` placeholder. The `<function_app>` is used as the default DNS domain for the function app, and so the name needs to be unique across all apps in Azure. In this case, `<storage_account>` is the name of the storage account you created.  

```azurecli-interactive
az functionapp create --name <function_app> --storage-account  <storage_account>  \
--resource-group myResourceGroup --consumption-plan-location westeurope
```
You can now deploy a function code project to this function app.

## Deploy the function code 

The C# function that performs image resizing is available in a public GitHub repo: <https://github.com/Azure-Samples/function-image-upload-resize>. Deploy to the function app by using the [az functionapp deployment source config](/cli/azure/functionapp/deployment/source#config) command. 

In the following command, `<function_app>` is the same function app you created in the previous script.

```azurecli-interactive
az functionapp deployment source config --name <function_app> \
 --resource-group myResourceGroup \
 --repo-url https://github.com/Azure-Samples/function-image-upload-resize \
 --branch master --manual-integration
```

The function code is deployed directly from the public sample repo. To learn more about deployment options for Azure Functions, see [Continuous deployment for Azure Functions](../azure-functions/functions-continuous-deployment.md).

## Create an event subscription in Event Grid

An event subscription tells Event Grid which events you want to send to your function. Create an event subscription by using the `az eventgrid resource event-subscription create` command. In the following command, `<function_app>` is the function app you created and `<storage_account>` is your storage account. 

```azurecli-interactive
az eventgrid resource event-subscription create -g myResourceGroup \  
--provider-namespace Microsoft.Storage --resource-type storageAccounts \  
--resource-name <storage_account> --name myFuncSub  --subject-begins-with images \  
--endpoint https://<function_app>.azurewebsites.net/api/imageresizefunc \ 
```

The prefix filter value `image` filters storage events to only those on the **images** container.  

Now that the backend services are configured, you can publish the sample web app to Azure. 

## Create an App Service plan

An [App Service plan](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) specifies the location, size, and features of the web server farm that hosts your app.
Create an App Service plan with the [az appservice plan create](/cli/azure/appservice/plan#create) command.

The following example creates an App Service plan named `myAppServicePlan` in the **Free** pricing tier:

```azurecli-interactive
az appservice plan create --name myAppServicePlan  --resource-group myResourceGroup --sku FREE
```

## Create a web app

The web app provides a hosting space for the sample app code that is deployed from the GitHub sample repository. Create a [web app](../app-service-web/app-service-web-overview.md) in the `myAppServicePlan` App Service plan with the [az webapp create](/cli/azure/webapp#create) command. 

In the following command, replace *\<app_name>* with a unique name (valid characters are `a-z`, `0-9`, and `-`). If `<web_app>` is not unique, you get the error message: _Website with given name <web_app> already exists._ The default URL of the web app is `https://<web_app>.azurewebsites.net`. 

```azurecli-interactive
az webapp create --name <web_app> --resource-group myResourceGroup --plan myAppServicePlan
```

## Deploy the sample app from the GitHub repository

App Service supports several ways to deploy content to a web app. In this tutorial, you  deploy the web app from a public GitHub sample repository: <https://github.com/Azure-Samples/integration-image-upload-resize-storage-functions>. Configure GitHub deployment to the web app with the [az webapp deployment source config](/cli/azure/webapp/deployment/source#config) command. 

```azurecli-interactive
az webapp deployment source config --name <web_app>  
--resource-group myResourceGroup \  
--repo-url https://github.com/Azure-Samples/integration-image-upload-resize-storage-functions \ 
--branch master --manual-integration
```

## Configure web app settings

The sample web app uses the Azure Storage SDK to request access tokens, which are used to upload images. The storage account credentials used by the Storage SDK are set in the application settings for the web app. Add application settings to the deployed app with the [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#set) command.

```azurecli-interactive
az webapp config appsettings set --name <web_app> --resource-group myResourceGroup \
--settings AzureStorageConfig__AccountName=<storage_account> \  
AzureStorageConfig__AccountKey=<storage_key> \  
AzureStorageConfig__ImageContainer=images  \  
AzureStorageConfig__ThumbnailContainer=thumbs
```
After the web app is deployed and configured, you can test the entire image upload and resizing functionality in the app.  

## Test the sample app

To test the web app, browse to the URL of your published app as The default URL of the web app is `https://<web_app>.azurewebsites.net>`.

Click the **Upload photos** region to select and upload a file. 

Notice that a thumbnail copy of the uploaded image is displayed in the **Generated thumbnails** carousel. 

![Published web app in Edge browser](./media/resize-images-on-storage-blob-upload-event/tutorial-completed.png) 

## Next Steps

In this tutorial, you learned how to:

> [!div class="checklist"]
> * Create an Azure Storage account
> * Define blob containers in Azure Storage
> * Deploy serverless code using Azure Functions
> * Create a custom Event Grid topic 
> * Subscribe to an Event Grid topic
> * Deploy a web app to Azure

Advance to the next tutorial to learn how to do something even better.

> [!div class="nextstepaction"]
> 