---
title: Migrating to the Azure Cognitive Services Speech Service
titleSuffix: Azure Cognitive Services
description: Use this topic to migrate your applications to the Azure Cognitive Services Speech Service
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 09/12/2018
ms.author: aahi
---

# Migrating to the Azure Cognitive Services Speech API

Starting in TBD, The Microsoft Translator Speech API will be replaced by the [Cognitive Services Speech Service](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/). 
After the Speech Service is released, you will have one year to migrate your applications to it before all Translator API subscriptions expire. This guide outlines the differences between the Translator Speech API and Speech Service, and suggest strategies for migrating your applications. 
Note that Your Translator Speech API subscription key won't be accepted by the Speech Service, and that you will need a new subscription.


## Comparison of features

| Feature                                           | Cognitive Services Speech API                                   | Translator Speech API (deprecated) | Details                                                                                                                                                                                                                                                                            |  |
|---------------------------------------------------|-----------------------------------------------------------------|------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--|
| Translation to text                               | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |  |
| Translation to speech                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |  |
| Global endpoint                                   | :heavy_minus_sign:                                              | :heavy_check_mark:                 | The Speech Service does not offer a global endpoint, but is in consideration for a future release. A global endpoint can automatically direct traffic to the nearest regional endpoint, decreasing latency in your application.                                                    |  |
| Regional endpoints                                | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |  |
| Connection time limit                             | Unlimited with the SDK. 10 Minutes with a websocket connection* | 90 Minutes                         |                                                                                                                                                                                                                                                                                    |  |
| Auth key in header                                | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |  |
| Multiple languages translated in a single request | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |  |
| SDKs available                                    | :heavy_check_mark:                                              | :heavy_minus_sign:                 | See the [Speech Service documentation](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) for available SDKs.                                                                                                                                                    |  |
| Websocket Connections                             | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |  |
| Languages API                                     | :heavy_minus_sign:                                              | :heavy_check_mark:                 | A Speech Service Languages API is under consideration for a future release. The Speech Service supports the same languages described in the [Translator API languages reference](https://docs.microsoft.com/azure/cognitive-services/translator-speech/languages-reference) topic. |  |
| Profanity Filter and Marker                       | :heavy_check_mark:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |  |
| .WAV/PCM as input                                 | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |  |
| Other file types as input                         | :heavy_minus_sign:                                              | :heavy_minus_sign:                 |                                                                                                                                                                                                                                                                                    |  |
| Partial Results                                   | :heavy_check_mark:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |  |
| Timing Info                                       | :heavy_minus_sign:                                              | :heavy_check_mark:                 | This feature is not currently available for the Speech Service, but is under consideration for a future release.                                                                                                                                                                   |  |
| Correlation ID                                    | :heavy_minus_sign:                                              | :heavy_check_mark:                 |                                                                                                                                                                                                                                                                                    |  |
| Custom Speech Models                              | :heavy_check_mark:                                              | :heavy_minus_sign:                 | The Speech Service offers custom speech models that enable you to customize speech recognition to your organization’s unique vocabulary.                                                                                                                                           |  |
| Custom Translation Models                         | :heavy_check_mark:                                              | :heavy_minus_sign:                 | Subscribing to the Microsoft Text Translation API enables you to use [Custom Translator](https://www.microsoft.com/translator/business/customization/) (currently in preview) to use your own data for more accurate translations.                                                 |  |

## Migration strategies

If you or your organization have an application in development that uses the Translator API, you should update it to use the Speech Service as soon as possible.  
See the [Speech API](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) documentation for available SDKs, code samples, and tutorials.
Applications already in production must be migrated to use the Speech Service. Below are some things to consider when migrating:

* The new Speech API does not currently offer a global endpoint. You will need to determine if your application will function efficiently using a single regional endpoint for all of it's traffic. If it won't, you will need to use geolocation to determine the most efficient endpoint.

* If your application uses long-lived connections and can't use the available SDKs, you can use a websocket connection and manage the 10-minute timeout limit by reconnecting at the appropriate times.

* As a Language API, global endpoint, and additional SDKs are considered for future releases, you should check the [Speech Service documentation](https://docs.microsoft.com/azure/cognitive-services/Speech-Service/) often for updates.

* If your application uses the Translator Text API and Translator Speech API to enable custom translation models, you will be able to add ‘Category’ IDs directly using the Speech Service.

* The Speech Service can complete translations into multiple languages in a single request.


