---
title: Call the Text Analytics API (Microsoft Cognitive Services on Azure) | Microsoft Docs
description: Learn how to call the Text Analytics REST API.
services: cognitive-services
author: HeidiSteen
manager: jhubbard

ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: get-started-article
ms.date: 08/24/2017
ms.author: heidist
---

# How to call Text Analytics REST API

Calls to the **Text Analytics API** are HTTP POST/GET calls, which you can formulate in any language. In this article, we use REST and [Postman](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) to demonstrate key concepts.

Each request must include your access key and an HTTP endpoint. The endpoint specifies the region you chose during sign up, the service URL, and a resource used on the request: `sentiment`, `keyphrases`, `languages`. 

Recall that Text Analytics is stateless so there are no data assets to manage. Your text is uploaded, analyzed upon receipt, and results are returned immediately to the calling application.

> [!Tip]
> For one-off calls to see how the API works, you can send POST requests from the built-in **API testing console** available on any [API doc page](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6). There is no setup, and user requirements consist only of pasting an access key and the JSON documents into the request. 

## Prerequisites

You must have a [Cognitive Services API account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) and [sign up for the Text Analytics API](text-analytics-howto-signup.md). 

An [access key](text-analytics-howto-accesskey.md) is required on every request. The key is created for you during sign up. 

<a name="json-schema"></a>

## JSON schema requirements

Input rows must be JSON in raw unstructured text. XML is not supported. The schema is simple, consisting of the elements described in the following list. 

Although this could change in the future, you can currently use the same documents for all three operations: sentiment, key phrase, and language detection.

+ `id` is required. The data type is string, but in practice document IDs tend to be integers. The system uses the IDs you provide to structure the output. Language codes, keywords, and sentiment scores are generated for each ID.

+ `text` is required and contains unstructured raw text, up to 10 KB. For more information about limits, see [Text Analytics Overview > Data limits](overview.md#data-limits). 

+ `language` is used only in sentiment analysis and key phrase extraction. It is ignored in language detection. It is a 2-character [ISO 639-1](https://en.wikipedia.org/wiki/List_of_ISO_639-1_codes) value. For a list of supported languages, see [Text Analytics overview](overview.md#supported-languages).

## Set up a request in Postman

If you are using Postman or another Web API test tool, set up the endpoint to the resource you want to use, and provide the access key in a request header. Each operation requires that you append the appropriate resource to the endpoint. 

1. In Postman:

   + Choose **Post** as the request type.
   + Paste in the endpoint you copied from the portal page.
   + Append a resource.

  Endpoints for each available resource are as follows (your region may vary):

   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases`
   + `https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages`

2. Set three request headers:

   + `Ocp-Apim-Subscription-Key` set to your access key, obtained from Azure portal.
   + `Content-Type` set to application/json.
   + `Accept` set to application/json.

  Your request should look similar to the following screenshot, assuming a **/keyPhrases** resource.

   ![Request screenshot with endpoint and headers](../media/text-analytics/postman-request-keyphrase-1.png)

4. Click **Body** and choose **raw** for the format.

   ![Request screenshot with body settings](../media/text-analytics/postman-request-body-raw.png)

5. Paste in some JSON documents in a format that is valid for the intended analysis:

  + [Language detection](text-analytics-howto-language-detection.md)  
  + [Key phrase extraction](text-analytics-howto-keyword-extraction.md)  
  + [Sentiment analysis](text-analytics-howto-sentiment-analysis.md)  

  Content must include a **id** and **text** for each document. For sentiment analysis and key phrase extraction, an additional 2-character **language** should be provided (such as `en` for English, `es` for Spanish, and so forth).

## Next steps

+ [Quickstart](quick-start.md) is a walk through of the REST API calls written in C#. Learn how to submit text, choose an analysis, and view results with minimal code.

+ [API reference documentation](//go.microsoft.com/fwlink/?LinkID=759346) provides the technical documentation for the APIs. Documentation embeds interactive requests so that you can call the API from each documentation page.

+ To see how the Text Analytics API can be used as part of a bot, see the [Emotional Bot](http://docs.botframework.com/bot-intelligence/language/#example-emotional-bot) example on the Bot Framework site.

+ [External & Community Content](text-analytics-resource-external-community.md) provides a list of blog posts and videos demonstrating how to use Text Analytics with other tools and technologies.

## See also 

 [Text Analytics Overview](overview.md)  
 [Frequently asked questions (FAQ)](text-analytics-resource-faq.md)