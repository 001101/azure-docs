---
title: Intelligent Insights monitors database usage - Azure SQL Database | Microsoft Docs
description: Intelligent Insights lets you know what is happening with your database performance
services: sql-database
documentationcenter: ''
author: danimir
manager: drasumic
editor: carlrab

ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/25/2017
ms.author: v-daljep

---
# Intelligent Insights

Intelligent Insights lets you know what is happening with your database performance.

Azure SQL Database built-in intelligence continuously monitors database usage through artificial intelligence and detects disruptive events that cause poor performance. A detailed analysis is performed on detected events, which generates a diagnostics log with an intelligent assessment of the issue. This assessment consists of a root cause analysis of the database performance issue and recommendations for performance improvements where possible.  

## What can Intelligent Insights do for you?

Intelligent Insights is a unique capability of Azure's built-in intelligence that provides the following value:

- Proactive monitoring
- Tailored performance insights
- Early detection of database performance degradation
- Root cause analysis of issues detected
- Performance improvement recommendations
- Scale out capability on hundreds of thousands of databases
- Positive impact to DevOps resources and the total cost of ownership

## How does Intelligent Insights work?

Intelligent Insights analyzes SQL Database performance by comparing the database workload from the last hour with the past seven-day baseline workload. Database workload is composed of queries determined to be the most significant to the database performance, such as the most repeated and largest queries. Each database is unique based on its structure, data, usage, and application because each workload baseline generated is specific and unique to an individual instance. Intelligent Insights, independent of the workload baseline, also monitors absolute operational thresholds and detects issues with excessive wait times, critical exceptions, and issues with query parameterizations that might affect performance.

After a performance degradation issue is detected from multiple observed metrics by using artificial intelligence, analysis is performed. A diagnostic log is output with an intelligent insight on what is happening with your database. Intelligent Insights makes it easy to track the database performance issue from its first appearance until resolution. Each detected issue is tracked through its lifecycle from initial issue detection and verification of performance improvement to its completion. Updates are provided in the diagnostics log every 15 minutes. 

