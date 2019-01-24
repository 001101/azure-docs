---
title: What are the Bing Search APIs?
titleSuffix: Azure Cognitive Services
description: Use this article to learn about the Bing Search APIs, and how you can enable cognitive internet searches in your apps and services.  
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 1/24/2019
ms.author: aahi
---

# What are the Bing Search APIs?

The Bing Search APIs enable you to quickly build web-connected apps and services that can find webpages, images, news, locations, and more without advertisements. By sending search requests to the Bing search REST APIs or SDKs, you can get instant, relevant answers to web searches in a variety of content types. Use this article to learn about the various Bing search APIs and how you can integrate cognitive searches into your applications and services. Note that pricing and rate limits may vary between APIs.

## The Bing Web Search API

The [Bing Web Search API](../Bing-Web-Search/index.yml) can return webpages in addition to images, video, news and more. The search queries sent to this API can be filtered to include or exclude certain content types.

Consider using the Bing Web Search API in applications that may need to search for all types of relevant web content. If your application searches for a specific type of online content, consider one of the search APIs below: 

## Content-specific Bing search APIs

The following Bing search APIs return specific content from the web such as images, news, local businesses and videos.

| Bing API | Description |
| -- | -- | 
| [Entity Search](../Bing-Entities-Search/index.yml) | The Bing Entity Search API returns search results containing entities which can be people, places, or things. Depending on the search query sent to the API, it will return one or more entities that satisfy the search query, which can include noteworthy individuals, local businesses, landmarks, destinations, and more. |
| [Image Search](../Bing-Image-Search/index.yml) | The Bing Image Search API enables you to search for and find high-quality static and animated images similar to [Bing.com/images](https://www.Bing.com/images). You can refine searches to include or exclude images by attribute, including size, color, license, and freshness. You can also search for trending images, upload images to gain insights about them, and display thumbnail previews. |
| [News Search](../Bing-News-Search/index.yml) | The Bing News Search API enables you to find news stories similar to [Bing.com/news](https://www.Bing.com/news). The API can return news articles from either multiple sources or specific domains. You can search across categories to get trending articles, top stories, and headlines.
| [Video Search](../Bing-Video-Search/index.yml) | The Bing Video Search API enables you to find videos across the web. Get trending videos, related content, and thumbnail previews. |
| [Visual Search](../Bing-visual-search/index.yml) | Upload an image or use a URL to get insightful information about it, like visually similar products, images, and related searches. |
 [Local Business Search](../bing-local-business-search/index.yml) | The Bing Local Business Search API enables your applications to find contact and location information about local businesses based on search queries. |

## The Bing Custom Search API

By creating a custom search instance with the [Bing Custom Search](../Bing-Custom-Search/index.yml) API, you can create a search experience focused only on content and topics you care about. For example, after specifying the domains, websites, and specific webpages that Bing will search, your users will see results tailored to that specific content. You can incorporate the Bing Custom Autosuggest, Image, and Video Search APIs to further customize your search experience.  

## Additional Bing Search APIs

The following Bing Search APIs enable you to improve your search experience by combining them with other Bing search APIs.

| API | Description |
| -- | -- | 
| [Bing Autosuggest](../Bing-Autosuggest/index.yml) | The Bing Autosuggest API can improve your application's search experience by returning suggested searches in real time.  |
| [Bing Statistics](https://docs.microsoft.com/en-us/azure/cognitive-services/bing-news-search/bing-news-stats) | Bing Statistics provides analytics for the Bing Search APIs your application uses. Some of the available analytics include call volume, top query strings, and geographic distribution. |

## See also

* Bing Search API [pricing details](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* The [Bing Use and Display Requirements](./useanddisplayrequirements.md) specify acceptable uses of the content and information gained through the Bing search APIs.
