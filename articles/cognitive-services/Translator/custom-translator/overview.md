---
title: What is Custom Translator?
titleSuffix: Azure Cognitive Services
description: Custom Translator offers similar capabilities to what Microsoft Translator Hub does for Statistical Machine Translation (SMT), but exclusively for Neural Machine Translation (NMT) systems.  
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: overview
#Customer intent: As a custom translator user, I want to understand what is Custom Translator, so that I can start using it.
---

# What is Custom Translator?

[Custom Translator](https://portal.customtranslator.azure.ai) is a feature of the Microsoft Translator service, which enables Translator enterprises, app developers, and language service providers to build neural machine translation (NMT). These are the systems that understand the terminology used in their own business and industry. The customized translation system seamlessly integrates into existing applications, workflows, and websites.

Microsoft Translator released [Neural Machine Translation (NMT)](https://blogs.msdn.microsoft.com/translation/2016/11/15/microsoft-translator-launching-neural-network-based-translations-for-all-its-speech-languages/) in 2016. NMT provided major advances in translation quality over the then industry-standard [Statistical Machine Translation (SMT)](https://en.wikipedia.org/wiki/Statistical_machine_translation) technology. Because NMT better captures the context of full sentences before translating them, it provides higher quality, more human-sounding, and more fluent translations. [Custom Translator](https://portal.customtranslator.azure.ai/) offers similar capabilities to what [Microsoft Translator Hub](https://hub.microsofttranslator.com/) does for Statistical Machine Translation (SMT), but exclusively for Neural Machine Translation (NMT) systems.

The supported languages are limited to the language pairs where NMT languages exist and the list of NMT languages available today can be found on the [Microsoft Translator Languages webpage](https://www.microsoft.com/translator/business/languages/).

## Features

Custom Translator provides different features to build custom translation system and subsequently access it.

|Feature  |Description  |
|---------|---------|
|[Create a project](how-to-create-project.md)     |  Create your project for required language pair. Your project contains all your custom models.       |
|[Upload documents](how-to-upload-document.md)     |  Upload your parallel documents and dictionaries to train your custom model.       |
|[Create model](how-to-train-model.md)     |   Train and build your custom models with the uploaded documents.       |
|[Deploy model](how-to-view-system-test-results.md#deploy-a-model)     |   Deploy custom models and access via Microsoft Translator [Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl).     |
|[Share your work](how-to-manage-settings.md#share-your-workspace)     |  Share your works with different people when there is a need.       |


## Get better translations

You can use previously translated documents to build a translation system. These documents include domain-specific terminology and style, better than a generic translation system. Users can upload ALIGN, PDF, LCL, HTML, HTM, XLF, TMX, XLIFF, TXT, DOCX, and XLSX documents.

Custom Translator also accepts data that's parallel at the document level to make data collection and preparation more effective. If users have access to versions of the same content in multiple languages but in separate documents, Custom Translator will be able to automatically match sentences across documents.

If the appropriate type and amount of training data is supplied, it's not uncommon to see [BLEU score](what-is-bleu-score.md) gains between 5 and 10 points by using Custom Translator.

## Be productive and cost effective

With [Custom Translator](https://portal.customtranslator.azure.ai), training and deploying a custom system doesn't require any programming skills. 

Using the secure [Custom Translator](https://portal.customtranslator.azure.ai) portal, users can upload training data, train systems, test systems, and deploy them to a production environment through an intuitive user interface. The system will then be available for use at scale within a few hours (actual time depends on training data size).

[Custom Translator](https://portal.customtranslator.azure.ai) can also be programmatically accessed through a [dedicated API](https://custom-api.cognitive.microsofttranslator.com/swagger/) (currently in preview). The API allows users to manage creating or updating training on a regular basis through their own app or webservice. 

The cost of using a custom model to translate content is based on the user’s Translator Text API pricing tier.

-   The cost of training a custom system is calculated at the rate of one character per character of training material in the source text.

-   The cost of hosting a deployed system is 1M characters per month per deployed system.

-   The cost of using a deployed customized translation system is the same as
    the uncustomized system, which is based on the user’s Translator
    Text API pricing tier. 

See the Cognitive Services [Translator Text API pricing webpage](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
for pricing tier details. 

## Securely translate anytime, anywhere on all your apps and services

Translation systems built with [Custom Translator](https://portal.customtranslator.azure.ai) are available through the same cloud-based, [secure](https://cognitive.uservoice.com/knowledgebase/articles/1147537-api-and-customization-confidentiality), high performance, highly scalable Microsoft Translator [Text API V3](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl), that powers billions of translations  every day. Custom systems can be seamlessly accessed and integrated into any product or business workflow, and on any device, via the Microsoft Translator Text API through standard REST technology.


## Next steps

- Read about [pricing details](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/).

- With [Quickstart](quickstart-build-deploy-custom-model.md) learn to build a translation model in Custom Translator.
