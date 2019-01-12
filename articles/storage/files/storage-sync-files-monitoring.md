---
title: Monitoring Azure File Sync | Microsoft Docs
description: How to monitor Azure File Sync.
services: storage
author: jeffpatt24
ms.service: storage
ms.topic: article
ms.date: 01/11/2019
ms.author: jeffpatt
ms.component: files
---

# Monitoring Azure File Sync

Use Azure File Sync to centralize your organization's file shares in Azure Files, while keeping the flexibility, performance, and compatibility of an on-premises file server. Azure File Sync transforms Windows Server into a quick cache of your Azure file share. You can use any protocol that's available on Windows Server to access your data locally, including SMB, NFS, and FTPS. You can have as many caches as you need across the world.

This article describes how to monitor your Azure File Sync deployment. Additional monitoring options (like alerts) will be available in future Azure File Sync updates.

The following monitoring options are available currently:

## Azure Portal

Use the portal to view Registered server state and Server endpoint health (sync health).

Registered Server State
- If the Registered server state = Online, the server is successfully communicating with the service.
- If Registered server state = Appears Offline, verify the Storage Sync Monitor (AzureStorageSyncMonitor.exe) process on the server is running. If the server is behind a Firewall or Proxy, configure the firewall and proxy per our [documentation](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-firewall-and-proxy).

Server Endpoint Health 
- The server endpoint health in the portal is based on the sync events that are logged in the Telemetry event log on the server (ID 9102 and 9302). If a sync session fails due a transient error (e.g., error cancelled), sync may still show healthy in the portal as long as the current sync session is making progress (Event ID 9302 is used to determine if files are being applied).
- If the portal shows a sync error due to sync not making progress, check the [Troubleshooting documentation](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) for guidance.

## Event Logs

Use the Telemetry event log on the server to monitor registered server, sync, and cloud tiering health. The Telemetry event log is located under Applications and Services\Microsoft\FileSync\Agent in Event Viewer.

Sync
- Event ID 9102 is logged once a sync session completes. This event should be used to determine if sync sessions are completing successfully (HResult = 0) and if there are per-item sync errors. See the following documentation for more information: [Sync Health](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#broken-sync) & [Per-Item Errors](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-see-if-there-are-specific-files-or-folders-that-are-not-syncing).
- Event ID 9302 is logged every 5 to 10 minutes if there’s an active sync session. This event should be used to determine if the current sync session is making progress (AppliedItemCount > 0). If sync is not making progress, the sync session should eventually fail and an Event ID 9102 will be logged with the error. See the following documentation for more information: [Sync Progress](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-troubleshoot?tabs=server%2Cazure-portal#how-do-i-monitor-the-progress-of-a-current-sync-session)

Registered Server Health
- Event ID 9301 is logged every 30 seconds when a server queries the service for any jobs. If GetNextJob completes with status = 0, the server is able to communicate with the service. If GetNextJob completes with an error, check the [Troubleshooting documentation](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#common-sync-errors) for guidance.

Cloud Tiering
- Tiering 
  - Use Event ID 9002, 9003, 9016 and 9029 to monitor tiering activity and errors. See [How to monitor cloud tiering activity?](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#monitor-tiering-activity) for more information.
- Recall 
  - Use Event ID 9005, 9006, 9007 to monitor recall activity and errors. See [How do I monitor recall activity on a server?](https://docs.microsoft.com/en-us/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cazure-portal#monitor-recall-activity) for more information.

## Azure Monitor

Use [Azure Monitor](https://docs.microsoft.com/en-us/azure/azure-monitor/overview) to monitor sync, cloud tiering and server connectivity. Metrics for Azure File Sync are enabled by default and are sent to Azure Monitor every 15 minutes.

To view Azure File Sync metrics in Azure Monitor, select the Storage Sync Services resource type.

The following metrics for Azure File Sync are available in Azure Monitor:

| Metric Name | Description |
|-|-|
| Bytes synced | Size of data transferred (upload and download).<br><br>Unit: Bytes<br>Aggregation Type: Sum<br>Applicable dimensions: Server Endpoint Name, Sync Direction, Sync Group Name |
