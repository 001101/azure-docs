---
title: "Tutorial: Building a static web app with Blazor"
description: Learn to build an Azure Static Web Apps website with Blazor.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic:  tutorial
ms.date: 09/10/2020
ms.author: cshoe
---

# Tutorial: Building a static web app with Blazor

Azure Static Web Apps publishes a website to a production environment by building apps from a GitHub repository. In this tutorial, you deploy a web application to Azure Static Web apps using the portal.

If you don't have an Azure subscription, [create a free trial account](https://azure.microsoft.com/free).

## Prerequisites

- [GitHub](https://github.com) account
- [Azure](https://portal.azure.com) account

## Create a repository

This article uses a GitHub template repository to make it easy for you to get started. The template features a starter app used to deploy using Azure Static Web Apps.

1. Make sure you're signed in to GitHub and navigate to the following location to create a new repository:
    1. https://github.com/staticwebdev/blazor-starter/generate
1. Name your repository **my-first-static-blazor-app**

## Create a static web app

Now that the repository is created, you can create a static web app from the Azure portal.

1. Navigate to the [Azure portal](https://portal.azure.com)
1. Select **Create a Resource**
1. Search for **Static Web Apps**
1. Select **Static Web Apps (Preview)**
1. Select **Create**

In the _Basics_ section, begin by configuring your new app and linking it to a GitHub repository.

:::image type="content" source="media/deploy-blazor/basics.png" alt-text="Basics tab":::

1. Select your _Azure subscription_
1. Select or create a new _Resource Group_
1. Name the app **my-first-static-blazor-app**.
      1. Valid characters are `a-z` (case insensitive), `0-9`, and `-`.
1. Select a _Region_ closest to you
1. Select the **Free** _SKU_
1. Select the **Sign-in with GitHub** button and authenticate with GitHub

After you sign in with GitHub, enter the repository information.

:::image type="content" source="media/deploy-blazor/repository-details.png" alt-text="Repository details":::

1. Select your preferred _Organization_
1. Select **my-first-static-blazor-app** from the _Repository_ drop-down
1. Select **main** from the _Branch_ drop-down

> [!NOTE]
> If you don't see any repositories, you may need to authorize Azure Static Web Apps in GitHub. Browse to your GitHub repository and go to **Settings > Applications > Authorized OAuth Apps**, select **Azure Static Web Apps**, and then select **Grant**. For organization repositories, you must be an owner of the organization to grant the permissions.

1. In the _Build Details_ section, add Blazor-specific configuration details.

    1. Select **Blazor** from the _Build Presets_ dropdown, and keep all the default values.

1. Select **Review + create**.

    :::image type="content" source="media/deploy-blazor/review-create.png" alt-text="Review create button":::

    > [!NOTE]
    > You can edit the [workflow file](github-actions-workflow.md) to change these values after you create the app.

1. Select **Create**.

    :::image type="content" source="media/deploy-blazor/create-button.png" alt-text="Create button":::

1. Select **Go to resource**.

    :::image type="content" source="media/deploy-blazor/resource-button.png" alt-text="Go to resource button":::

## View the website

There are two aspects to deploying a static app. The first provisions the underlying Azure resources that make up your app. The second is a GitHub Actions workflow that builds and publishes your application.

Before you can navigate to your new static site, the deployment build must first finish running.

The Static Web Apps overview window displays a series of links that help you interact with your web app.

:::image type="content" source="../articles/static-web-apps/media/deploy-blazor/overview-window.png" alt-text="Overview window":::

1. Clicking on the banner that says, _Click here to check the status of your GitHub Actions runs_ takes you to the GitHub Actions running against your repository. Once you verify the deployment job is complete, then you can navigate to your website via the generated URL.

2. Once GitHub Actions workflow is complete, you can click on the _URL_ link to open the website in new tab.

:::image type="content" source="../articles/static-web-apps/media/deploy-blazor/static-blazor-web-site.png" alt-text="Blazor starter website":::

## Clean up resources

If you're not going to continue to use this application, you can delete the Azure Static Web Apps instance through the following steps:

1. Open the [Azure portal](https://portal.azure.com)
1. Search for **my-first-static-blazor-app** from the top search bar
1. Select on the app name
1. Select on the **Delete** button
1. Select **Yes** to confirm the delete action

## Next steps

> [!div class="nextstepaction"]
> [Define routes](routes.md)
