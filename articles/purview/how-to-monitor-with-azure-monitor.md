---
title: Monitor Azure Purview with Azure Monitor
description: This article provides an overview of Purview metrics in Azure Monitor.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 12/01/2020
---
# Azure Purview metrics in Azure Monitor

This article provides an overview of Azure Purview metrics, alerts, and diagnostic settings in Azure Monitor.

## Monitor Azure Purview

Azure Purview admins can use Azure Monitor to track the operational state of Purview account. The metrics will provide data points for customers to track potential problems, troubleshoot, and improve the reliability of the Purview account. The telemetry is sent to Azure monitor for events occurring in Azure Purview.  

## Aggregated Metrics 

The Metrics can be accessed from the Azure portal for a Purview account. Access to the metrics are controlled by the role assignment of Purview account. Users need to be part of the "Monitoring Reader" role in Azure Purview to see the metrics. Check out [Monitoring Reader Role permissions](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/roles-permissions-security#built-in-monitoring-roles) to learn more about the roles access levels. 

The person who created the Purview account will automatically get permissions to view metrics but if anyone else wants to see metrics then they must be in the Monitoring Reader role. In order to add someone to the Monitoring Reader role the owner of Purview account or the Subscription owner must follow below steps

1. Go to the Azure portal https://portal.azure.com and search for the Azure Purview account name.  

1. Select Access control (IAM)  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/access-iam.png" alt-text="Screenshot showing how to access IAM." lightbox="./media/how-to-monitor-with-azure-monitor/access-iam.png":::

2. Select Add a role assignment  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-role-assignment.png" alt-text="Screenshot showing how to add role assignment." lightbox="./media/how-to-monitor-with-azure-monitor/add-role-assignment.png":::


3. Select Role as “Monitoring Reader” and assign  access to Azure AD user. And assign the AAD account to access the metrics.  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-monitoring-reader.png" alt-text="Screenshot showing how to add monitoring reader role." lightbox="./media/how-to-monitor-with-azure-monitor/add-monitoring-reader.png":::



## Metrics visualization 

Users with a role assigned as “Monitoring Reader” on Azure Purview can see the aggregated metrics and diagnostic logs sent to Azure Monitor.  The metrics are listed in the Azure portal for the corresponding Purview account. In the Azure portal, select the Metrics section to see the list of all available metrics.  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/purview-metrics.png" alt-text="Screenshot showing available Purview metrics section." lightbox="./media/how-to-monitor-with-azure-monitor/purview-metrics.png":::

Azure Purview users can also access the metrics page directly from the management center of the Azure Purview account. Select Azure Monitor in the main page of Purview management center to launch Azure portal.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png" alt-text="Screenshot to launch Purview metrics from management center." lightbox="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png":::

### Available Metrics

To get familiarized with how to use the metric section in the Azure portal pre read the following two documents. [Getting started with Metric Explorer](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/metrics-getting-started)(3 min read) and [Advanced features of Metric Explorer](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/metrics-charts)(5 min read) 

Below table contains the list of metrics available to explore in the Azure portal 

| Metric Name | Metric Namespace | Aggregation type | Description |
| ------------------- | ------------------- | ------------------- | ----------------- |
| Scan Canceled | Automated scan | Sum <br> Count | Aggregate the canceled data source scans over time period |
| Scan Completed | Automated scan | Sum <br> Count | Aggregate the completed data source scans over time period |
| Scan Failed | Automated scan | Sum <br> Count | Aggregate the failed data source scans over time period |
| Scan time taken | Automated scan | Min <br> Max <br> Sum <br> Avg | Aggregate the total time taken by scans over time period |



## Diagnostic Logs to Azure Storage account 

Raw telemetry events are emitted to Azure Monitor. Events can be logged to a customer storage account of choice for further analysis. Exporting of logs is done via the Diagnostic settings for the Purview account on the Azure portal. 

Follow the steps to create a Diagnostic setting for your Azure Purview account.


1. Create a new diagnostic setting to collect platform logs and metrics -> https://docs.microsoft.com/en-us/azure/azure-monitor/platform/diagnostic-settings (select the destination only as Azure storage account)

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/stepone-diagnostic-setting.png" alt-text="Screenshot showing creating diagnostic log." lightbox="./media/how-to-monitor-with-azure-monitor/stepone-diagnostic-setting.png":::

1. Log the events to a storage account. A dedicated storage account is recommended for archiving the  diagnostic logs. Create a new Azure Storage account by following steps -> https://docs.microsoft.com/en-us/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal 
 
   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/steptwo-diagnostic-setting.png" alt-text="Screenshot showing assigning storage account for diagnostic log." lightbox="./media/how-to-monitor-with-azure-monitor/steptwo-diagnostic-setting.png":::

Allow up to 15 minutes to start receiving logs in the newly created storage account. [See data retention and schema of resource logs in Azure Storage account](https://docs.microsoft.com/en-us/azure/azure-monitor/platform/resource-logs-collect-storage#data-retention). Once the diagnostic logs are configured, the events flow to the storage account.


### ScanStatusLogEvent
The event tracks the scan life cycle. A scan operation follows progress through a sequence of states, from Queued, Running and finally a terminal state of Succeeded | Failed | Canceled. An event is logged for each state transition and the schema of the event will have the following properties. 


```JSON
{
  "time": "<The UTC time when the event occurred>",
  "properties": {
    "dataSourceName": "<Registered data source friendly name>",
    "dataSourceType": "<Registered data source type>",
    "scanName": "<Scan instance friendly name>",
    "assetsDiscovered": "<If the resultType is succeeded, count of assets discovered in scan run>",
    "assetsClassified": "<If the resultType is succeeded, count of assets classified in scan run>",
    "scanQueueTimeInSeconds": "<If the resultType is succeeded, total seconds the scan instance in queue>",
    "scanTotalRunTimeInSeconds": "<If the resultType is succeeded, total seconds the scan took to run>",
    "runType": "<How the scan is triggered>",
    "errorDetails": "<Scan failure error>",
    "scanResultId": "<Unique GUID for the scan instance>"
  },
  "resourceId": "<The azure resource identifier>",
  "category": "<The diagnostic log category>",
  "operationName": "<The operation that cause the event Possible values for ScanStatusLogEvent category are: 
                    |AdhocScanRun 
                    |TriggeredScanRun 
                    |StatusChangeNotification>",
  "resultType": "Queued – indicates a scan is queued. 
                 Running – indicates a scan entered a running state. 
                 Succeeded – indicates a scan completed successfully. 
                 Failed – indicates a scan failure event. 
                 Cancelled – indicates a scan was cancelled. ",
  "resultSignature": "<Not used for ScanStatusLogEvent category. >",
  "resultDescription": "<This will have an error message if the resultType is Failed. >",
  "durationMs": "<Not used for ScanStatusLogEvent category. >",
  "level": "<The log severity level. Possible values are:
            |Informational 
            |Error >",
  "location": "<The location of the Azure Purview account>",
}
```

The Sample log for an event instance is shown in the below section.

```JSON
{
  "time": "2020-11-24T20:25:13.022860553Z",
  "properties": {
    "dataSourceName": "AzureDataExplorer-swD",
    "dataSourceType": "AzureDataExplorer",
    "scanName": "Scan-Kzw-shoebox-test",
    "assetsDiscovered": "0",
    "assetsClassified": "0",
    "scanQueueTimeInSeconds": "0",
    "scanTotalRunTimeInSeconds": "0",
    "runType": "Manual",
    "errorDetails": "empty_value",
    "scanResultId": "0dc51a72-4156-40e3-8539-b5728394561f"
  },
  "resourceId": "/SUBSCRIPTIONS/111111111111-111-4EB2/RESOURCEGROUPS/FOOBAR-TEST-RG/PROVIDERS/MICROSOFT.PURVIEW/ACCOUNTS/FOOBAR-HEY-TEST-NEW-MANIFEST-EUS",
  "category": "ScanStatusLogEvent",
  "operationName": "TriggeredScanRun",
  "resultType": "Delayed",
  "resultSignature": "empty_value",
  "resultDescription": "empty_value",
  "durationMs": 0,
  "level": "Informational",
  "location": "eastus",
}
```
## Next Steps

> [!div class="nextstepaction"]
> [Scan Azure Data sources](portal-scan-azure-data-sources.md)