---
title: Tutorial - Share Azure Spatial Anchors across sessions and devices with an Azure Cosmos DB back end | Microsoft Docs
description: In this tutorial, you learn how to share Azure Spatial Anchors identifiers across Android/iOS devices in Unity with a back-end service and Azure Cosmos DB.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors

ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
# ms.reviewer: MSFT-alias-of-reviewer
#Customer intent: As a mixed reality developer, I want to learn how to share Azure Spatial Anchors identifiers among devices in Unity with a back-end service and Azure Cosmos DB.
---
# Tutorial: Sharing Azure Spatial Anchors across sessions and devices with an Azure Cosmos DB back end

This tutorial is a continuation of [sharing Azure Spatial Anchors across sessions and devices.](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md) You need to add a few more things to make Azure Cosmos DB serve as the back end during sharing azure spatial anchors across sessions and devices.

Add an Azure Cosmos Database to the resource group you created in earlier. 

### Create a database account

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

Copy the `Connection String` because you'll need it.

### Make minor changes to the SharingService files

In **Solution Explorer**, open `SharingService\Startup.cs`.

Locate `#define INMEMORY_DEMO` at the top of the file and comment that line out. Save the file.

In **Solution Explorer**, open `SharingService\appsettings.json`.

Locate the `StorageConnectionString` property, and set the value to be the same as the `Connection String` value that you copied in the [create a database account step](#create-a-database-account). Save the file.

You can publish the Sharing Service again and finally deploy the app.

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## Next steps

In this tutorial, you've used Azure Cosmos DB to share anchor identifiers across devices. To learn more about how to use Azure Spatial Anchors in a new Unity HoloLens app, continue to the next tutorial.

> [!div class="nextstepaction"]
> [Starting a new Android app](./tutorial-new-unity-hololens-app.md)
