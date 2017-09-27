---
title: 'Azure Stream Analytics: Understand and adjust Streaming Units | Microsoft Docs'
description: Understand what factos impact performance in Azure Stream Analytics.
keywords: streaming unit, query performance
services: stream-analytics
documentationcenter: ''
author: jeanb
manager: jhubbard
editor: cgronlun

ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: jeanb

---

# Understand and adjust Streaming Units

In order to achieve low latency streaming processing, Azure Stream Analytics jobs perform all processing in memory. When running out of memory, the streaming job fails. As a result, for a production job, it’s important to monitor a streaming job’s resource usage, and make sure there is enough resource allocated, in order to keep the jobs running 24/7. 
One important metric you can monitor the resource usage with is the SU % utilization metric. Streaming Units (SUs) represent the computing resources that are consumed to execute a job. SUs provide a way to describe the relative event processing capacity based on a blended measure of CPU, memory, and read and write rates. This capacity let you focus on the query logic and removes you from needing to know storage tier performance considerations, allocate memory for your job manually, and approximate the CPU core-count needed to run your job in a timely manner. 
The metric is a percentage number ranging from 0% to 100%. For a streaming job with minimal footprint, the SU % Utilization metric is usually under 10%. It’s best to keep the metric below 80% to account for occasional spikes.  You can set an alert on the metric. 
See https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/insights-alerts-portal 


## Configure Stream Analytics Streaming Units (SUs)
1. Sign in to [Azure portal](http://portal.azure.com/)
2. In the list of resources, find the Stream Analytics job that you want to scale and then open it. 
3. In the job blade, under Configure, click **Scale**. 
4. Use the slider to set the SUs for the job. Notice that you are limited to specific SU settings. 

## How many SUs are required for a job?

Choosing the number of required SUs for a particular job depends on the partition configuration for the inputs and the query that's defined within the job. The **Scale** blade allows you to set the right number of SUs. It is a best practice to allocate more SUs than needed. The Stream Analytics processing engine optimizes for latency and throughput at the cost of allocating additional memory.

In general, the best practice is to start with 6 SUs for queries that don't use *PARTITION BY*. Then determine the sweet spot by using a trial and error method in which you modify the number of SUs after you pass representative amounts of data and examine the SU %Utilization metric.

Smaller queries can use 3 SUs. 
Using 1 SU is not recommended for production job. We usually advice to only use 1-SU-jobs for prototyping and testing jobs.

> [NOTE]
> When the number of readers changes during job upgrades, transient warnings are written to audit logs. Stream Analytics jobs automatically recover from these transient issues.

## Factors increasing SU% utilization 
### Stateful query logic 

One of the unique capability of Azure Stream Analytics job is to perform stateful processing, such as windowed aggregates, temporal joins, and temporal analytic functions. Each of these operators keep some states. 
#### Windowed aggregates
The state size of a windowed aggregate is proportional to the number of groups (cardinality) in the group by operator. 
For example, in <code>SELECT count(*) from input group by clusterid, tumblingwindow (minutes, 5)</code> query, the number associated with clusterid is the cardinality of the query. 
In order to ameliorate issues caused by high cardinality in the previous query, you can send events to Event Hub partitioned by ''clusterid'', and scale out the query by allowing the system to process each input partition separately using ''Partition By'' as shown in the example below:
<code>SELECT count(*) from input PARTITION BY PartitionId GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)</code>
Once the query is partitioned out, it is spread out over multiple nodes. As a result, the number of clusterid coming into each node is reduced thereby reducing the cardinality of the group by operator. 
Event Hub partitions should be partitioned by the grouping key to avoid the need for a reduce step. Additional details are covered here. https://docs.microsoft.com/en-us/azure/event-hubs/event-hubs-overview. 
#### Temporal join
The state size of a temporal join is proportional to the number of events in the temporal wiggle room of the join, which is event input rate multiply by the wiggle room size. 
The number of unmatched events in the join affect the memory utilization for the query. The following query is looking to find the ad impressions that generate clicks:
<code>SELECT id from clicks INNER JOIN, impressions on impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.</code>
It is possible that lots of ads are shown and few people click on it and it is required to keep all the events in the time window. Memory consumed is proportional to the window size and event rate. 
To remediate this, send events to Event Hub partitioned by the join keys (id in this case), and scale out the query by allowing the system to process each input partition separately using the Partition By as shown:
<code>
SELECT id from clicks PARTITION BY PartitionId INNER JOIN impressions PARTITION BY PartitionId on impression.PartitionId = clocks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
</code>Once the query is partitioned out, it is spread out over multiple nodes. As a results the number of events coming into each node is reduced thereby reducing the size of the state kept in the join window. 
#### Temportal analytic dunction
The state size of a temporal analytic function is proportional to the event rate multiply by the duration. 
The remediation is similar to temporal join. You can scale out the query using PARTITION BY. 

### Out of order buffer 
User can configure the out of order buffer size in the Event Ordering configuration pane. The buffer is used to hold inputs for the duration of the window, and reorder them. The size of the buffer is proportional to the event input rate multiply by the out of order window size. The default window size is 0. 
To remediate this, scale out query using PARTITION BY. Once the query is partitioned out, it is spread out over multiple nodes. As a results the number of events coming into each node is reduced thereby reducing the number of events in each reorder buffer. 

### Input partition count 
Each input partition of a job input has a buffer. The larger number of input partitions, the more resource the job consumes. For each SU, Azure Stream Analytics can process roughly 1MB/s of input, so you may want to match ASA SU number with the number of partition of your Event Hub. Typically, 1SU job is sufficient for an Event Hub with 2 partitions (which is the minimum for Event Hub) If the Event Hub has more partitions, your ASA job consumes more resources, but not necessarily uses the extra throughput provided by Event Hub. For a 6SU job, you may need 4 or 8 partitions from the Event Hub. Using an Event Hub with 16 partitions or larger in an 1SU job often contributes to excessive resource usage, and should be avoided. 

### Reference data 
Reference data in ASA are loaded into memory for fast lookup. With the current implementation, each join operation with reference data keeps a copy of the reference data in memory, even if you join with the same reference data multiple times. For queries with Partition By, each partition has a copy of the reference data, so the partitions are fully decoupled. With the multiplier effect, memory usage can quickly get very high if you join with reference data multiple times with multiple partitions.  



## Get help
For further assistance, try our [Azure Stream Analytics forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## Next steps
* [Introduction to Azure Stream Analytics](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Scale Azure Stream Analytics jobs](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics query language reference](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure Stream Analytics Management REST API reference](https://msdn.microsoft.com/library/azure/dn835031.aspx)
