---
title: Create your first function on Linux from the Azure CLI | Microsoft Docs 
description: Learn how to create your first Azure Function running on a default Linux image for serverless execution using the Azure CLI.
services: functions 
keywords: 
author: ggailey777
ms.author: glenga
ms.date: 10/30/2017
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: azure-cli
manager: cfowler
---

# Create your first function running on Linux using the Azure CLI

This quickstart tutorial walks through how to use Azure Functions to create your first function on Linux. You use the Azure CLI to create a function app running on a built-in image. This app is the [serverless](https://azure.microsoft.com/overview/serverless-computing/) infrastructure that hosts your function. The function code itself is deployed to the image from a GitHub sample repository.    

You can follow the steps below using a Mac, Windows, or Linux computer. 

## Prerequisites 

Before running this sample, you must have the following:

+ An active [GitHub](https://github.com) account. 
+ An active Azure subscription.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

If you choose to install and use the CLI locally, this topic requires the Azure CLI version 2.0 or later. Run `az --version` to find the version you have. If you need to install or upgrade, see [Install Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## Create a Linux App Service plan

Linux hosting for Functions is currently not supported on consumption plans. You must run on a Linux App Service plan. To learn more about hosting, see [Azure Functions hosting plans comparison](functions-scale.md). 

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## Create a function app with a built-in image

You must have a function app to host the execution of your functions. The function app provides an environment for serverless execution of your function code. It lets you group functions as a logic unit for easier management, deployment, and sharing of resources. Create a function app by using the [az functionapp create](/cli/azure/functionapp#create) command. 

In the following command, substitute a unique function app name where you see the `<app_name>` placeholder and the storage account name for  `<storage_name>`. The `<app_name>` is used as the default DNS domain for the function app, and so the name needs to be unique across all apps in Azure. The _deployment-source-url_ parameter is a sample repository in GitHub that contains a "Hello World" HTTP triggered function.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myAppServicePlan --deployment-source-url https://github.com/Azure-Samples/functions-quickstart
```
After the function app has been created, the Azure CLI shows information similar to the following example:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

Because `myAppServicePlan` is a Linux plan, the built-in image is used to create the function app. Now that you have a function app, you can deploy the actual function code from the GitHub sample repository.

[!INCLUDE [functions-deploy-your-function-code](../../includes/functions-deploy-your-function-code.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
