---
title: Create a new app with LUIS | Microsoft Docs
description: Create and manage your applications on the Language Understanding (LUIS) webpage. 
services: cognitive-services
author: cahann
manager: hsalama

ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 03/01/2017
ms.author: cahann
---

# Create an app
You can create a new app two different ways. The first method to create the app is to start with an empty app and create intents, utterances and entities. The second method is to import a LUIS app from a JSON file that contains intents, utterances, and entities. 

You can create and manage your applications on **My Apps** page. You can always access this page by clicking **My apps** on the top navigation bar of [LUIS.ai](https://www.luis.ai) web page. 

![List of apps](./media/luis-create-new-app/apps-list.png)

## Create new app

1. On **My Apps** page, click **Create new app**.
2. In the dialog box, name your application "TravelAgent".

    ![Create new app dialog](./media/luis-create-new-app/create-app.png)

3. Choose your application culture (for TravelAgent app, we’ll choose English), and then click **Done**. 

    >[!NOTE]
    >The culture cannot be changed once the application is created. 

## Import new app

1. On **My Apps** page, click **Import new app**.
2. In the **Import new app** dialog, select the JSON file defining the LUIS app.

    ![Import a new app dialog](./media/luis-create-new-app/import-app.png)

## Next steps

Your first task in the app is to add intents. For more info on how to add intents, see [Add intents](Add-intents.md).