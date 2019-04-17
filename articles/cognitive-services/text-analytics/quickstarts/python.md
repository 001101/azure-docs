---
title: 'Quickstart: Using Python to call the Text Analytics API'
titleSuffix: Azure Cognitive Services
description: Get information and code samples to help you quickly get started using the Text Analytics API in Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme

ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 03/28/2019
ms.author: aahi
---

# Quickstart: Call the Text Analytics Service using the Python SDK 
<a name="HOLTop"></a>

Use this quickstart to begin analyzing language with the Text Analytics SDK for Python. [Text Analytics](//go.microsoft.com/fwlink/?LinkID=759711) REST API is compatible with most programming languages, the SDK provides an easy way to integrate the service into your applications.

Refer to the [API definitions](//go.microsoft.com/fwlink/?LinkID=759346) for technical documentation for the APIs.

You can run this example from the command line or as a Jupyter notebook on [MyBinder](https://mybinder.org) by clicking on the launch Binder badge:

[![Binder](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=TextAnalytics.ipynb)


## Prerequisites

[!INCLUDE [cognitive-services-text-analytics-signup-requirements](../../../../includes/cognitive-services-text-analytics-signup-requirements.md)]

You must also have the [endpoint and access key](../How-tos/text-analytics-how-to-access-key.md) that was generated for you during sign-up.

> [!Tip]
>  While you could call the [HTTP endpoints](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v2-1/operations/56f30ceeeda5650db055a3c9) directly from C#, the Microsoft.Azure.CognitiveServices.Language SDK makes it much easier to call the service without having to worry about serializing and deserializing JSON.

### Command Line 

You may need to update [IPython](https://ipython.org/install.html), the kernel for Jupyter:
```bash
pip install --upgrade IPython
```

## Authenticate your credentials

1. Add the following import statements to your script:

```
ADD CODE
```

2. Create a variable for your Text Analytics subscription key.

```
ADD CODE FOR REPLACING SUB KEY 
```

## Create a Text Analytics client

```
Code for creating client
```

> [!Tip]
> For secure deployment of secrets in production systems we recommend using [Azure Key Vault](https://docs.microsoft.com/en-us/azure/key-vault/quick-create-net)
>


## Sentiment analysis

## Language detection

1. Create a list of dictionaries, containing the documents you want to analyze. Using the client created earlier call `detect_language()`

``` 
CODE FOR REQUEST
``` 

## Entity recognition

## Key phrase extraction

## Next steps

> [!div class="nextstepaction"]
> [Text Analytics With Power BI](../tutorials/tutorial-power-bi-key-phrases.md)

## See also

 [Text Analytics overview](../overview.md)
 [Frequently asked questions (FAQ)](../text-analytics-resource-faq.md)
