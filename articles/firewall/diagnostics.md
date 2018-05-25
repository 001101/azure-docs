---
title: Monitor access logs, performance logs, and metrics for Azure Firewall
description: Learn how to enable and manage access logs and performance logs for Azure Firewall
services: firewall
author: vhorne
tags: azure-resource-manager

ms.service: firewall
ms.topic: article
ms.workload: infrastructure-services
ms.date: 5/25/2018
ms.author: victorh

---
# Diagnostic logs and metrics for Azure Firewall

You can monitor Azure Firewall resources in the following ways:

* [Logs](#diagnostic-logging): You can save performance logs, access logs, and other data. You can then use this data for monitoring purposes.

* [Metrics](#metrics): Azure Firewall currently has xxx  metrics to view performance counters.


## <a name="diagnostic-logging"></a>Diagnostic logs

You can use different types of logs in Azure to manage and troubleshoot Azure Firewalls. You can access some of these logs through the portal. All logs can be extracted from Azure Blob storage and viewed in different tools, such as [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Excel, and Power BI. You can learn more about the different types of logs from the following list:

* **Activity log**: You can use [Azure activity logs](../monitoring-and-diagnostics/insights-debugging-with-events.md) (formerly known as operational logs and audit logs) to view all operations that are submitted to your Azure subscription, and their status. Activity log entries are collected by default, and you can view them in the Azure portal.

* **Application rule log**:
* **Network rule log**:


> [!NOTE]
> Logs are available only for resources deployed in the Azure Resource Manager deployment model. You cannot use logs for resources in the classic deployment model. For a better understanding of the two models, see the [Understanding Resource Manager deployment and classic deployment](../azure-resource-manager/resource-manager-deployment-model.md) article.

You have three options for storing your logs:

* **Storage account**: Storage accounts are best used for logs when logs are stored for a longer duration and reviewed when needed.
* **Event hubs**: Event hubs are a great option for integrating with other security information and event management (SEIM) tools to get alerts on your resources.
* **Log Analytics**: Log Analytics is best used for general real-time monitoring of your application or looking at trends.

### Enable logging through PowerShell

Activity logging is automatically enabled for every Resource Manager resource. You must enable access and performance logging to start collecting the data available through those logs. To enable logging, use the following steps:

1. Note your storage account's resource ID, where the log data is stored. This value is of the form: /subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Storage/storageAccounts/\<storage account name\>. You can use any storage account in your subscription. You can use the Azure portal to find this information.

    ![Portal: resource ID for storage account](./media/application-gateway-diagnostics/diagnostics1.png)

2. Note your Firewall's resource ID for which logging is enabled. This value is of the form: /subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Network/firewalls/\<Firewall name\>. You can use the portal to find this information.

    ![Portal: resource ID for Firewall](./media/application-gateway-diagnostics/diagnostics2.png)

3. Enable diagnostic logging by using the following PowerShell cmdlet:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/firewall/<Firewall name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Activity logs do not require a separate storage account. The use of storage for access and performance logging incurs service charges.

### Enable logging through the Azure portal

1. In the Azure portal, find your resource and click **Diagnostic logs**.

   For Firewall, three logs are available:

   * Access log
   * Performance log
   * Firewall log

2. To start collecting data, click **Turn on diagnostics**.

   ![Turning on diagnostics][1]

3. The **Diagnostics settings** blade provides the settings for the diagnostic logs. In this example, Log Analytics stores the logs. Click **Configure** under **Log Analytics** to configure your workspace. You can also use event hubs and a storage account to save the diagnostic logs.

   ![Starting the configuration process][2]

4. Choose an existing Log Analytics workspace or create a new one. This example uses an existing one.

   ![Options for Log Analytics workspaces][3]

5. Confirm the settings and click **Save**.

   ![Diagnostics settings blade with selections][4]

### Activity log

Azure generates the activity log by default. The logs are preserved for 90 days in the Azure event logs store. Learn more about these logs by reading the [View events and activity log](../monitoring-and-diagnostics/insights-debugging-with-events.md) article.

### Application rule log

Application rule log overview. Each application rule access is logged in JSON format, as shown in the following example:


|Value  |Description  |
|---------|---------|
|msg     | Need description|

```json
{
     "category": "AzureFirewallApplicationRule",
     "time": "2018-04-16T23:45:04.8295030Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/SECUREGATEWAYS/{resourceName}",
     "operationName": "AzureFirewallApplicationRuleLog",
     "properties": "{\"msg\":\"HTTPS request from 10.1.0.5 to mydestination.com was allowed. Action: Allow. Rule Collection: collection1000. Rule: rule1002\"}",
     "Tenant": "REPLACE_ME",
     "Role": "sgw",
     "RoleInstance": "gsa-a88322d6-8879000002"
}

```

### Network rule log

Network rule log description. The following data is logged:


|Value  |Description  |
|---------|---------|
|xxx     | xxx|


```json
{
xxx
}
```


### View and analyze the activity log

You can view and analyze activity log data by using any of the following methods:

* **Azure tools**: Retrieve information from the activity log through Azure PowerShell, the Azure CLI, the Azure REST API, or the Azure portal. Step-by-step instructions for each method are detailed in the [Activity operations with Resource Manager](../azure-resource-manager/resource-group-audit.md) article.
* **Power BI**: If you don't already have a [Power BI](https://powerbi.microsoft.com/pricing) account, you can try it for free. By using the [Azure Activity Logs content pack for Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), you can analyze your data with preconfigured dashboards that you can use as is or customize.

### View and analyze the access, performance, and firewall logs

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) can collect the counter and event log files from your Blob storage account. It includes visualizations and powerful search capabilities to analyze your logs.

You can also connect to your storage account and retrieve the JSON log entries for access and performance logs. After you download the JSON files, you can convert them to CSV and view them in Excel, Power BI, or any other data-visualization tool.

> [!TIP]
> If you are familiar with Visual Studio and basic concepts of changing values for constants and variables in C#, you can use the [log converter tools](https://github.com/Azure-Samples/networking-dotnet-log-converter) available from GitHub.
> 
> 

## Metrics

Metrics are a feature for certain Azure resources where you can view performance counters in the portal. For Firewall, the following metrics are available:

- **xxx**
- **yyy**
- **zzz**



Browse to a Firewall, under **Monitoring** click **Metrics**. To view the available values, select the **METRIC** drop-down list.


### Alert rules

You can start alert rules based on metrics for a resource. For example, an alert can call a webhook or email an administrator if the throughput of the Firewall is above, below, or at a threshold for a specified period.

The following example walks you through creating an alert rule that sends an email to an administrator after throughput breaches a threshold:

1. Click **Add metric alert** to open the **Add rule** blade. You can also reach this blade from the metrics blade.

   !["Add metric alert" button][6]

2. On the **Add rule** blade, fill out the name, condition, and notify sections, and click **OK**.

   * In the **Condition** selector, select one of the four values: **Greater than**, **Greater than or equal**, **Less than**, or **Less than or equal to**.

   * In the **Period** selector, select a period from five minutes to six hours.

   * If you select **Email owners, contributors, and readers**, the email can be dynamic based on the users who have access to that resource. Otherwise, you can provide a comma-separated list of users in the **Additional administrator email(s)** box.

   ![Add rule blade][7]

If the threshold is breached, an email that's similar to the one in the following image arrives:

![Email for breached threshold][8]

A list of alerts appears after you create a metric alert. It provides an overview of all the alert rules.

![List of alerts and rules][9]

To learn more about alert notifications, see [Receive alert notifications](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

To understand more about webhooks and how you can use them with alerts, visit [Configure a webhook on an Azure metric alert](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## Next steps

* Visualize counter and event logs by using [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md).
* [Visualize your Azure activity log with Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blog post.
* [View and analyze Azure activity logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blog post.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
