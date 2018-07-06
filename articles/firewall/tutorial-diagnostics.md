---
title: Tutorial - Monitor Azure Firewall logs 
description: In this tutorial, you learn how to enable and manage Azure Firewall logs.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.workload: infrastructure-services
ms.date: 7/11/2018
ms.author: victorh
#Customer intent: As an administrator, I want monitor Azure Firewall logs so that I can track firewall activity.
---
# Tutorial: Monitor Azure Firewall logs

You can monitor Azure Firewall using firewall logs. You can also use activity logs to audit operations on Azure Firewall resources.

You can access some of these logs through the portal. All logs can be extracted from Azure Blob storage and viewed in different tools, such as [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Excel, and Power BI.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Enable logging through the Azure portal
> * Enable logging with PowerShell
> * View and analyze the activity log
> * View and analyze the network and application rule logs

## Diagnostic logs

 

The following logs are available for Azure Firewall:

* **Activity log**

   You can use [Azure activity logs](../azure-resource-manager/resource-group-audit.md) (formerly known as operational logs and audit logs) to view all operations that are submitted to your Azure subscription. Activity log entries are collected by default, and you can view them in the Azure portal.

* **Application rule log**

   The Application rule log is saved to a storage account, streamed to Event hubs and/or sent to Log Analytics only if you have enabled it for each Azure Firewall. Each new connection that matches one of your configured application rules results in a log for the accepted/denied connection. The data is logged in JSON format, as shown in the following example:

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

* **Network rule log**

   The Network rule log is saved to a storage account, streamed to Event hubs and/or sent Log Analytics only if you have enabled it for each Azure Firewall. Each new connection that matches one of your configured network rules results in a log for the accepted/denied connection. The data is logged in JSON format, as shown in the following example:

   ```
   Category: access logs are either application or network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message. 
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

> [!NOTE]
> Logs are available only for resources deployed in the Azure Resource Manager deployment model. You cannot use logs for resources in the classic deployment model. For a better understanding of the two models, see the [Understanding Resource Manager deployment and classic deployment](../azure-resource-manager/resource-manager-deployment-model.md) article.

You have three options for storing your logs:

* **Storage account**: Storage accounts are best used for logs when logs are stored for a longer duration and reviewed when needed.
* **Event hubs**: Event hubs are a great option for integrating with other security information and event management (SEIM) tools to get alerts on your resources.
* **Log Analytics**: Log Analytics is best used for general real-time monitoring of your application or looking at trends.

## Enable logging through the Azure portal

1. In the Azure portal, open your firewall resource group and click the firewall.
2. Under **Monitoring**, click **Diagnostic logs**.

   For Azure Firewall, two service-specific logs are available:

   * Application rule log
   * Network rule log

3. To start collecting data, click **Turn on tutorial-diagnostics**.
4. The **Diagnostics settings** page provides the settings for the diagnostic logs. 
5. In this example, Log Analytics stores the logs, so type **Firewall log analytics** for the name.
6. Click **Send to Log Analytics** to configure your workspace. You can also use event hubs and a storage account to save the diagnostic logs.
7. Under **Log Analytics**, click **Configure**.
8. In the OMS Workspaces page, click **Create New Workspace**.
9. On the **Log analytics workspace** page, type **firewall-oms** for the new **OMS Workspace** name.
10. Select your subscription, use the existing firewall resource group (**Test-FW-RG**), select **East US** for the location, and select the **Free** pricing tier.
11. Click **OK**.
   ![Starting the configuration process][1]
12. Under **Log**, click **AzureFirewallApplicationRule** and **AzureFirewallNetworkRule** to collect logs for application and network rules.
13. Under **Metric**, click **AllMetrics** to collect firewall metrics.
   ![Save diagnostics settings][2]
14. Click **Save**.

## Enable logging with PowerShell

Activity logging is automatically enabled for every Resource Manager resource. You must enable access and performance logging to start collecting the data available through those logs. To enable logging, use the following steps:

1. Note your storage account's resource ID, where the log data is stored. This value is of the form: */subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Storage/storageAccounts/\<storage account name\>*.

   You can use any storage account in your subscription. You can use the Azure portal to find this information.

<!---    ![Portal: resource ID for storage account](./media/tutorial-diagnostics/diagnostics1.png) -->

2. Note your Firewall's resource ID for which logging is enabled. This value is of the form: */subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Network/azureFirewalls/\<Firewall name\>*.

   You can use the portal to find this information.

<!---    ![Portal: resource ID for Firewall](./media/tutorial-diagnostics/diagnostics2.png) -->

3. Enable diagnostic logging by using the following PowerShell cmdlet:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```
    
> [!TIP] 
>Activity logs do not require a separate storage account. The use of storage for access and performance logging incurs service charges.

## View and analyze the activity log

You can view and analyze activity log data by using any of the following methods:

* **Azure tools**: Retrieve information from the activity log through Azure PowerShell, the Azure CLI, the Azure REST API, or the Azure portal. Step-by-step instructions for each method are detailed in the [Activity operations with Resource Manager](../azure-resource-manager/resource-group-audit.md) article.
* **Power BI**: If you don't already have a [Power BI](https://powerbi.microsoft.com/pricing) account, you can try it for free. By using the [Azure Activity Logs content pack for Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/), you can analyze your data with preconfigured dashboards that you can use as is or customize.

## View and analyze the network and application rule logs

Azure [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) can collect the counter and event log files from your Blob storage account. It includes visualizations and powerful search capabilities to analyze your logs.

You can also connect to your storage account and retrieve the JSON log entries for access and performance logs. After you download the JSON files, you can convert them to CSV and view them in Excel, Power BI, or any other data-visualization tool.

> [!TIP]
> If you are familiar with Visual Studio and basic concepts of changing values for constants and variables in C#, you can use the [log converter tools](https://github.com/Azure-Samples/networking-dotnet-log-converter) available from GitHub.


## Next steps

* Visualize counter and event logs by using [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md).
* [Visualize your Azure activity log with Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blog post.
* [View and analyze Azure activity logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blog post.

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
