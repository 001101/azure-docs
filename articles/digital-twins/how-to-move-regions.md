---
# Mandatory fields.
title: Move instance to a different Azure region
titleSuffix: Azure Digital Twins
description: See how to move an Azure Digital Twins instance from one Azure region to another.
author: baanders
ms.author: baanders # Microsoft employees only
ms.date: 08/26/2020
ms.topic: how-to
ms.custom: subject-moving-resources
ms.service: digital-twins
#Customer intent: As an Azure service administrator, I want to move my Azure Digital Twins instance to another region.

# Optional fields. Don't forget to remove # if you need a field.
# ms.reviewer: MSFT-alias-of-reviewer
# manager: MSFT-alias-of-manager-or-PM-counterpart
---

# Move an Azure Digital Twins instance to a different Azure region

If you need to move your Azure Digital Twins instance from one region to another, the current process is to **recreate your resources in the new region**, and then (optionally) delete the original resources. At the end of this process, you will be working with a new Azure Digital Twins instance that is identical to the first, except for the updated location.

This article provides guidance on how to do a complete move, copying over everything you'll need to make the new instance match the original.

This process includes the following steps:
* Prepare: Download your original models, twins, and graph
* Move: Create a new Azure Digital Twins instance, in a new region
* Move: Repopulate the new Azure Digital Twins instance
    - Upload original models, twins, and graph
    - Recreate endpoints and routes
    - Re-link connected resources
* Clean up source resources (optional): Delete original instance

## Prerequisites

Before attempting to recreate your Azure Digital Twins instance, it's a good idea to go over the components of your original instance and get a clear idea of all the pieces that will need to be recreated.

Here are some questions you may want to consider:
* What are the **models** uploaded to my instance? How many are there?
* What are the **twins** in my instance? How many are there?
* What is the general shape of the **graph** in my instance? How many relationships are there?
* What **endpoints** do I have in my instance?
* What **routes** do I have in my instance? Do they have filters?
* Where does my instance **connect to other Azure services**? Some common integration points include...
    - Event Grid, Event Hub, or Service Bus
    - Azure Functions
    - Logic Apps
    - Time Series Insights
    - Azure Maps
    - Device Provisioning Service (DPS)
* What other **personal or company apps** do I have that connect to my instance?

