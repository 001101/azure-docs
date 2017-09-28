---
title: Publish your LUIS app | Microsoft Docs
description: After you build and test your app by using Language Understanding Intelligent Services (LUIS), publish it as a web service on Azure. 
services: cognitive-services
author: cahann
manager: hsalama

ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 09/26/2017
ms.author: cahann
---


# Publish your app
When you finish building and testing your LUIS app, you can publish it as a web service on Azure and get an HTTP endpoint that can be integrated into any client or backend code. 

> [!TIP]
> You can optionally test your app before publishing it. For instructions, see [Train and test your app](Train-Test.md).

You can either publish your app directly to the **Production Slot** where end users can access and use your model, or you can publish to a **Staging Slot** to use as a development workstation where you can work on your app and go through trials and tests to validate any changes before publishing to the production slot. 

**To publish your app to an HTTP endpoint:**

1. Open your app (e.g. TravelAgent) by clicking its name on **My Apps** page, and then click **Publish App** in the left panel to access the **Publish App** page. The screenshot below shows the Publish page if you haven't published your app yet.

    ![Publish page-](./media/luis-how-to-publish-app/luis-first-publish.png)
 
    If you have previously published this app, this page will look like the following screenshot: 
 
    ![Publish page](./media/luis-how-to-publish-app/luis-republish.png)
2. If you want to use a key other than the Starter key, click **Change** next to the **Assigned endpoint key** label. This opens a dialog that allows you to select an existing endpoint key to assign to the app. For more information on how to create and add endpoint keys to your LUIS app, see [Manage your keys](Manage-Keys.md).
3. Choose whether to publish to **Production** or to **Staging** slot by selecting the corresponding value from the **Endpoint Slot** list. 
4. If you want to enable Bing Spell Check, click the **Enable Bing Spell Checker** check box. 

    >[!NOTE]
    >The option to enable Bing Spell Check is included with your LUIS subscription. However, you need to add `spellCheck=true` to the URL when you call the LUIS app endpoint to turn on spell checking. Checking the **Enable Bing Spell Checker** check box will append `spellCheck=true` to the URL that displays in the **Publish app** page when publish is complete. 


5. If you want the JSON response of your published app to include all intents defined in your app and their prediction scores, click **Add Verbose Flag** checkbox. Otherwise, it will include only the top scoring intent.
6. Click **Train** if the app wasn't trained already.  

7. Click **Publish**. The endpoint URL of your published app is displayed. 

    >[!NOTE]
    >If the **Publish** button is disabled, then either your app does not have an assigned an endpoint key, or you have not trained your app yet.




If you want to test your published endpoint in a browser using the generated URL, you can click the URL to open it in your browser, then set the URL parameter "&q" to your test query (for example: "&q=Book me a flight to Boston on May 4"), and then press Enter. You will get the JSON response of your HTTP endpoint. 

![JSON response of published HTTP endpoint](./Images/PublishApp-JSONresponse.JPG)


## Next steps

To test how your published app works, you can access the test console by clicking **Train & Test** in the left panel. For instructions on how to test your app using the test console, see [Train and test your app](Train-Test.md).
