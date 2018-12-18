---
title: "Quickstart: Get image insights using the Bing Visual Search REST API and Python"
titleSuffix: Azure Cognitive Services
description: Learn how to upload an image to the Bing Visual Search API and get insights about it.
services: cognitive-services
author: swhite-msft
manager: cgronlun

ms.service: cognitive-services
ms.component: bing-visual-search
ms.topic: quickstart
ms.date: 5/16/2018
ms.author: scottwhi
---

# Quickstart: Your first Bing Visual Search query in Python

Use this quickstart to make your first call to the Bing Visual Search API and view the search results. This simple JavaScript application uploads an image to the API, and displays the information returned about it. While this application is written in JavaScript, the API is a RESTful Web service compatible with most programming languages.

When uploading a local image, the POST form data must include the Content-Disposition header. Its `name` parameter must be set to "image" and the `filename` parameter may be set to any string. The contents of the form is the binary of the image. The maximum image size you may upload is 1 MB.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

## Prerequisites

* [Python 3.x](https://www.python.org/)

For this quickstart, you will need to start a subscription at S9 price tier as shown in [Cognitive Services Pricing - Bing Search API](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/search-api/). 

To start a subscription in Azure portal:
1. Enter 'BingSearchV7' in the text box at the top of the Azure portal that says `Search resources, services, and docs`.  
2. Under Marketplace in the drop-down list, select `Bing Search v7`.
3. Enter `Name` for the new resource.
4. Select `Pay-As-You-Go` subscription.
5. Select `S9` pricing tier.
6. Click `Enable` to start the subscription.

## Initialize the application

1. Create a new Python file in your favorite IDE or editor, and add the following import statement.

    ```python
    import requests, json
    ```

2. Create variables for your subscription key, endpoint, and the path to the image you're uploading.

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```

3. Create a dictionary object to hold your requests' header information. Bind your subscription key to the string `Ocp-Apim-Subscription-Key`, as shown below.

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Create another dictionary to contain your image, which will be opened and uploaded when you send the request. 

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## Parse the JSON response

1. Create a method called `print_json()` to take in the API response, and print the JSON.

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## Send the request

1. Use `requests.post()` to send a request to the Bing Visual Search API. Include the string for your endpoint, header, and file information. Print `response.json()` with `print_json()`

    ```python
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())
    
    except Exception as ex:
        raise ex
    ```

## Next steps

[Get insights about an image using an insights token](../use-insights-token.md)  
[Bing Visual Search image upload tutorial](../tutorial-visual-search-image-upload.md)
[Bing Visual Search single-page app tutorial](../tutorial-bing-visual-search-single-page-app.md)  
[Bing Visual Search overview](../overview.md)  
[Try it](https://aka.ms/bingvisualsearchtryforfree)  
[Get a free trial access key](https://azure.microsoft.com/try/cognitive-services/?api=bing-visual-search-api)  
[Bing Visual Search API reference](https://aka.ms/bingvisualsearchreferencedoc)
