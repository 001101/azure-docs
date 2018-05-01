--- 
title: Anomaly Finder API cURL Quick Starts | Microsoft Docs
description: Get information to help you quickly get started using cURL and the Anomaly Finder API in Cognitive Services.
services: cognitive-services
author: chliang
manager: bix

ms.service: cognitive-services
ms.technology: anomaly-detection
ms.topic: article
ms.date: 04/20/2018
ms.author: chliang
---

# Anomaly Finder cURL Quick Starts

This article provides information and code samples to help you quickly get started using the Anomaly Finder API with cURL to accomplish task of getting anomaly result of time series data.

## Prerequisites

[!INCLUDE [GetSubscriptionKey](../get-subscription-key.md)]

## Getting anomaly points with Anomaly Finder API using cURL 
[!INCLUDE [DataContract](../datacontract.md)]

### Example of time series data
The example of the time series data points is as follows,
[!INCLUDE [Request](../request.md)]

### Analyze data and get anomaly points cURL example

The steps of using the example,
1. Replace the `[YOUR_SUBSCRIPTION_KEY]` value with your valid subscription key.
2. Replace the `[YOUR_REGION]` to use the location where you obtained your subscription keys.
3. Replace the `[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]` with the example or your own data points.
4. Execute and check the response.

```cURL

curl -v -X POST "https://labsportalppe.azure-api.net/anomalyfinder/v1.0/anomalydetection"
-H "Content-Type: application/json"
-H "Ocp-Apim-Subscription-Key: [YOUR_SUBSCRIPTION_KEY]"
--data-ascii "[REPLACE_WITH_THE_EXAMPLE_OR_YOUR_OWN_DATA_POINTS]" 

```

### Example response
A successful response is returned in JSON. Example response is as follows:
[!INCLUDE [Response](../response.md)]