---
title: Moderate Facebook posts with Azure Content Moderator | Microsoft Docs
description: Use Content Moderator with sample Facebook page
services: cognitive-services
author: sanjeev3
manager: mikemcca

ms.service: cognitive-services
ms.technology: content-moderator
ms.topic: article
ms.date: 09/18/2017
ms.author: sajagtap
---

# Moderate images and text on a Facebook page

In this tutorial, we will learn how to use Content Moderator with a sample Facebook page to either take down or allow publishing of images and text by users browsing the Facebook page.

The tutorial will guide you through these steps:

1. Create a Content Moderator team.
2. Create Azure Functions that listen for HTTP events from Content Moderator and Facebook.
3. Create a Facebook Page and App, and connect it to Content Moderator

After we are done, Facebook will send the content posted by the visitors to Content Moderator. Based on the match thresholds, your Content Moderator workflows will either allow publishing the content or block and create reviews within the review tool for for human moderation.

## 1. Create a Content Moderator team

Refer to the [Quickstart](quick-start.md) page to sign up for Content Moderator and create a team.

## 2. Configure image moderation workflow (threshold)

Refer to the [Workflows](review-tool-user-guide/workflows) page to configure a custom image workflow (threshold). Note the workflow name.

## 3. Configure text moderation workflow (threshold)

Use steps similar to the [Workflows](review-tool-user-guide/workflows) page to configure a custom text threshold and workflow. Note the workflow name.

![Configure Text Workflow](images/text-workflow-configure.PNG)

Test your workflow by using the "Execute Workflow" button.

![Test Text Workflow](images/text-workflow-test.PNG)

## 4. Create Azure Functions

Sign in to the [Azure Management Portal](https://portal.azure.com/) to create your Azure Functions. follow these steps:

1. Create a Azure Function App as shown on the [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-function-app-portal) page.
2. Open the newly created Function App.
3. Within the App, navigate to **Platform features -> Application Settings**
4. Define the following [application settings](https://docs.microsoft.com/en-us/azure/azure-functions/functions-how-to-use-azure-function-app-settings#settings).

    | App Setting | Description   | 
    | -------------------- |-------------|
    | cm:TeamId   | Your Content Moderator TeamId  | 
    | cm:SubscriptionKey | Your Content Moderator subscription key - See [Credentials](/review-tool-user-guide/credentials) | 
    | cm:Region | Your Content Moderator region |
    | cm:ImageWorkflow | Name of the workflow to run on Images |
    | cm:TextWorkflow | Name of the workflow to run on Text |
    | cm:CallbackEndpoint | Url for the CMListener Function App that you create later in this guide |
    | fb:VerificationToken | The secret token, also used to subscribe to the Facebook feed events |
    | fb:PageAccessToken | This is a facebook graph api access token that does not expire and allows the function Hide/Delete posts on your behalf. |

5. Create a new **HttpTrigger-CSharp** function named **FBListener**. This function receives events from Facebook. Follow these steps to create this function.

    1. Keep the [Azure Functions Creation](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-function-app-portal) page open for reference.
    2. Click the **"+"** add to create new function.
    3. Instead of the built-in templates, choose the **"Get started on your own/custom function"** option.
    4. Click on the tile that says **"HttpTrigger-CSharp"**
    5. Enter the name "FBListener". The **Authorization Level** field should be set to **"Function"**.
    6. Click **Create**.
    7. Replace the contents of the **run.csx** with the contents from [**FbListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/FbListener/run.csx).

6. Create a new **HttpTrigger-CSharp** function named **CMListener**. This function receives events from Facebook. Follow these steps to create this function.

    1. Keep the [Azure Functions Creation](https://docs.microsoft.com/en-us/azure/azure-functions/functions-create-function-app-portal) page open for reference.
    2. Click the **"+"** add to create new function.
    3. Instead of the built-in templates, choose the **"Get started on your own/custom function"** option.
    4. Click on the tile that says **"HttpTrigger-CSharp"**
    5. Enter the name "CMListener". The **Authorization Level** field should be set to **"Function"**.
    6. Click **Create**.
    7. Replace the contents of the **run.csx** with the contents from [**CMListener/run.csx**](https://github.com/MicrosoftContentModerator/samples-fbPageModeration/blob/master/CmListener/run.csx).


## 5. Configure the Facebook Page and App

1. Create a Facebook App.

    1. Navigate to the [Facebook developer site](https://developers.facebook.com/)
    2. Click on **My Apps**.
    3. Add a New App.
    4. Select **Webhooks -> Get Started**
    5. Select **Page -> Subscribe to this topic**
    6. Provide the **FBListener Url** as the Callback URL and the **Verify Token** you configured under the **Function App Settings**
    7. Once subscribed, scroll down to feed and select **subscribe**.

2. Create a Facebook Page.

    1. Navigate to [Facebook](https://www.facebook.com/bookmarks/pages) and create a new Facebook Page.
    2. Allow the Facebook App to access this page by following these steps:
        1. Navigate to the **Graph API Explorer**.
        2. Select **Application**.
        3. Select **Page Access Token**, Send a **Get** request.
        4. Click the ID in the response (this is the Page ID).
        5. Now append the **"/subscribed_apps"** to the URL and Send a **Get** (empty response) request.
        6. Submit a **Post** request. You will get the response as **"success": true**.
