---
title: What is Form Recognizer?
titleSuffix: Azure Cognitive Services
description: The Azure Cognitive Services Form Recognizer allows you to identify and extract key/value pairs and table data from form documents.
author: PatrickFarley
manager: nitinme

ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 07/25/2019
ms.author: pafarley
#Customer intent: As a developer of form-processing software, I want to learn what the Form Recognizer service does so I can determine if I should use it.
---

# What is Form Recognizer?

Azure Form Recognizer is a cognitive service that uses machine learning technology to identify and extract text, key/value pairs and table data from form documents. It then outputs structured data that includes the relationships in the original file. You can call Form Recognizer models by using a REST API to reduce complexity and integrate it into your workflow or application. You can train and run custom models in a local Docker container. You quickly get accurate results that are tailored to your specific content without heavy manual intervention or extensive data science expertise.

Form Recognizer is made up of the following services:
* **Custom models** - Extract key/value pairs and table data from forms. These models are trained to your own data, so they're tailored to your forms.
* **Prebuilt receipt model** - Extracting data from [sales receipts](link to sales receipt image) using a prebuilt model.
* **Layout API** - Extract text and table structures, along with their bounding box coordinates, from form documents.

<!-- add diagram -->

> [!CAUTION]
> As this feature is still in development, API, inputs, and outputs are not final and might change. Preview features are available for testing and experimentation with the goal of gathering feedback. We strongly advise against using preview APIs in production applications.

## Custom models

The Form Recognizer custom model trains to your own data, and you only need five sample input forms to start. When you submit your input data, the algorithm clusters the forms by type, discovers what keys and tables are present, and associates values to keys and entries to tables. It then outputs structured data that includes the relationships in the original file. After you train the model, you can test and retrain it and eventually use it to reliably extract data from more forms according to your needs.

You have the following options when you train custom models: training with labeled data and without labeled data.

### Train without labels

By default, Form Recognizer uses unsupervised learning to understand the layout and relationships between fields and entries in your forms. This doesn't require manual data labeling or intensive coding and maintenance.

### Train with labels

When you train with labeled data, the model does supervised learning to extract values of interest, using the labeled forms you provide. This results in better-performing models and can produce models that work with complex forms or forms containing values without keys.

The Form Recognizer labelled data feature uses the document [Layout API](#layout-api) to learn the expected sizes and positions of printed and handwritten text elements. Then it uses specially-formatted JSON documents that reflect the labels you have manually applied to different fields. We recommend that you use 10 manually labeled forms of the same type when training a new model.

## Prebuilt receipt model

Form Recognizer also includes a model for reading sales thermal receipts. This model extracts key information such as the time and date of the transaction, merchant information, amounts of taxes, line items, totals, and more. In addition, the prebuilt receipt model is trained to recognize and return all of the text on a receipt.

<!-- image of receipt -->

## Layout API

Form Recognizer can also extract text using high-definition optical character recognition (OCR). It also extracts the structures of tables (row and column numbers).

## Where do I start?

**Step 1:** Request access:

Form Recognizer is available in a limited-access preview. To get access to the preview, fill out and submit the [Form Recognizer access request](https://aka.ms/FormRecognizerRequestAccess) form. The form requests information about you, your company, and the scenario in which you'll use Form Recognizer.

**Step 2:** Create a Form Recognizer resource in the Azure portal:

When you're granted access to use Form Recognizer, you'll receive a Welcome email with several links and resources. Use the "Azure portal" link in that message to open the Azure portal and create a Form Recognizer resource.

**Step 3:** Extract data from your forms:
* Custom - train a model to your forms
    * Train without labels
           * [Quickstart: Train without labels a Form Recognizer model and extract form data by using the REST API with cURL](quickstarts/curl-train-extract.md)
           * [Quickstart: Train without labels a Form Recognizer model and extract form data by using the REST API with Python](quickstarts/python-train-extract.md)
    * Train with labels 
           * [Quickstart: Train with labels a Form Recognizer model and extract form data by using the sample labeling tool](quickstarts/label.md)
           * [Quickstart: Train with labels a Form Recognizer model and extract form data by using the REST API with Python](quickstarts/python-train-with-labels-extract.md)
           * [Quickstart: Train with labels a Form Recognizer model and extract form data by using the REST API with cURL](quickstarts/curl-train-wiht-labels-extract.md)   
* Prebuilt receipts - extract data from US thermal receipts
  * [Quickstart: Extract receipt data using cURL](quickstarts/curl-receipts.md)
  * [Quickstart: Extract receipt data using Python](quickstarts/python-receipts.md)
* Layout - extract text and table structure from forms
  * [Quickstart: Extract layout data using cURL](quickstarts/curl-layout.md)
  * [Quickstart: Extract layout data using Python](quickstarts/python-layout.md)

We recommend that you use the free service when you're learning the technology. Remember that the number of free pages is limited to 500 per month.

**Step 4:** Review the REST APIs:

You'll use the following APIs to train models and extract structured data from forms.

|Name |Description |
|---|---|
| **Train Custom Model**| Train a new model to analyze your forms by using five forms of the same type. Use the 'useLabelFile' parameter to train with our without labels |
| **Analyze Form** |Analyze a single document passed in as a stream to extract text, key/value pairs and tables from the form with your custom model.  |
| **Analyze Receipt** |Analyze a single receipt document to extract key information and other receipt text.|
| **Analyze Layout** |Analyze the layout of a form to extract text and table strcutre.|

Explore the [REST API reference documentation](https://aka.ms/form-recognizer/api) to learn more. If you're familiar with a previous version of the API, see the [What's new](./whats-new.md) article to learn about recent changes.

## Input requirements
### Custom model

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### Prebuilt receipt model

The input requirements for the receipt model are slightly different.

* Format must be JPEG, PNG, BMP, PDF (text or scanned) or TIFF.
* File size must be less than 20 MB.
* Image dimensions must be between 50 x 50 pixels and 10000 x 10000 pixels. 
* PDF dimensions must be at most 17 x 17 inches, corresponding to Legal or A3 paper sizes and smaller.
* For PDF and TIFF, only the first 200 pages are processed (with a free tier subscription, only the first two pages are processed).

## Data privacy and security

This service is offered as a [preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) of an Azure service under the [Online Service Terms](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). As with all the cognitive services, developers using the Form Recognizer service should be aware of Microsoft policies on customer data. See the [Cognitive Services page](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) on the Microsoft Trust Center to learn more.

## Next steps

Complete a [quickstart](quickstarts/curl-train-extract.md) to get started with the [Form Recognizer APIs](https://aka.ms/form-recognizer/api).
