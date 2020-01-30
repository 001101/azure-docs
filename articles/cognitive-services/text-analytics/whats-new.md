---
title: What's new in the Text Analytics API
titleSuffix: Text Analytics - Azure Cognitive Services
description: This article provides you with information about new releases and features for the Azure Cognitive Services Text Analytics.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: aahi
---

# What's new in the Text Analytics API?

The Text Analytics API is updated on an ongoing basis. To stay up-to-date with recent developments, this article provides you with information about new releases and features.

## Named Entity Recognition v3 public preview - February 2020

Additional entity types are now available in the Named Entity Recognition (NER) v3 public preview service as we expand the detection of general and PII entities found in text. It includes:

* Recognition of the following general entity types (English only):
    * PersonType
    * Product
    * Event
    * Geopolitical Entity (GPE) as a subtype under Location
    * Skill

* Recognition of the following personal information entity types (English only):
    * Person
    * Organization
    * Age as a subtype under Quantity
    * Date as a subtype under DateTime
    * Email 
    * Phone Number (US only)
    * URL
    * IP Address

Access them using the [endpoints for NER v3](how-tos/text-analytics-how-to-entity-linking.md?tabs=version-3#sending-a-rest-api-request).

Entity linking supports English and Spanish. NER language support varies by the entity type. 

> [!div class="nextstepaction"]
> [Learn more about Named Entity Recognition v3](how-tos/text-analytics-how-to-entity-linking.md#named-entity-recognition-versions-and-features)

## Sentiment Analysis v3 public preview - October 2019

The [next version of Sentiment Analysis](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0-Preview-1/operations/Sentiment) is now available for public preview, and provides significant improvements in the accuracy and detail of the API's text categorization and scoring. It additionally provides:

* Automatic labeling for different sentiments in text.
* Sentiment analysis and output on a document and sentence level. 

It supports English (`en`), Japanese (`ja`), Chinese Simplified (`zh-Hans`),  Chinese Traditional (`zh-Hant`), French (`fr`), Italian (`it`), Spanish (`es`), Dutch (`nl`), Portuguese (`pt`), and German (`de`), and is available in the following regions: `Australia East`, `Central Canada`, `Central US`, `East Asia`, `East US`, `East US 2`, `North Europe`, `Southeast Asia`, `South Central US`, `UK South`, `West Europe`, and `West US 2`. 

> [!div class="nextstepaction"]
> [Learn more about Sentiment Analysis v3](how-tos/text-analytics-how-to-sentiment-analysis.md#sentiment-analysis-versions-and-features)

## Next steps

* [What is the Text Analytics API?](overview.md)  
* [Example user scenarios](text-analytics-user-scenarios.md)
* [Sentiment analysis](how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Language detection](how-tos/text-analytics-how-to-language-detection.md)
* [Entity recognition](how-tos/text-analytics-how-to-entity-linking.md)
* [Key phrase extraction](how-tos/text-analytics-how-to-keyword-extraction.md)
