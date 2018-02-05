---
title: How to configure retention in your Azure Time Series Insights environment | Microsoft Docs
description: This article describes how to configure retention in your Azure Time Series Insights environment. 
services: time-series-insights
ms.service: time-series-insights
author: anshan
ms.author: anshan
manager: kfile
editor: MicrosoftDocs/tsidocs
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 02/05/2018
---

# Configuring retention in Time Series Insights
This article describes two settings that impact data retention in Time Series Insights (TSI).

Each TSI environment has a setting that controls **Data retention time**. The values span between 1 and 400 days. The data is deleted based on the environment storage capacity or retention duration (1-400), whichever comes first.

Each TSI environment has an additional setting **Data retention mode**. This setting controls ingress and purge behavior when the max capacity of an environment is reached. There are two modes to choose from:
- **Continue ingress and purge old data** (default)  
- **Pause ingress**

> [!NOTE]
> By default, when creating a new environment, the retention is configured to **Continue ingress and purge old data**. This setting can be toggled as needed after creation time using the Azure portal, on the **Configure** page of the TSI environment.

For information on switching retention modes, review [Configuring retention in Time Series Insights](time-series-insights-how-to-configure-retention.md).

Compare the retention modes:

## **Continue ingress and purge old data**
- This mode is the default mode for TSI environments and exhibits the same behavior TSI environments did in public preview.
- This mode is preferred when users want to always see their *most recent data* in their TSI environment. 
- Ensure the end users are comfortable with data loss once the limits are reached.
- This mode *purges* data once the environment’s limits (retention time, size, or count, whichever comes first) are reached. Retention is set to 30 days by default. 
- The oldest ingested data is purged first (FIFO approach).

### Example 1:
Consider an example environment with retention mode **Continue ingress and purge old data**:
In this example, **Data retention time** is set to 400 days. **Capacity** is set to S1 unit, which contains 30 GB of total capacity.   Let's assume inbound data accumulates to 500 MB each day on average. This environment can only retain 60 days worth of data given the rate of inbound data, since the maximum capacity is reached at 60 days. The inbound data accumulates as: 500 MB each day x 60 days = 30 GB. 

In this example, on the 61st day, the environment shows the freshest data, but purges the oldest data, older than 60 days. The purge makes room for the new data streaming in, so that new data may continue to be explored. 

If the user wishes to retain data longer, they can increase the size of the environment by adding additional units or can push less data.  

### Example 2:
Consider an environment also configured retention mode**Continue ingress and purge old data**. In this example, **Data retention time** is set to a lower value of 180 days. **Capacity** is set to S1 unit, which contains 30 GB of total capacity. In order to store data for the full 180 days, the daily ingress cannot exceed 0.166 GB (166 MB) per day.  

Whenever this environment’s daily ingress rate exceeds of 0.166 GB per day, data cannot be stored for 180 days, since some data gets purged. Consider this same environment during a busy time frame. Assume the environment’s ingress rate may increase to an average 0.189 GB per day. In that busy time frame, about 158 days of data are retained (30GB/0.189 = 158.73 days of retention). This is less than the desired data retention time frame.

## Pause ingress
- This mode is designed to ensure data is not purged if the size and count limits are reached prior to their retention period.  
- This mode provides additional time for the users to increase the capacity of their environment before data is purged due to breaching of retention period
- This mode helps protect from data loss. 
- However, once an environment’s maximum capacity is reached, the environment pauses data ingress until additional actions occur: 
    a. You increase the environment’s maximum capacity. For more information, [How to scale your Time Series Insights environment](time-series-insights-how-to-scale-your-environment.md) to add more scale units.
    b. The data retention period is reached and data is purged, thus bringing the environment below its maximum capacity.

### Example 3:
Consider an environment with retention mode configured to **pause ingress**. In this example, the **Data retention period** is configured to 60 days. **Capacity** is set to 3 units of S1. Assume this environment has ingress of 2-GB data each day. In this environment, ingress is paused once the maximum capacity is reached. At that time, the environment shows the same dataset until ingress resumes or until ‘continue ingress’ is enabled (which would purge older data to make room for new data). 

When ingress resumes:
- Data flows in the order it was received by event source
- The events are indexed based on their timestamp, unless you have exceeded retention policies on your event source. For more on event source retention configuration, read here. 

> [!IMPORTANT]
> You should set alerts to provide notice to help avoid ingress being paused. Data loss is possible since the default retention is 24 hours for Azure event sources. Therefore, once ingress is paused, you likely lose the most recent data unless additional action is taken. You must increase capacity, or switch modes to **Continue ingress and purge mode** to avoid the data loss potential.

If no properties are configured on event source (timeStampPropertyName), TSI defaults to the timestamp of arrival at event hub as the x-axis. If timeStampPropertyName is configured to be something different, the environment looks for the configured timeStampPropertyName in the data packet when events are parsed. 

If you need to scale your environment up to accommodate additional capacity or to increase the length of retention, [How to scale your Time Series Insights environment](time-series-insights-how-to-scale-your-environment.md) for more information.  

## Next steps
For information on switching retention modes, review [Configuring retention in Time Series Insights](time-series-insights-how-to-configure-retention.md).