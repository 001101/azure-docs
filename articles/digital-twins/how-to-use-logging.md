---
title: How to enable logging in Azure Digital Twins | Microsoft Docs
description: How to enable logging in Azure Digital Twins
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: adgera
---

# How to enable logging in Azure Digital Twins

Azure Digital Twins supports robust logging and analytics provided through Azure Log Analytics, diagnostic logs, and activity logging. Logging methods can be combined to summarize records across several services or to provide granular logging coverage for multiple services.

This article summarizes logging options and how to combine them in ways specific to Azure Digital Twins.

## Review activity logs

Azure [activity logs](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) provide quick insights into subscription-level event and operation histories for each Azure service instance.

Subscription-level events include:

* Resource creation
* Resource removal
* Creating app secrets
* Integrating other services

Activity logging for Azure Digital Twins is enabled by default and can be found through the Azure portal by:

1. Selecting your Azure Digital Twins instance.
1. Choosing **Activity log** to bring up the display panel:

    ![Activity log][1]

>[!TIP]
>Use **activity logs** for quick insights into subscription-level events.

## Enable customer diagnostic logs

Azure [diagnostic settings](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)  can be set for each Azure instance to supplement activity logging. Whereas activity logs pertain to subscription-levels events, diagnostic logging provides insights into the operational history of the resources themselves.

Examples of diagnostic logging include:

* The execution time for a user-driven function
* A successful API call
* Retrieving an app key from a vault

To set up diagnostic logging for an instance:

1. Bring up the resource in Azure portal.
1. Click **Diagnostic settings**:

    ![Diagnostic settings one][2]

1. Click **Turn on diagnostics** to collect data.
1. Fill in the requested fields and select how and where data will be saved:

    ![Diagnostic settings two][3]

>[!TIP]
>Use **diagnostic logs** for insights into resource operations.

## Azure monitor and log analytics

IoT applications unite disparate resources, devices, locations, and data into one. Fine-grained logging provides detailed information about each specific piece, service, or component of the overall application architecture but a holistic overview is often required for maintenance and debugging.

Azure Monitor contains the powerful Log Analytics service, which allows logging sources to be viewed and analyzed in one location. Azure Monitor is therefore highly useful for analyzing logs within sophisticated IoT apps.

Examples of use include:

* Querying multiple diagnostic log histories
* Seeing logs for several user-defined functions
* Displaying logs for two or more services within a specific time-frame

Full log querying capabilities are provided through [Azure Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-queries). To set up these powerful features:

1. Search for **Log Analytics** in the Azure portal.
1. You will see your available **Log Analytics** instances. Choose one and select **Logs** to query:

    ![Log analytics][4]

1. If you don't already have a **Log Analytics** instance, you can create a workspace by clicking the **Add** button:

    ![Create OMS][5]

Once your **Log Analytics** instance is provisioned, you may use powerful queries to find entries in multiples logs or search for specific data.

For more information about powerful query operations, see [getting started with queries](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries).

Azure Log Analytics also provides powerful error and alert notification services, which can be viewed by clicking **Diagnose and solve problems**:

   ![Alert and error notifications][6]

>[!TIP]
>Use **Log Analytics** to see log histories for multiple app functionalities or services.

## Other options

Azure Digital Twins also supports application-specific logging and security auditing. For a thorough overview of all Azure logging options available to your Azure Digital Twins instance, see the [Azure log audit](https://docs.microsoft.com/azure/security/azure-log-audit) article.

<!-- Images -->
[1]: media/how-to-use-logging/activity-log.png
[2]: media/how-to-use-logging/diagnostic-settings-one.png
[3]: media/how-to-use-logging/diagnostic-settings-two.png
[4]: media/how-to-use-logging/log-analytics.png
[5]: media/how-to-use-logging/log-analytics-oms.png
[6]: media/how-to-use-logging/log-analytics-notifications.png