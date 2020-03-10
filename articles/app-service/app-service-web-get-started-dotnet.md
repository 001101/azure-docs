---
title: 'QuickStart: Create a C# ASP.NET Core app'
description: Learn how to run web apps in Azure App Service by deploying the default C# ASP.NET Core web app template from Visual Studio.
ms.assetid: b1e6bd58-48d1-4007-9d6c-53fd6db061e3
ms.topic: quickstart
ms.date: 02/28/2020
ms.custom: mvc, devcenter, vs-azure
ms.custom: seodec18

---
# Quickstart: Create an ASP.NET Core web app in Azure

> [!NOTE]
> This article deploys an app to App Service on Windows. To deploy to App Service on _Linux_, see [Create a .NET Core web app in App Service on Linux](./containers/quickstart-dotnetcore.md).
>

[Azure App Service](overview.md) provides a highly scalable, self-patching web hosting service.

In this quickstart, you'll create and deploy an ASP.NET Core web app to Azure App Service. When you're finished, you'll have a resource group that consists of an App Service plan and an App Service app with a deployed web application.

## Prerequisites

- An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Install <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> with the **ASP.NET and web development** workload.

  If you've installed Visual Studio 2019 already:

  - Install the latest updates in Visual Studio by selecting **Help** > **Check for Updates**.
  - Add the workload by selecting **Tools** > **Get Tools and Features**.


## Create an ASP.NET Core web app

Create an ASP.NET Core web app by following these steps:

1. Open Visual Studio and select **Create a new project**.

1. In **Create a new project**, find and choose **ASP.NET Core Web Application** and confirm that **C#** is listed in the languages for that choice, then select **Next**.

1. In **Configure your new project**, name your web application project *myFirstAzureWebApp*, and select **Create**.

   ![Configure your web app project](./media/app-service-web-get-started-dotnet/configure-web-app-project.png)

1. Choose the **Web Application** template. Make sure Authentication is set to **No Authentication** and no other option is selected. Select **Create**.

   ![Select ASP.NET Core Razor Pages for this tutorial](./media/app-service-web-get-started-dotnet/aspnet-razor-pages-app.png)

   You can deploy any type of ASP.NET Core web app to Azure. 
   
1. From the Visual Studio menu, select **Debug** > **Start Without Debugging** to run the web app locally.

   ![Run app locally](./media/app-service-web-get-started-dotnet/razor-web-app-running-locally.png)

## Publish your web app

1. In **Solution Explorer**, right-click the **myFirstAzureWebApp** project and select **Publish**.

1. In the **Pick a publish target** dialog box, choose **App Service** and then select **Create New**.

   ![Publish from project overview page](./media/app-service-web-get-started-dotnet/publish-app-vs2019.png)

1. Select **Create Profile**. If you haven't already signed-in to your Azure account from Visual Studio, select either **Add an account** or **Sign in**. You can also create a free Azure account.

1. In the **App Service: Create new** dialog, use the values specified in the following table:

| Setting             | Value                     | Description                                                          |
| ------------------- | ------------------------- | ---------------------------------------------------------------------|
| **Name**            | Globally unique name      | A Name that uniquely identifies your new app. Accept this name or enter a new name. Valid characters are: `a-z`, `A-Z`, `0-9`, and `-`.                                                                                      |
| **Subscription**    | Your subscription         | The Azure subscription to use. Accept this subscription or select a new one from the drop-down list.                                                                                                |
| **[Resource Group](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/overview#terminology)**  | *myResourceGroup* | Select **New**. In **New resource group name**, enter *myResourceGroup* and select **OK**.  |
| **[Hosting Plan](https://docs.microsoft.com/en-us/azure/app-service/overview-hosting-plans)**  | *myFirstAzureWebAppPlan* | Select **New**. In **Hosting Plan: Create new**, enter a **Hosting Plan** of *myFirstAzureWebAppPlan*, a **Location** of *West Europe*, and a **Size** of *Free*.                                                                               |
| **Application Insights**   | None  |  Additional information about your web app.                                         |

![Configure app name](./media/app-service-web-get-started-dotnet/web-app-name-vs2019.png)

1. Select **Create** to start creating the Azure resources. Once the wizard completes, deploy your web app by selecting **Publish**.

   ![Deploy web app to Azure](./media/app-service-web-get-started-dotnet/deploy-web-app.png)

This publishes your ASP.NET Core web app to Azure, and launches the app in your default browser.

![Published ASP.NET web app in Azure](./media/app-service-web-get-started-dotnet/web-app-running-live.png)

The app name specified in the **App Service Create new** page is used as the URL prefix in the format `http://<app_name>.azurewebsites.net`.

**Congratulations!** Your ASP.NET Core web app is running live in Azure App Service.

## Update the app and redeploy

1. In **Solution Explorer**, under your project, open **Pages** > **Index.cshtml**.

1. Replace the two `<div>` tags with the following code:

   ```HTML
   <div class="jumbotron">
       <h1>ASP.NET in Azure!</h1>
       <p class="lead">This is a simple app that we've built that demonstrates how to deploy a .NET app to Azure App Service.</p>
   </div>
   ```

1. To redeploy to Azure, right-click the **myFirstAzureWebApp** project in **Solution Explorer** and select **Publish**.

1. In the **Publish** summary page, select **Publish**.

   ![Visual Studio publishing summary page](./media/app-service-web-get-started-dotnet/publish-summary-page-vs2019.png)

When publishing completes, Visual Studio launches a browser to the URL of the web app.

![Updated ASP.NET web app in Azure](./media/app-service-web-get-started-dotnet/web-app-running-live-updated.png)

## Manage the Azure app

To manage the web app, go to the [Azure portal](https://portal.azure.com), and search for and select **App Services**.

![Select App Services](./media/app-service-web-get-started-dotnet/app-services.png)

On the **App Services** page, select the name of your web app.

![Portal navigation to Azure app](./media/app-service-web-get-started-dotnet/access-portal-vs2019.png)

This opens your web app's **Overview** page, containing options for basic management like browse, stop, start, restart, and delete. The left menu provides further pages for configuring your app.

![App Service in Azure portal](./media/app-service-web-get-started-dotnet/web-app-general-vs2019.png)

[!INCLUDE [Clean-up section](../../includes/clean-up-section-portal.md)]

## Next steps

In this quickstart, you used Visual Studio to create and deploy an ASP.NET Core web app to Azure App Service.

Advance to the next article to learn how to create a .NET Core app and connect it to a SQL Database:

> [!div class="nextstepaction"]
> [ASP.NET Core with SQL Database](app-service-web-tutorial-dotnetcore-sqldb.md)