![Server](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

The metrics used to measure and detect database performance issues are based on query duration, timeout requests, excessive wait times, and errored requests. For mor information on metrics, see the [Detection metrics](sql-database-intelligent-insights.md#detection-metrics) section of this document.

## Degradations detected

Identified SQL Database performance degradations are recorded in the diagnostics log with intelligent entries that consist of the following properties:

| Property             | Details              |
| :------------------- | ------------------- |
| Database information  | Metadata about a database on which an insight was detected, such as resource URI. |
| Observed time range | Start and end time for the period of the detected insight. |
| Impacted metrics | Metrics that caused an insight to be generated: <ul><li>Query duration increase [seconds]</li><li>Excessive waiting [seconds]</li><li>Timed-out requests [percentage]</li><li>Errored-out requests [percentage].</li></ul>|
| Impact value | Value of a metric measured. |
| Impacted queries and error codes | Query hash or error code. These can be used to easily correlate to impacted queries. Metrics that consist of either query duration increase, waiting time, timeout counts, or error codes are provided. |
| Detections | Detection identified at the database during the time of an event. There are 15 detection patterns. For more information, see [Troubleshoot database performance issues with Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md). |
| Root cause analysis | Root cause analysis of the issue identified in a humanly readable format. Some insights might contain a performance improvement recommendation where possible. |
|||

## Issues state lifecycle: Active, Verifying, and Completed

Performance issues recorded in the diagnostics log are flagged with one of the three states of an issue lifecycle: Active, Verifying, and Completed. After a performance issue is detected, and as long it's deemed by SQL Database built-in intelligence as present, the issue is flagged as "Active". When the issue is considered mitigated, it's verified and the issue status is changed to "Verifying". After SQL Database built-in intelligence considers the issue resolved, the issue status is flagged to "Completed".

## Use Intelligent Insights

The Intelligent Insights diagnostics log can be sent to Azure Log Analytics, Azure Event Hubs, and Azure Storage as described in [Azure SQL Database metrics and diagnostics logging](sql-database-metrics-diag-logging.md). After the log is sent to one of these targets, the log can be used for custom alerting and monitoring development by using Microsoft or third-party tools. 

For more information on SQL Database performance troubleshooting by using Intelligent Insights, see [Troubleshoot Azure SQL Database performance issues with Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

## Built-in Intelligent Insights analytics with Log Analytics 

A Log Analytics solution provides reporting and alerting capabilities on top of the Intelligent Insights diagnostics log data. The following example shows an Intelligent Insights report in Azure SQL Analytics:

![Server](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

After the Intelligent Insights diagnostics log is configured to stream data to SQL Analytics, you can [monitor SQL Database by using SQL Analytics](../log-analytics/log-analytics-azure-sql.md).

## Custom integrations of Intelligent Insights log

For more information on custom alerting and monitoring development by using Microsoft or third-party tools, see [Use the Intelligent Insights database performance diagnostics log](sql-database-intelligent-insights-use-diagnostics-log.md).

## Set up Intelligent Insights with Azure Event Hubs

- To configure Intelligent Insights to stream log events in to Event Hubs, see [Stream Azure diagnostics logs to Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md).
- To use Event Hubs for custom monitoring and alerting, see [What to do with metrics and diagnostics logs in Event Hubs](sql-database-metrics-diag-logging.md#stream-into-azure-storage). 

## Set up Intelligent Insights with Storage

- To configure Intelligent Insights to be stored with Storage, see [Stream into Azure Storage](sql-database-metrics-diag-logging.md#stream-into-azure-storage).

## Detection metrics

Metrics used for detection models that generate Intelligent Insights are based on monitoring:

- Query duration.
- Timeout requests.
- Excessive wait time.
- Errored out requests.

Query duration and timeout requests are used as primary models in detecting issues with database workload performance. They're used because they directly measure what is happening with the workload. In order to detect all possible cases of workload performance degradation, excessive wait time and errored-out requests are used as additional models to indicate issues that affect the workload performance.

The system automatically considers changes to the workload and changes in the number of query requests made to the database to dynamically determine the normal and out-of-the-ordinary database performance thresholds.

All of the metrics are considered together in various relationships through a scientifically derived data model that categorizes each performance issue detected. Information provided through an intelligent insight includes:

* Details of the performance issue detected. 
* A root cause analysis of the issue occurrence. 
* Recommendations on how to improve the performance of SQL Database monitored where possible.

## Query duration

The query duration degradation model analyzes individual queries and detects the increase in time it takes to compile and execute a query compared to the performance baseline.

If SQL Database built-in intelligence detects a significant increase in query compile or query execution time that impacts workload performance, these queries are flagged as query duration performance degradation issues. 

The Intelligent Insights diagnostics log outputs the query hash of the query degraded in performance to indicate whether the performance degradation was related to query compile or execution time increase, and increased query duration time.

## Timeout requests

The timeout requests degradation model analyzes individual queries and detects increase in timeouts at the query execution level, and the overall request timeouts at the database level compared to the performance baseline period.

Because some of the queries might time out even before reaching the execution stage, SQL Database built-in intelligence also measures and analyzes, through the means of aborted workers vs. requests made, all queries that reached the database regardless of whether they reached the execution stage or not. 

After the number of timeouts for executed queries, or the number of aborted request workers increases above the system-managed threshold, a diagnostic log is populated with intelligent insights.

The insights generated contain the number of timed-out requests and the number of timed-out queries. They indicate whether the performance degradation was related to timeout increase at the execution stage or the overall database level. When the increase in timeouts is deemed significant to database performance, these queries are flagged as timeout performance degradation issues. 

## Excessive wait times

The excessive wait time model monitors individual database queries. It detects unusually high query wait stats above the system-managed absolute thresholds. The following query excessive wait-time metrics are observed by using the new SQL Server feature, Query Store Wait Stats (sys.query_store_wait_stats):

- Reaching resource limits
- Reaching elastic pool resource limits
- Excessive number of worker or session threads
- Excessive database locking
- Memory pressure
- Other wait stats

Reaching resource limits or elastic pool resource limits denote that consumption of available resources on a subscription or in the elastic pool has increased above absolute thresholds that indicates workload performance degradation. An excessive number of worker or session threads denotes a condition in which the number of worker threads or sessions initiated reached above absolute thresholds that indicates workload performance degradation.

Excessive database locking denotes a condition in which the count of locks on a database has reached over an absolute threshold indicating a workload performance degradation. Memory pressure is a condition in which the number of threads requesting memory grants has increased above an absolute threshold that indicates workload performance degradation.

Other wait stats detection indicate a condition in which miscellaneous metrics measured through the Query Store Wait Stats are reaching above an absolute threshold that indicates a workload performance degradation.

After excessive wait times are detected, the Intelligent Insights diagnostics log outputs, depending on the data available:

* Hashes of the affecting and affected queries degraded in performance. 
* Details of the metrics causing queries to wait in execution.
* Measured wait time.

## Errored requests

The errored requests degradation model monitors individual queries and detects an increase in the number of queries that errored out compared to the baseline period. This model also monitors critical exceptions that reached absolute thresholds managed by SQL Database built-in intelligence. The system automatically considers the number of query requests made to the database and accounts for any workload changes in the monitored period.

When the measured increase in errored requests in relationship with the overall number of requests made is deemed significant to the workload performance, impacted queries are flagged as errored requests performance degradation issues.

The Intelligent Insights log outputs the count of errored requests that indicates whether the performance degradation was related to increase in errored requests or to reaching one of the monitored critical exception thresholds and measured time of the performance degradation. 

If any of the monitored critical exceptions reach above the absolute thresholds managed by the system, an intelligent insight is generated with critical exception details.

## Next steps
* Learn how to [troubleshoot SQL Database performance issues with Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
* Use the [Intelligent Insights SQL Database performance diagnostics log](sql-database-intelligent-insights-use-diagnostics-log.md).
* Learn how to [monitor SQL Database by using SQL Analytics](../log-analytics/log-analytics-azure-sql.md).
* Learn how to [collect and consume log data from your Azure resources](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).


