---
title: Integrate events from Azure Digital Twins setup | Microsoft Docs
description: Learn how to receive notifications from your Azure Digital Twins setup, as well as create detailed analyses using the steps in this tutorial.
services: digital-twins
author: dsk-2015

ms.service: digital-twins
ms.topic: tutorial 
ms.date: 08/30/2018
ms.author: dkshir
---

# Tutorial: Notify from and analyse your Azure Digital Twins setup

Azure Digital Twins service allows you to bring together people, places and things in a coherent spatial system. This is the third tutorial in a series that demonstrate how to use the Digital Twins to manage your facilities for efficient space utilization. Once you have provisioned the spatial graph and user-defined function using the steps in the previous tutorials, and simulate the device events, you can integrate the events with other services to create a custom notification and analysis system.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Create email notifications with Logic App
> * Analyse events using Time Series Insights

If you don’t have an Azure, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## Prerequisites

This tutorial assumes that you have completed the steps to [Provision your Azure Digital Twins setup](tutorial-facilities-setup.md), as well as [Custom monitor your Azure Digital Twins setup](tutorial-facilities-udf.md). Before proceeding, make sure that you have:
- an instance of Digital Twins running, and 
- the [Azure Digital Twins sample application](https://github.com/Azure-Samples/digital-twins-samples-csharp) downloaded or cloned on your work machine.

## Create email notifications with Logic App
[Azure Logic Apps](../logic-apps/logic-apps-overview.md) allow you to create automated tasks like creating an email notification system for events received from other services. This section will show you how to use Logic Apps to create email notifications for telemetry data from your setup. We will use an [Event Grid Topic](../event-grid/overview.md) to route events coming from the sensors in your spatial graph to the Logic App. You will create an endpoint in your Digital Twins instance for the events to be routed to the Event Grid topic. 

1. Sign in to the [Azure portal](https://portal.azure.com).
1. In the left navigation pane, click **Resource groups**, and then select the resource group you created or used for your Digital Twins instance. 
1. Click **Add** button on top, and search and select a new **Logic App** resource. Click **Create**.
1. Enter a **Name** for your Logic App, and then select your **Subscription**, your **Resource group**, and **Location**. Click **Create**.
1. Open your Logic App when it is deployed, and click **Logic App Designer**. 
1. Pick the **When an Event Grid event occurs** trigger. Sign in with your Azure account when prompted. Confirm to **Allow access** to your Event Grid when prompted. Click **Continue**.
1. In the **When a resource event occurs (Preview)** window, 
    a. Select the **Subscription** that you used to create the Event Grid previously,
    b. Select **Microsoft.EventGrid.Topics** as the **Resource Type**,
    c. Select your Event Grid resource from the drop down for the **Resource Name**.
1. Click **New step**.
1. In the **Choose an action** window,
    a. Search *parse json*, and select the **Parse JSON** action.
    b. Click within the textbox in front of the **Content** field, and select **Body** from the **Dynamic content** list.
    c. Click **Use sample to payload to generate schema** and paste the following JSON payload:
        ```JSON
        {
        "id": "32162f00-a8f1-4d37-aee2-9312aabba0fd",
        "subject": "UdfCustom",
        "data": {
          "TopologyObjectId": "20efd3a8-34cb-4d96-a502-e02bffdabb14",
          "ResourceType": "Space",
          "Payload": "\"Air quality is poor.\"",
          "CorrelationId": "32162f00-a8f1-4d37-aee2-9312aabba0fd"
        },
        "eventType": "UdfCustom",
        "eventTime": "0001-01-01T00:00:00Z",
        "dataVersion": "1.0",
        "metadataVersion": "1",
        "topic": "/subscriptions/a382ee71-b48e-4382-b6be-eec7540cf271/resourceGroups/HOL/providers/Microsoft.EventGrid/topics/DigitalTwinEventGrid"
        }
        ```
      Click **Done**. Note that this payload has fictitious values. Logic App uses this sample payload to generate a **Schema**.
    
    ![Logic App Parse JSON for Event Grid](./media/tutorial-facilities-events/logic-app-parse-json.png)

1. Click **New step**.
1. In the **Choose an action** window,
    a. Search and select **Condition** Action. 
    b. Click within the first **Choose a value** textbox, and select **eventType** from the **Dynamic content** list for the **Parse JSON** window.
    c. Click within the second **Choose a value** textbox, and type **UdfCustom**.
1. In the **If true** window,
    a. Click on **Add an action**, search for *email*, and select the email client of your choice. This tutorial uses *Office 365 Outlook*. 
    b. Select **Send an email** from the **Actions** list. Click **Sign in** and use your Azure account credentials. Click **Allow access** when prompted.
    c. In the **Send an email** window, enter your email ID to receive notifications. In the **Subject**, enter *Digital Twins notification for poor air quality in space * and then select **TopologyObject** from the **Dynamic content** list for **Parse JSON**.
    d. In the **Body** of the same window, enter text similar to this: *Poor air quality detected in a room, and temperature needs to be adjusted*. Feel free to elaborate using elements from the **Dynamic content** list as shown below.

    ![Logic App Send an email](./media/tutorial-facilities-events/logic-app-send-email.png)
1. Click **Save**, and then **Run**.
1. If your device simulation is not already running, open a command window and navigate to the Digital Twin sample. Run the following commands:
```cmd/sh
cd device-connectivity
dotnet run
```

In a few moments, observe the state of the Logic App change as it receives events from your simulated device. 

    ![Logic App Send an email](./media/tutorial-facilities-events/logic-app-events.png)

In a few minutes, you should start getting email notifications from this Logic App. To stop these emails, navigate to your **Logic App** in the portal and select the **Overview** pane. Click **Disable**.


## Analyse events using Time Series Insights



## Clean up resources

If you wish to stop exploring Azure Digital Twins beyond this point, feel free to delete resources created in this tutorial:

1. From the left-hand menu in the [Azure portal](http://portal.azure.com), click **All resources**, select and **Delete** your Digital Twins resource group.
2. If you need to, you may proceed to delete the sample applications on your work machine as well. 


## Next steps

Proceed to the next article to learn more about the spatial intelligence graph and object model in Azure Digital Twins. 
> [!div class="nextstepaction"]
> [Next steps button](concepts-objectmodel-spatialgraph.md)

