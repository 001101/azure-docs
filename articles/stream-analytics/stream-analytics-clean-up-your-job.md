---
title: Clean up your Stream Analytics job
description: This article is a guide for how to delete Azure Stream Analytics jobs.
services: stream-analytics
author: mamccrea
manager: kfile
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2017
---

# Clean up your Stream Analytics job

Azure Stream Analytics jobs can be easily deleted through the Azure portal or Azure PowerShell.

>[!NOTE]
>Microsoft does not own any data processed by Azure Stream Analytics or stored in Azure Storage. Azure Stream Analytics does not store any data once a job is stopped or deleted. If you are required to remove data from Azure, be sure to follow the removal process for the input and output resources of your Stream Analytics job.

## Stop a job in Azure portal

1. Sign in to the Azure portal. 

2. Locate your running Stream Analytics job and select it.

3. On the Stream Analytics job page, select **Stop** to stop the job. 

![Stop Job](./media/stream-analytics-clean-up-your-job/stop-job.png)


## Delete a job in Azure portal

1. Sign in to the Azure portal. 

2. Locate your existing Stream Analytics job and select it.

3. On the Stream Analytics job page, select **Delete** to delete the job. 

![Delete Job](./media/stream-analytics-clean-up-your-job/delete-job.png)


## Stop or delete a job using PowerShell

To stop or delete a job using PowerShell, refer to [Monitor and manage Stream Analytics jobs with Azure PowerShell cmdlets](stream-analytics-monitor-and-manage-jobs-use-powershell).