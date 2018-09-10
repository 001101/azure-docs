---
title: "Quickstart: Search for images using the Bing Image Search SDK and Python"
titleSuffix: Azure Cognitive Services
description: Use this quickstart to search and find images on the web using the Bing Image Search SDK and Python.
services: cognitive-services
author: aahill
manager: cagronlun
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: article
ms.date: 08/28/2018
ms.author: aahi
---
# Quickstart: Search for images with the Bing Image Search SDK and Python

Use this quickstart to make your first image search using the Bing Image Search SDK, which is a wrapper for the API and contains the same features. This simple Python application sends an image search query, parses the JSON response, and displays the URL of the first image returned.

The source code for this sample is available [on Github](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) with additional error handling and annotations.

## Prerequisites 

* [Python 2.7 or 3.4](https://www.python.org/) and higher.

* The [Azure Image Search SDK](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) for Python
    * Install using `pip install azure-cognitiveservices-search-imagesearch`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## Create and initialize the application

1. Create a new Python script in your favorite IDE or editor, and the following imports:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchAPI
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Create variables for your subscription key and search term.

    ```python
    subscription_key = "Enter your key here"
    search_term = "canadian rockies"
    ```

## Create the image search client

3. Create an instance of `CognitiveServicesCredentials`, and use it to instantiate the client:

    ```python
    client = ImageSearchAPI(CognitiveServicesCredentials(subscription_key))
    ```
4. Send a search query to the Bing Image Search API:
    ```python
    image_results = client.images.search(query=search_term)
    ```
## Process and view the results

Parse the image results returned in the response.


If the response contains search results, store the first result and print out its details, such as a thumbnail URL, the original URL,along with the total number of returned images.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## Next steps

[Cognitive Services Python SDK samples](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)


