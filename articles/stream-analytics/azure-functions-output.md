---
title: Azure Functions output from Azure Stream Analytics
description: This article describes data output options available in Azure Stream Analytics, including Power BI for analysis results.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 07/15/2020
---

## Azure Functions output from Azure Stream Analytics
Azure Functions is a serverless compute service that you can use to run code on-demand without having to explicitly provision or manage infrastructure. It lets you implement code that's triggered by events occurring in Azure or partner services. This ability of Azure Functions to respond to triggers makes it a natural output for Azure Stream Analytics. This output adapter enables users to connect Stream Analytics to Azure Functions, and run a script or piece of code in response to a variety of events.

Azure Functions output from Stream Analytics is currently not available in the Azure China 21Vianet and Azure Germany (T-Systems International) regions.

Azure Stream Analytics invokes Azure Functions via HTTP triggers. The Azure Functions output adapter is available with the following configurable properties:

| Property name | Description |
| --- | --- |
| Function app |The name of your Azure Functions app. |
| Function |The name of the function in your Azure Functions app. |
| Key |If you want to use an Azure Function from another subscription, you can do so by providing the key to access your function. |
| Max batch size |A property that lets you set the maximum size for each output batch that's sent to your Azure function. The input unit is in bytes. By default, this value is 262,144 bytes (256 KB). |
| Max batch count  |A property that lets you specify the maximum number of events in each batch that's sent to Azure Functions. The default value is 100. |

Azure Stream Analytics expects HTTP status 200 from the Functions app for batches that were processed successfully.

When Azure Stream Analytics receives a 413 ("http Request Entity Too Large") exception from an Azure function, it reduces the size of the batches that it sends to Azure Functions. In your Azure function code, use this exception to make sure that Azure Stream Analytics doesn't send oversized batches. Also, make sure that the maximum batch count and size values used in the function are consistent with the values entered in the Stream Analytics portal.

> [!NOTE]
> During test connection, Stream Analytics sends an empty batch to Azure Functions to test if the connection between the two works. Make sure that your Functions app handles empty batch requests to make sure test connection passes.

Also, in a situation where there's no event landing in a time window, no output is generated. As a result, the **computeResult** function isn't called. This behavior is consistent with the built-in windowed aggregate functions.

## Partitioning
| Azure Functions | Yes | Based on the PARTITION BY clause in the query. | Follows the input partitioning for [fully parallelized queries](stream-analytics-scale-jobs.md). |
## Output batch size
| Azure Functions    | | The default batch size is 262,144 bytes (256 KB). <br /> The default event count per batch is 100. <br /> The batch size is configurable and can be increased or decreased in the Stream Analytics [output options](#azure-functions).