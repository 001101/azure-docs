﻿---
title: How to send events to an Azure Time Series Insights environment | Microsoft Docs
description: This tutorial explains how to create and configure event hub and run a sample application to push events to be shown in Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/26/2018
---

# Send events to a Time Series Insights environment using event hub

This article explains how to create and configure event hub and run a sample application to push events. If you have an existing event hub with events in JSON format, skip this tutorial and view your environment in Time Series Insights.(Link needed)

## Configure an event hub

1. To create an event hub, follow instructions from the Event Hub documentation.
1. Search for event hub in the search bar. Click **Event Hubs** in the returned list.
1. Select your event hub by clicking on its name.
1. Under **Entities** in the middle configuration window, click **Event Hubs** again.
1. Select the name of the event hub to configure it.

    ![consumer-group][1]

1. Under **Entities**, select **Consumer groups**.
1. Make sure you create a consumer group that is used exclusively by your Time Series Insights event source.

> [!IMPORTANT]
> Make sure this consumer group is not used by any other service (such as Stream Analytics job or another Time Series Insights environment). If the consumer group is used by other services, read operation is negatively affected for this environment and the other services. If you are using `$Default` as the consumer group, it could lead to potential reuse by other readers.

1. Under the **Settings** heading, select **Share access policies**.
1. On the event hub, create **MySendPolicy** that is used to send events in the C# sample.

    ![shared=access-one][2]

    ![shared-access-two][3]

## Add Time Series Insights Instances

Time Series Insights Update uses Instances to add contextual data to incoming telemetry data. The data is joined at query time using a TimeSeries Id. The Time Series Id for the sample windmills is “Id”. To learn more about Time Series Instances and Time Series ID, see <Insert Link for model>

### Create Time Series Insights event source

1. If you haven't created an event source, follow these instructions to create an event source.
1. Specify **TimeSeriesId** – refer to [Time Series Models](./time-series-insights-update-tsm.md) to learn more about **TimeSeriesId**.

### Push events (sample windmills)

1. Search for event hub in the search bar. Click **Event Hubs** in the returned list.
1. Select your event hub by clicking on its name.
1. Go to **Shared Access Policies** and then **RootManageSharedAccessKey**. Copy the **Connection sting-primary key**

   ![connection-string][4]

1. Go to https://tsiclientsample.azurewebsites.net/windFarmGen.html. This runs simulated windmill devices.
1. Paste the connection string copied from step three in the **Event Hub Connection String**
1. Click on **Click to Start**
1. Go back to your event hub. You should see the new events being received by the hub:

   ![telemetry][5]

## Next steps

> [!div class="nextstepaction"]
> [View your environment in Time Series Insights explorer](https://insights.timeseries.azure.com).

<!-- Images -->
[1]: media/send-events/consumer-group.png
[2]: media/send-events/shared-access-policy.png
[3]: media/send-events/shared-access-policy-2.png
[4]: media/send-events/sample-code-connection-string.png
[5]: media/send-events/telemetry.png