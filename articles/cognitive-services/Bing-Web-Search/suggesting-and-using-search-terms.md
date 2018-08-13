---
title: Suggesting and using search terms | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Bing Web Search SDK for applications that search the web.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.assetid:
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 8/13/2018
ms.author: erhopf
---

# Suggesting and using search terms  

If you provide a search box where the user enters their search term, use the [Bing Autosuggest API](../bing-autosuggest/get-suggested-search-terms.md) to improve the experience. The API returns suggested query strings based on partial search terms as the user types.

After the user enters their search term, URL encode it before setting the [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query) query parameter. For example, if the user enters *sailing dinghies*, set `q` to `sailing+dinghies` or `sailing%20dinghies`.

If the query term contains a spelling mistake, the search response includes a [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#querycontext) object. The object shows the original spelling and the corrected spelling that Bing used for the search.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

You can use this information to let the user know that you modified their query string when you display the search results.

![Query context UX example](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## Next Steps  

* Review [Search responses]()  

## See also  
