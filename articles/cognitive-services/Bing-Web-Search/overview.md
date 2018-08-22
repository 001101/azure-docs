---
title: What is Bing Web Search? | Microsoft Docs
description: The Bing Web Search API is a RESTful service that allows your application use Bing's web search capabilities, including web pages, images, videos, news, entities, related search queries, spelling corrections, unit conversions, translations, and calculations.
services: cognitive-services
author: erhopf
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: overview
ms.date: 08/14/2018
ms.author: erhopf
---

# What is Bing Web Search?

The Bing Web Search API is a RESTful service that let's your application use Bing's web search capabilities for instant answers to user queries. Search result are easily configured to include web pages, images, videos, news, entities, related search queries, spelling corrections, unit conversions, translations, and calculations. Results are provided as JSON and based on search relevance and your Bing Search subscriptions.

This API is optimal for applications that need access to all content that is relevant to a user's search query. If you're building an application that requires only a specific type of result, consider using the [Bing Image Search API](../Bing-Image-Search/overview.md), [Bing Video Search API](../Bing-Video-Search/search-the-web.md), or [Bing News Search API](../Bing-News-Search/search-the-web.md). See [Cognitive Services APIs](https://docs.microsoft.com/en-us/azure/cognitive-services#cognitive-services-apis) for a complete list of Bing Search APIs.

Want to see how it works? Try our [Bing Web Search API demo](https://azure.microsoft.com/en-us/services/cognitive-services/bing-web-search-api/).

## Features  

In addition to instant answers, Bing Web Search provides additional features and functionality that allow you to customize search results for your users.

| Feature | Description |
|---------|-------------|
| [Suggest search terms in real time](../bing-autosuggest/get-suggested-search-terms.md) | Improve your app experience by using the Bing Autosuggest API to display suggested search terms as they're typed. |
| [Filter results by content type](filter-answers.md) | Customize and refine search results with filters and query parameters. |
| [Hit highlighting for unicode characters](hit-highlighting.md) | Identify and remove unwanted unicode characters from search results before displaying them to users with  hit highlighting. |
| [Localize search results by country, region, and/or market](supported-countries-markets.md) | Bing Web Search supports more than three dozen countries or regions. Use this feature to refine search results for a specific country/region or market. |
| [Analyze search metrics with Bing Statistics](bing-web-stats.md) | Bing Statistics is a paid subscription that provides analytics on call volume, top query strings, geographic distribution, and more. |

## Workflow

The Bing Web Search API is easy to call from any programming language that can make HTTP requests and parse JSON responses. The service is accessible using the [REST API](quickstarts/python.md) or the [Bing Web Search SDKs](web-sdk-python-quickstart.md).  

1. Create a [Cognitive Services API account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) with access to the Bing Search APIs. If you don't have an Azure subscription, you can [create a free account](https://azure.microsoft.com/en-us/try/cognitive-services/?api=bing-web-search-api).  
2. Send a [request to the Bing Web Search API](quickstarts/python.md).
3. Parse the JSON response.

## Next steps

* Use our [Python quickstart](quickstarts/python.md) to make your first call to the Bing Web Search API.  
* [Build a single-page web app](tutorial-bing-web-search-single-page-app.md).
* Review [Web Search API v7 reference](https://docs.microsoft.com/en-us/rest/api/cognitiveservices/bing-web-api-v7-reference) documentation.  
* Learn more about [use and display requirements](UseAndDisplayRequirements.md) for Bing Web Search.  
