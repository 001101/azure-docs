﻿---
title: Unified alerts in Azure Monitor| Microsoft Docs
description: Understand how the new simple and scalable alerts experience in Azure makes authoring, viewing and managing alerts easier.
author: manishsm-msft
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics

ms.assetid:
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/28/2018
ms.author: mamit,bwren
ms.custom:

---
# Unified alerts in Azure Monitor

## Overview

> [!NOTE]
>  A new unified alert experience that provides enhanced management of alerts and introduces alert states is currently available in public preview. See the [last section of this article](#enhanced-unified-alerts-experience-public-preview) for a description of this enhanced experience and the process to enabled it.

> [!NOTE]
> This article describes the unified alert experience in Azure Monitor. Classic alerts are described in [classic alerts Overview](monitoring-overview-alerts.md).

This article describes the unified alert experience in Azure Monitor. The [previous alert experience](monitoring-overview-alerts.md) is available from the **Alerts (Classic)** option in the Azure Monitor menu. 

## Benefits of unified alert experience

The unified experience has the following benefits over the classic experience:

-	**Better notification system**: Unified alerts use [action groups]( https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups), which are named groups of notifications and actions that can be reused in multiple alerts.  Classic metric alerts and older Log Analytics alerts do not use action groups.
- **Unified authoring experience**  - You can manage alerts and alert rules for metrics, logs and activity log across Azure Monitor, Log Analytics, and Application Insights in one place. 
- **View fired Log Analytics alerts in Azure portal** - View alerts from Log Analytics with other alerts from other sources in the Azure portal. Previously these were in a separate portal.
- **Separation of Fired alerts and Alert Rules** - Alert rules are now distinguished from alerts. An alert rule is the definitions of a condition that triggers an alert. An alert is an instance of an alert rule firing.
- **Better workflow** - The unified alert authoring experience guides you through the process of configuring an alert rule.
 
Metric alerts have the following improvements over classic metric alerts:

-	**Improved latency**: Metric alerts can run as frequently as every one minute. Classic metric alerts always run at a frequency of 5 minutes. Log alerts still have a delay longer than one minute due to the time is takes to ingest the logs. 
-	**Support for multi-dimensional metrics**: You can alert on dimensional metrics allowing you to monitor a specific instance of the metric.
-	**More control over metric conditions**: You can define richer alert rules that support monitoring the maximum, minimum, average, and total values of metrics.
-	**Combined monitoring of multiple metrics**: You can monitor up to two metrics with a single rule. An alert is triggered if both metrics breach their respective thresholds for the specified time-period.
-	**Metrics from Logs** (limited public preview): Some log data going into Log Analytics can now be extracted and converted into Azure Monitor metrics and then alerted on just like other metrics. 


## Alert rule terminology
The unified alerts experience uses the following concepts to separate the alert rules from alerts while unifying the authoring experience across different alert types.

| Item | Definition |
|:---|:---|
| Alert rule | Definition of the condition to create an alert. Composed of a _target resource_, _signal_, _criteria_, and _logic_. An alert rule is only active if it's in an _enabled_ state.
| Target Resource | Defines the scope and signals available for alerting. A target can be any Azure resource.<br>Examples: a virtual machine, a storage account, a virtual machine scale set, a Log Analytics workspace, or an Application Insights resource. |
| Signal | Source of data emitted by the Target resource. Supported signal types are *Metric*, *Activity log*, *Application Insights*, and *Log*. |
| Criteria | Combination of _signal_ and _logic_ applied on a target resource.<br>Examples: Percentage CPU > 70%, Server Response Time > 4 ms, Result count of a log query > 100 etc. |
| Logic | User-defined logic to check if the signal is within expected range/values. |
| Action | Action to perform when the alert is fired. Multiple actions may occur when an alert fires. These alerts support action groups.<br>Examples: emailing an email address, calling a webhook URL. |


## Alert pages
The goal of the Alerts experience is to be the single place to view and manage all your Azure alerts. The following sections describe the functions of each individual page of the unified experience.

### Alerts overview page
**Monitor - Alerts** overview page shows an aggregated summary of all the fired alerts, and total configured/enabled alert rules. It also shows a list of all fired alerts. Changing the subscriptions or filter parameters updates the aggregates and the alerts fired list.

> [!NOTE]
> Fired Alerts shown in Alerts are limited to supported metric and activity log alerts. Azure Monitor Overview shows count of fired alerts including those in classic Azure Alerts.

 ![alerts-overview](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### Alert rules management
**Monitor - Alerts>Rules** is a single page to manage all alert rules across your Azure subscriptions. It lists all the alert rules (enabled or disabled) and can be sorted based on target resources, resource groups, rule name, or status. Alert rules can also be disabled/enabled or edited from this page.  

 ![alerts-rules](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## Creating an alert rule
In the unified alerts experience, alerts can be authored in a consistent manner regardless of the monitoring service or signal type. All fired alerts and related details are available in single page.
 
You create a new alert rule with the following three steps:
1. Pick the _target_ for the alert.
1. Select the _signal_ from the available signals for the target.
1. Specify the _logic_ to be applied to data from the signal.
 
This simplified authoring process no longer requires the user to know the monitoring source or signals supported before selecting an Azure resource. The common authoring experience automatically filters the list of available signals based on target resource selected and guides the creation of alert logic

You can learn more on how to create following alert types [here](monitor-alerts-unified-usage.md).
- Metric Alerts
- Log alerts (Log Analytics)
- Log alerts (Activity Logs)
- Log alerts (Application Insights)

 
Alerts are available across several Azure monitoring services. For information on how and when to use each of these services, see [Monitoring Azure applications and resources](./monitoring-overview.md). The following table provides a listing of the types of alert rules available across Azure and what's currently supported by the unified alert experience.


| **Signal Type** | **Monitor Source** | **Description** | 
|-------------|----------------|-------------|
| Metric | Azure monitor | Also called [near-real-time metric alerts](monitoring-near-real-time-metric-alerts.md), they support evaluating metric conditions as frequently as 1 minute and allow for multi-metric and multi-dimensional metric rules. A list of supported resource types is available [here](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported). |
| Metric | Azure monitor | [Older classic metric alerts](monitoring-overview-alerts.md) are not supported in the new alerts experience. You can find them under Alerts (Classic) in the Azure portal. The classic alerts support some metrics types that have not yet been moved to the newer alerts. For a full list, see [supported metrics](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-supported-metrics)
| Logs  | Log Analytics | Receive notifications or run automated actions when a Log search query over metric and/or event data meets certain criteria. Older Log Analytics alerts are still available, but are [being copied into the new experience](monitoring-alerts-extend.md). In addition, a [preview of *Log Analytics logs as metrics*](monitoring-alerts-extend-tool.md) is available. The preview allows you to take some types of logs and convert them to metrics, where you can then alert on them using the new alerting experience. The preview is useful if you have non-Azure logs that you want to get alongside native Azure Monitor metrics. |
| Activity Log | Activity Logs (general) | Contains the records of all Create, Update, and Delete actions performed through the selected target (resource/resource group/subscription). |
| Activity Log  | Service Health | Not supported in new alerts experience. See [Create activity log alerts on service notifications](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Logs  | Application Insights | Contains logs with the performance details of your application. Using analytics query, you can define the conditions for the actions to be taken - based on the application data. |
| Metric | Application Insights | Not supported in new alerts experience. See [Metric alerts](../application-insights/app-insights-alerts.md) |
| Web Availability Tests | Application Insights | Not supported in Alerts experience.  See [Web test alerts](../application-insights/app-insights-monitor-web-app-availability.md). Available to any website instrumented to send data to Application Insights. Receive a notification when availability or responsiveness of a website is below expectations. |


## Enhanced unified alerts experience (Public Preview)

An enhanced unified alerts experience was released in public preview for Azure Monitor on June 1, 2018. This experience builds on the benefits of the [unified alerts experience](#overview) released March 2018 and provides the ability to manage and aggregate individual alerts in addition to modifying alert state. This section describes the new features and how to navigate the new alert pages in the Azure portal.

### Features in the new alert experience

The new experience provides the following features that aren't available in the classic unified experience:

- View and manage individual alerts.
- Aggregate alerts across multiple subscriptions into single view organized by alert severity. 
- Change the state of alerts to reflect where it current is in the resolution process. 
- Organize alerts by smart groups which are automatically generated with machine learning algorithms.

### Enable the new alert experience
Enable the new alert experience by clicking on the banner at the top of the Alerts page. This process creates an alert store that includes the past 30 days of fired alerts across supported monitor services. It may take a few minutes for the new experience to be initially enabled.

![Banner](media/monitoring-overview-unified-alerts/opt-in-banner.png)

Once the new experience is enabled, you can switch back and forth between the new and old experience by clicking on the banner.

All subscriptions that you have access to will be enrolled when you enable the new experience. Although the entire subscription is enabled, only users that selected the new experience will be able to view it. Other users with access to the subscription must enable the experience separately.

Enabling the new alert experience does not impact the configuration of action groups or notifications in your alert rules. It only changes the way that you view and manage fired instances of the alerts in the Azure portal.

### Smart Groups
Smart groups reduce noise by allowing you to manage related alerts as a single unit rather than managing the individual alerts. You can view the details of smart groups and set the state similar to an alert. Each alert is a member of one and only one smart group. 

Smart Groups are automatically created using machine learning by combining alerts that represent a single issue. Alerts are evaluated on the basis of similar properties, historical patterns, or both. The algorithm considers when an alert is created and looks for similarity based on such properties as alert name, description, and subscription. The algorithm currently only considers alerts from the same service in the same subscription.

You can't create custom smart groups.

### Alert States
The new unified alert experience introduces the concept of alert state. The state of an alert specifies where it is in the resolution process.  When an alert is created, it has a status of *New*. You can change the status when you've acknowledged an alert and when you've closed it.  The following alert states are supported.

| State | Description |
|:---|:---|
| New | The issue has just been detected and not yet reviewed. |
| Acknowledged | An administrator has reviewed the alert and started working on it. |
| Closed | The issue has been resolved. Note that once an alert has been closed, you can reopen it my changing it to another state. |

The state of an alert is different than the monitor condition. Metric alert rules can set an alert to a condition of _resolved_ when the error condition is no longer met. Alert state is set by the user and is independent of the monitor condition. Even though the system may set the monitor condition to resolved, the alert state isn't changed until the user changes it.

#### Changing the state of an alert or smart group
You can change the state of an individual alert or managed multiple alerts together by setting the state of a smart group.

You can change the state of an alert by clicking on **Change alert state** in the detail view for the alert or **Change smart group state** in the detail view for the smart group. You can change the state of multiple items at one time by selecting them in a list view and clicking **Change State** at the top of the page. In both cases, select a new state from the dropdown and optionally provide a comment. If you're changing a single item, then you also have an option to apply the same changes to all the alerts in the smart group.

![Change state](media/monitoring-overview-unified-alerts/change-tate.png)

### Alerts page
The default Alerts page provides a quick summary of alerts that are created within a particular time window. It displays the total alerts for each severity with columns identifying the total number of alerts in each state for each severity. Click any of the severities to open the [All Alerts](#all-alerts-page) page filtered by that severity.

![Alerts page](media/monitoring-overview-unified-alerts/alerts-page.png)

You can filter this view by selecting values in the dropdowns at the top of the page.

| Column | Description |
|:---|:---|
| Subscription | Select up to 5 Azure subscriptions. Only alerts in the selected subscriptions are included in the view. |
| Resource Group | Select a single resource group. Only alerts with targets in the selected resource group are included in the view. |
| Time Range | Only alerts fired within the selected time window will be included in the view. Supported values are past hour, past 24 hours, past 7 days, and past 30 days. |

The Alerts page displays the following values. You can click on each to open another page.

| Value | Description |
|:---|:---|
| Total Alerts | Total number of alerts that match the selected criteria. Click this value to open the All Alerts view with no filter. |
| Smart Groups | Total number of smart groups created from the alerts that match the selected criteria. Click this value to open the Smart Groups list in the All Alerts view.
| Total Alert Rules | Total number of alert rules in the selected subscription and resource group. Click this value to open the Rules view filtered on the selected subscriptions and resource group.


### All Alerts page 
The All Alerts page allows you to view a list of alerts that were created within the selected time window. You can either view a list of the individual alerts or a list of the smart groups containing the alerts. Click the banner at the top of the page to toggle between views.

![All Alerts page](media/monitoring-overview-unified-alerts/all-alerts-page.png)

You can filter the view by selecting the following values in the dropdowns at the top of the page.

| Column | Description |
|:---|:---|
| Subscription | Select up to 5 Azure subscriptions. Only alerts in the selected subscriptions are included in the view. |
| Resource Group | Select a single resource group. Only alerts with targets in the selected resource group are included in the view. |
| Resource Type | Select one or more resource types. Only alerts with targets of the selected type are included in the view. This column is only available once a resource group has been specified. |
| Resource | Select a resource. Only alerts with that resources as a target are included in the view. This column is only available once a resource type has been specified. |
| Severity | Select an alert severity or select *All* to include alerts of all severities. |
| Monitor Condition | Select a monitor condition or select *All* to include alerts of conditions. |
| Alert State | Select an alert state or select *All* to include alerts of states. |
| Monitor Service | Select a service or select *All* to include all services. Only alerts created by rules using that service as a target are included. |
| Time Range | Only alerts fired within the selected time window will be included in the view. Supported values are past hour, past 24 hours, past 7 days, and past 30 days. |

Click **Columns** at the top of the page to select which columns to display. You can remove any column except for 

### Alert detail page
The Alert Detail page is displayed when you click on an alert. It provides details of the alert and allows you to change its state.

![Alert Detail](media/monitoring-overview-unified-alerts/alert-detail.png)

The Alert Detail page includes the following sections.

| Section | Description |
|:---|:---|
| Essentials | Displays the properties and other significant information about the alert. |
| History | Lists each action taken by the alert and any changes made to the alert. |
| Smart Group | Information about the smart group the alert is included in. The **Alert Count** refers to the number of alerts included in the smart group. This includes the other alerts that are included in the same same smart group that were created in the past 30 days.  This is regardless of the time filter in the alerts list page. Click on an alert to view its detail. |
| More Details | Displays granular data for the alert. This information is typically specific to the type of source that created the alert. |


### Smart Group detail page
The Smart Group Detail page is displayed when you click on a smart group. It provides details of the smart group, including the reasoning used to create the group, and allows you to change its state.
 
![Smart Group Detail](media/monitoring-overview-unified-alerts/smart-group-detail.png)


The Smart Group Detail page includes the following sections.

| Section | Description |
|:---|:---|
| Alerts | Lists the individual alerts that are included in the smart group. Click on an alert to open its Alert Detail page. |
| History | Lists each action taken by the smart group and any changes made to it. |

## Next steps
- [Learn how to use the new Alerts experience to create, view, and manage alerts](monitor-alerts-unified-usage.md)
- [Learn about log alerts in Alerts experience](monitor-alerts-unified-log.md)
- [Learn about metric alerts in Alerts experience](monitoring-near-real-time-metric-alerts.md)
- [Learn about Activity log alerts in Alerts experience](monitoring-activity-log-alerts-new-experience.md)