You can gather this information using the [Azure portal](https://portal.azure.com), [Azure Digital Twins APIs and SDKs](how-to-use-apis-sdks.md), [Azure Digital Twins CLI commands](how-to-use-cli.md), or the [Azure Digital Twins (ADT) Explorer](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) sample.

## Prepare

In this section, you will prepare to recreate your instance by **downloading your original models, twins, and graph** from your original instance. You will do this using the [Azure Digital Twins (ADT) Explorer](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) sample.

>[!NOTE]
>You may already have files containing the models and/or the graph in your instance. If so, you do not need to download everything again—just the pieces you are missing or things that may have changed since you originally uploaded these files (such as twins that may have been updated with new data).

### Set up ADT Explorer application

First, download the sample application code and set it up to run on your machine. 

Navigate to the sample here: [Azure Digital Twins (ADT) explorer](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/). Hit the *Download ZIP* button to download a *.ZIP* file of this sample code to your machine as _**ADT_Explorer.zip**_. Unzip the file.

Next, set up permissions for ADT Explorer to run on your machine. To do this, follow the steps in the [*Set ADT Explorer permissions*](quickstart-adt-explorer.md#set-adt-explorer-permissions) section of the Azure Digital Twins quickstart.

Finally, run and configure ADT Explorer to connect to your original Azure Digital Twins instance. Follow the steps in the [*Run and configure ADT Explorer*](quickstart-adt-explorer.md#run-and-configure-adt-explorer) section of the quickstart.

Now you should have the ADT Explorer sample app running in a browser on your machine. The sample should be connected to your original Azure Digital Twins instance.

:::image type="content" source="media/how-to-move-regions/explorer-blank.png" alt-text="Browser window showing an app running at localhost:3000. The app is called ADT Explorer and contains boxes for a Query Explorer, Model View, Graph View, and Property Explorer. There is no onscreen data yet." lightbox="media/how-to-move-regions/explorer-blank.png":::

To verify the connection, you can hit the *Run query* button to run the default query that displays all twins and relationships in the graph in the *GRAPH EXPLORER* box.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="A button reading 'Run Query' near the top of the window is highlighted" lightbox="media/how-to-move-regions/run-query.png":::

You can leave ADT Explorer running, as you will use it again later in this article to re-upload these items to your new instance in the target region.

### Download models, twins, and graph

Next, download the various components of your solution to your machine.

<!-- Model download possibly not necessary if included with graph download -->
To download your **models**, use the *Download models* icon in the *MODEL VIEW* box.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/download-models.png" alt-text="In the Model View box, the first icon is highlighted. It shows an arrow pointing down out of a cloud." lightbox="media/how-to-move-regions/download-models.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

<!-- This will... download JSONs of the models to your machine? Can't verify, icon isn't doing anything -->

To download your **twins and graph**, make sure the full graph is showing in the *GRAPH VIEW* box (you can do this by rerunning the default query of `SELECT * FROM digitaltwins` in the *QUERY EXPLORER* box).
 
Then, hit the *Export graph* icon in the *GRAPH VIEW* box.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/export-graph.png" alt-text="In the Graph View box, an icon is highlighted. It shows an arrow pointing down out of a cloud." lightbox="media/how-to-move-regions/export-graph.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

This will enable a *Download* link in the *GRAPH VIEW*. Select it to download a JSON-based representation of the query result, including your models, twins, and relationships.

<!-- Does it export models? Need to verify. -->

## Move

Next, you will complete the "move" of your instance by creating a new instance in the target region, and populating it with the data and components from your original instance.

### Create a new instance

First, **create a new instance of Azure Digital Twins in your target region**. To do this, follow the steps in [*How-to: Set up an instance and authentication*](how-to-set-up-instance-scripted.md), keeping these pointers in mind:
* You can keep the same name for the new instance **if** it is in a different resource group. If you need to use the same resource group that contains your original instance, then your new instance will need its own distinct name.
* Enter the target new region when prompted for a location.
* You do **not need** to recreate the app registration. Your new instance can reuse the same app registration you already have.
    - If using the [scripted](how-to-set-up-instance-scripted.md) setup article, you can reenter the details of your existing app registration instead of entering a new name when prompted.
    - If using the manual [portal](how-to-set-up-instance-portal.md) or [CLI](how-to-set-up-instance-cli.md) setup articles, you can stop after the *Create the Azure Digital Twins instance* and *Set up user access permissions* steps. No need to continue through *Set up access permissions for client applications*.

Once this is complete, you will need the **hostname** of your new instance to continue setting it up with your data. If you didn't make a note of this during setup, you can follow [these instructions](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values) to get it now from the Azure portal.

<!-- You can keep the same app registration, right? -->

### Repopulate old instance

Next, you will set up the new instance so that it is a copy of the original.

#### Upload original models and graph using ADT Explorer

In this section, you can re-upload your models, twins, and graph to the new instance. If you don't have any models, twins, or graphs in your original instance or you don't want to move them to the new instance, you can skip to the [next section](#recreate-endpoints-and-routes).

Otherwise, to proceed, return to the browser window running **ADT Explorer** and follow the steps below.

##### Connect to the new instance

Currently, ADT Explorer is connected to your original Azure Digital Twins instance. Switch the connection to point to your new instance  by hitting the *Sign in* button at the top of the window. 

:::image type="content" source="media/how-to-move-regions/sign-in.png" alt-text="ADT Explorer highlighting the Sign In icon near the top of the window. The icon shows a simple silhouette of a person overlaid with a silhouette of a key." lightbox="media/how-to-move-regions/sign-in.png":::

Since you're reusing the app registration, you only need to replace the *ADT URL*. Change this value to that it reads *https://<new instance hostname>*.

Hit *Connect*. You may be asked to log in again with your Azure credentials, and/or grant this application consent for your instance.

##### Upload models and graphs

Next, upload the solution components that you downloaded earlier to your new instance.

To upload your **models**, hit the *Upload a Model* icon in the *MODEL VIEW* box.

:::image type="content" source="media/how-to-move-regions/upload-model.png" alt-text="In the Model View box, the middle icon is highlighted. It shows an arrow pointing into a cloud." lightbox="media/how-to-move-regions/upload-model.png":::
 
1. In the file selector box that appears, navigate to your downloaded model files.
2. Select all the model files you downloaded earlier from your original instance, and hit OK.
3. Follow the popup dialog asking you to sign into your Azure account.

<!-- May need to edit depending on what format the model files download in -->

ADT Explorer will now upload these model files to your Azure Digital Twins instance. They should show up back in the *MODEL VIEW* box once they've been added.

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/model-info.png" alt-text="A view of the 'Model View' box with two model definitions listed inside, Floor (dtmi:example:Floor;1) and Room (dtmi:example:Room;1)." lightbox="media/how-to-move-regions/model-info.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

Next, upload your **twins and graph**. In the *GRAPH VIEW* box, hit the *Import Graph* icon.

:::image type="content" source="media/how-to-move-regions/import-graph.png" alt-text="In the Graph View box, an icon is highlighted. It shows an arrow pointing into a cloud." lightbox="media/how-to-move-regions/import-graph.png":::

In the file selector box, navigate to your downloaded graph. Select the graph file and hit OK.

<!-- May need to edit depending on what format the graph downloads in -->

After a few seconds, ADT Explorer will open an *Import* view displaying a preview of the graph that is going to be loaded.

To confirm the graph upload, hit the *Save* icon in the upper right corner of the *GRAPH VIEW*:

:::row:::
    :::column:::
        :::image type="content" source="media/how-to-move-regions/graph-preview-save.png" alt-text="Highlighting the Save icon in the Graph Preview pane" lightbox="media/how-to-move-regions/graph-preview-save.png":::
    :::column-end:::
    :::column:::
    :::column-end:::
:::row-end:::

ADT Explorer will now upload the graph, including the twins and relationships, to your new Azure Digital Twins instance.

To verify the graph, hit the *Run Query* button in the *GRAPH EXPLORER* box to run the default query that displays all twins and relationships in the graph.

:::image type="content" source="media/how-to-move-regions/run-query.png" alt-text="Highlight around the same 'Run Query' button from earlier, near the top of the window" lightbox="media/how-to-move-regions/run-query.png":::

You should see your graph displayed in the *GRAPH EXPLORER* box. This confirms that your graph has been re-uploaded to the new instance in the target region.

#### Recreate endpoints and routes

If you have **endpoints and/or routes** in your original instance, you'll need to recreate them in your new instance. If you don't have any endpoints or routes in your original instance or you don't want to move them to the new instance, you can skip to the [next section](#re-link-connected-resources).

Otherwise, proceed, follow the steps in [*How-to: Manage endpoints and routes*](how-to-manage-routes-portal.md) using the new instance, keeping these pointers in mind: 
* You do **not need** to recreate the Event Grid, Event Hub, or Service Bus resource that you're using for the endpoint (*Prerequisite* sections in the endpoint instructions). You just need to re-create the endpoint on the Azure Digital Twins instance.
* You can reuse endpoint and route **names**, since they are scoped to a different instance.
* Remember to add any required **filters** to the routes you create.

<!-- Double check the name reuse -->

#### Re-link connected resources

If you have other apps or Azure resources that are connected to your original Azure Digital Twins instance, you'll need to edit the connection so that they reach your new instance instead. This may include other Azure services, or personal or company apps that you've set up to work with Azure Digital Twins.

If you don't have any other resources connected to your original instance or you don't want to move them to the new instance, you can skip to the [next section](#verify).

Otherwise, to proceed, consider the connected resources in your scenario. You do not need to delete and re-create any connected resources; instead, you just need to edit the points where they connect to an Azure Digital Twins instance through its **hostname**, and update this to use the hostname of the new instance instead of the original.

<!-- Anything else changing? -->

The exact resources you need to edit depends on your scenario, but here are some common integration points:
* Azure Functions. If you have an Azure function whose code includes the hostname of the original instance, you should update this value to the new instance's hostname and re-publish the function.
* Event Grid, Event Hubs, or Service Bus
* Logic Apps
* Time Series Insights
* Azure Maps
* Device Provisioning Service (DPS)
* Personal or company apps outside of Azure, such as the **client app** created in [*Tutorial: Code a client app*](tutorial-code.md), that connect to the instance and call Azure Digital Twins APIs

<!-- Is it correct that you should re-publish the Azure function? -->

After completing this step, your new instance in the target region should be a copy of the original instance.

## Verify

To verify that your new instance was set up correctly, you can use the following tools:
* The [**Azure portal**](https://portal.azure.com) (good for verifying that your new instance exists and is in the correct target region; also good for verifying endpoints and routes, and connections to other Azure services)
* The [Azure Digital Twins **CLI commands**](how-to-use-cli.md) (good for verifying that your new instance exists and is in the correct target region; also can be used to verify instance data)
* [**ADT Explorer**](https://docs.microsoft.com/samples/azure-samples/digital-twins-explorer/digital-twins-explorer/) (good for verifying instance data like models, twins, and graph)
* The [Azure Digital Twins APIs and SDKs](how-to-use-apis-sdks.md) (good for verifying instance data like models, twins, and graph; also good for verifying endpoints and routes)

You can also try running any custom apps or end-to-end flows that you had running with your original instance, to help you verify that they're working with the new instance correctly.

## Clean up source resources (optional)

Now that your new instance is set up in the target region with a copy of the original instance's data and connections, you can **delete the original instance** if you would like.

You can do this in the [Azure portal](https://portal.azure.com), with the [CLI](how-to-use-cli.md), or with the [control plane APIs](how-to-use-apis-sdks.md#overview-control-plane-apis).

To delete the instance using the Azure portal, [open the portal](https://portal.azure.com) in a browser window and navigate to your original Azure Digital Twins instance by searching for its name in the portal search bar.

Hit the *Delete* button, and follow the prompts to finish the deletion.

:::image type="content" source="media/how-to-move-regions/delete-instance.png" alt-text="View of the Azure Digital Twins instance details in the Azure portal, on the Overview tab. The Delete button is highlighted":::