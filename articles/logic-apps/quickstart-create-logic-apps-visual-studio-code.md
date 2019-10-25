---
title: Automate workflows with Visual Studio Code - Azure Logic Apps
description: Create or edit logic app underlying JSON definitions with Visual Studio Code (VS Code)
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.manager: carmonm
ms.reviewer: klam, deli, LADocs
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/05/2018
---

# Quickstart: Create and manage logic app workflow definitions with Visual Studio Code

With [Azure Logic Apps](../logic-apps/logic-apps-overview.md) and Visual Studio Code, you can create and manage logic apps that help you automate tasks, workflows, and processes for integrating apps, data, systems, and services across organizations and enterprises. This quickstart shows how you can create and edit the underlying workflow definitions, which use JavaScript Object Notation (JSON), for logic apps through a code-based experience. You can also work on existing logic apps that are already deployed to Azure.

Although you can perform these same tasks in the [Azure portal](https://portal.azure.com) and in Visual Studio, you can get started faster in Visual Studio Code when you're already familiar with logic app definitions and want to work directly in code. For example, you can disable, enable, delete, and refresh already created logic apps. Also, you can work on logic apps and integration accounts from any development platform where Visual Studio Code runs, such as Linux, Windows, and Mac.

For this article, you can create the same logic app from this [quickstart](../logic-apps/quickstart-create-first-logic-app-workflow.md), which focuses more on the basic concepts. In Visual Studio Code, the logic app looks like this example:

![Example logic app workflow definition](./media/quickstart-create-logic-apps-visual-studio-code/visual-studio-code-overview.png)

Before you start, make sure you have these items:

* If you don't have an Azure account and subscription, [sign up for a free Azure account](https://azure.microsoft.com/free/).

* Basic knowledge about [logic app workflow definitions](../logic-apps/logic-apps-workflow-definition-language.md) and their structure as described with JSON

  If you're new to Logic Apps, try this [quickstart](../logic-apps/quickstart-create-first-logic-app-workflow.md), which creates your first logic apps in the Azure portal and focuses more on the basic concepts.

* Access to the web for signing in to Azure and your Azure subscription

* Download and install these tools, if you don't have them already:

  * [Visual Studio Code version 1.25.1 or later](https://code.visualstudio.com/), which is free

  * Visual Studio Code extension for Azure Logic Apps

    You can download and install this extension from the [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) or directly from inside Visual Studio Code. Make sure that you reload Visual Studio Code after installing.

    ![Find "Visual Studio Code extension for Azure Logic Apps"](./media/quickstart-create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    To check that the extension installed correctly, select the Azure icon that appears in your Visual Studio Code toolbar.

    ![Confirm extension correctly installed](./media/quickstart-create-logic-apps-visual-studio-code/confirm-installed-visual-studio-code-extension.png)

    For more information, see [Extension Marketplace](https://code.visualstudio.com/docs/editor/extension-gallery). To contribute to this extension's open-source version, visit the [Azure Logic Apps extension for Visual Studio Code on GitHub](https://github.com/Microsoft/vscode-azurelogicapps).

<a name="sign-in-azure"></a>

## Sign in to Azure

1. Open Visual Studio Code. On the Visual Studio Code toolbar, select the Azure icon.

   ![Select Azure icon on Visual Studio Code toolbar](./media/quickstart-create-logic-apps-visual-studio-code/open-extensions-visual-studio-code.png)

1. In the Azure window, under **Logic Apps**, select **Sign in to Azure**. When the Microsoft sign-in page prompts you, sign in with your Azure account.

   ![Select "Sign in to Azure"](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-azure-visual-studio-code.png)

   1. If sign in takes longer than usual, Visual Studio Code prompts you to sign in through a Microsoft authentication website by providing you a device code. To sign in with the code instead, select **Use Device Code**.

      ![Continue with device code instead](./media/quickstart-create-logic-apps-visual-studio-code/use-device-code-prompt.png)

   1. To copy the code, select **Copy & Open**.

      ![Copy code for Azure sign in](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-prompt-authentication.png)

   1. To open a new browser window and continue to the authentication website, select **Open Link**.

      ![Confirm opening a browser and going to authentication website](./media/quickstart-create-logic-apps-visual-studio-code/confirm-open-link.png)

   1. On the **Sign in to your account** page, enter your authentication code, and select **Next**.

      ![Enter authentication code for Azure sign in](./media/quickstart-create-logic-apps-visual-studio-code/authentication-code-azure-sign-in.png)

1. Select your Azure account. After you sign in, you can close your browser, and return to Visual Studio Code.

   In the Azure pane, the **Logic Apps** and **Integration Accounts** sections now show the Azure subscriptions that are associated with your account. However, if you don't see the subscriptions that you expect, or if the sections show too many subscriptions, follow these steps:
   
   1. Move your pointer over the **Logic Apps** label. When the toolbar appears, select **Select Subscriptions** (filter icon).

      ![Find or filter Azure subscriptions](./media/quickstart-create-logic-apps-visual-studio-code/find-or-filter-subscriptions.png)

   1. From the list that appears, select the subscriptions that you want to appear.

1. Under **Logic Apps**, select the subscription that you want. The subscription node expands and shows any logic apps that exist in that subscription.

   ![Select your Azure subscription](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-subscription.png)

   > [!TIP]
   > Selecting your subscription under **Integration Accounts** shows any integration accounts that exist in that.

<a name="create-logic-app"></a>

## Create your logic app

1. If you haven't signed in to your Azure subscription already from inside Visual Studio Code, follow the [previous steps to sign in now](#sign-in-azure).

1. Open your subscription's context menu, and select **Create Logic App**.

   ![From subscription menu, select "Create Logic App"](./media/quickstart-create-logic-apps-visual-studio-code/create-logic-app-visual-studio-code.png)

   A list appears that shows any Azure resource groups in your subscription.

1. From resource group list, select either **Create a new resource group** or an existing resource group. For this example, create a new resource group.

   ![Create a new Azure resource group](./media/quickstart-create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Provide a name for your Azure resource group, and then press ENTER.

   ![Provide name for your Azure resource group](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Select the datacenter location for where to save your logic app's metadata.

   ![Select Azure location for saving logic app metadata](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-location-new-resources.png)

1. Provide a name for your logic app, and then press Enter.

   ![Provide name for your logic app](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   In the Azure window, under your Azure subscription, your new and empty logic app appears. Visual Studio Code also opens a JSON (.logicapp.json) file, which includes a skeleton workflow definition for your logic app. Now you can start manually authoring your logic app's workflow definition in this JSON file. For technical reference about the structure and syntax for a workflow definition, see the [Workflow Definition Language schema for Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).

   ![Empty logic app workflow definition JSON file](./media/quickstart-create-logic-apps-visual-studio-code/empty-logic-app-workflow-definition.png)

   For example, here is a sample logic app workflow definition. Usually, JSON elements appear alphabetically in each section. However, this sample shows these elements roughly in the order that the logic app's steps appear in the designer.

   > [!IMPORTANT]
   > If you use this sample logic app definition, make sure that you replace the fictitious email address with your own email address.
   > Also, the sample uses the Office 365 Outlook connector. If you have a different email provider, such as Outlook.com or Gmail, 
   > replace the `Send_an_email_action` action with an email connector that's supported by Azure Logic Apps.

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 3
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "Title: @{triggerBody()?['title']}\n\nDate published: @{triggerBody()?['publishDate']}\n\nLink: @{triggerBody()?['primaryLink']}",
                  "Subject": "New RSS item: @{triggerBody()?['title']}",
                  "To": "sophia-owen@fabrikam.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['outlook_1']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            }
         }
      },
      "outputs": {}
   }
   ```

1. When you're done, save your logic app's workflow definition.

1. When you're prompted to upload your logic app to your Azure subscription, select **Upload**.

   This step publishes your logic app to the [Azure portal](https://portal.azure.com), which and makes your logic live and running in Azure.

   ![Upload new logic app to your Azure subscription](./media/quickstart-create-logic-apps-visual-studio-code/upload-new-logic-app.png)


## View your logic app in the Azure portal

1. To find and check your logic app in Azure, sign in to the Azure portal with the same Azure account that you used for creating your logic app.

   ![Your new logic app in Azure portal](./media/quickstart-create-logic-apps-visual-studio-code/published-logic-app-in-azure.png)

<a name="edit-logic-app"></a>


1. To edit your logic app at any time, open your logic app's shortcut menu, and select **Open in Editor**.

   ![New logic app workflow definition](./media/quickstart-create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)



## Edit logic app

To work on a logic app that's published in Azure, you can open that logic app's definition by using Visual Studio Code.

1. If you haven't signed in to your Azure subscription from inside Visual Studio Code, follow the steps in this article to [sign in now](#sign-in-azure).

1. In the Azure window, under **Logic Apps**, expand your Azure subscription, and select the logic app you want.

1. From your logic app's menu, select **Open in Editor**. Or, next to your logic app's name, select the edit icon.

   ![Open editor for existing logic app](./media/quickstart-create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code opens the .logicapp.json file for your logic app's workflow definition.

   ![Opened logic app workflow definition](./media/quickstart-create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. Make your changes in your logic app's definition.

1. When you're done, save your changes.

1. When Visual Studio Code prompts you to update your logic app definition in your Azure subscription, select **Upload**.

   ![Upload your edits to logic app definition](./media/quickstart-create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## Next steps

> [!div class="nextstepaction"]
> * [Create logic apps with Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md)