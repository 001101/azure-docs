---
title: Create a Node.js in Azure App Service on Linux | Microsoft Docs
description: Deploy your first Node.js Hello World in Azure App Service on Linux in minutes.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''

ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 05/05/2017
ms.author: msangapu
ms.custom: mvc
---
# Create a Node.js web app in Azure App Service on Linux

> [!NOTE]
> This article deploys an app to App Service on Linux. To deploy to App Service on _Windows_, see [Create a Node.js web app in Azure](../app-service-web-get-started-nodejs.md).
>

[App Service on Linux](app-service-linux-intro.md) provides a highly scalable, self-patching web hosting service using the Linux operating system. This quickstart shows how to deploy a Node.js app to App Service on Linux using a built-in image. You create the web app with built-in image using the [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), and you use Git to deploy the Node.js code to the web app.

![Sample app running in Azure](media/quickstart-nodejs/hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## Download the sample

In the Cloud Shell, create a quickstart directory and then change to it. You use this Cloud Shell to run all the commands in this quickstart.

```bash
mkdir quickstart

cd quickstart
```

Next, run the following command to clone the sample app repository to your local machine.

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world
```

Change to the directory that contains the sample code.

```bash
cd nodejs-docs-hello-world
```

## Create a web app

Install the webapp extension, type `az extension add -n webapp`. Next you'll create the web app using the command below. Replace <app_name> with your web app name.

```bash
az webapp up -n <app_name>
```

When the web app has been created, the Azure CLI shows output similar to the following example:

```json
Creating Resource group 'appsvc_rg_Linux_CentralUS' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_CentralUS' ...
App service plan creation complete
Creating app '<app_name>' ....
Webapp creation complete
Updating app settings to enable build after deployment
Creating zip with contents of dir /home/username/quickstart/nodejs-docs-hello-world ...
Preparing to deploy and build contents to app.
Fetching changes.

Generating deployment script.
Generating deployment script.
Generating deployment script.
Running deployment command...
Running deployment command...
Running deployment command...
Deployment successful.
All done.
{
  "app_url": "https://<app_name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app_name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_CentralUS ",
  "serverfarm": "appsvc_asp_Linux_CentralUS",
  "sku": "STANDARD",
  "src_path": "/home/username/quickstart/nodejs-docs-hello-world ",
  "version_detected": "6.9",
  "version_to_create": "node|6.9"
}
```

## Browse to the app

Browse to the deployed application using your web browser. Replace <app_name> with your web app name.

```bash
http://<app_name>.azurewebsites.net
```

The Node.js sample code is running in a web app with built-in image.

![Sample app running in Azure](media/quickstart-nodejs/hello-world-in-browser.png)

**Congratulations!** You've deployed your first Node.js app to App Service on Linux.

## Update and redeploy the code

In the Cloud Shell, open the `index.js` file in the Node.js app. Type `nano index.js` and make a small change to the text in the call to `response.end`:

```nodejs
response.end("Hello Azure!");
```

Save your changes and exit nano.

You'll now redeploy the app. Substitute `<app_name>` with your web app.

```bash
azwebapp up -n <app_name>
```

Once deployment has completed, switch back to the browser window that opened in the **Browse to the app** step, and hit refresh.

![Updated sample app running in Azure](media/quickstart-nodejs/hello-azure-in-browser.png)

## Manage your new Azure web app

Go to the [Azure portal](https://portal.azure.com) to manage the web app you created.

From the left menu, click **App Services**, and then click the name of your Azure web app.

![Portal navigation to Azure web app](./media/quickstart-nodejs/nodejs-docs-hello-world-app-service-list.png)

You see your web app's Overview page. Here, you can complete basic management tasks like browse, stop, start, restart, and delete.

![App Service page in Azure portal](media/quickstart-nodejs/nodejs-docs-hello-world-app-service-detail.png)

The left menu provides different pages for configuring your app.

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## Next steps

> [!div class="nextstepaction"]
> [Node.js with MongoDB](tutorial-nodejs-mongodb-app.md)
