---
title: Learn about QnAMaker | Microsoft Docs
titleSuffix: Azure
description: Learn about QnAMaker and how it works.
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
---
# Learn about QnAMaker

[QnAMaker](https://qnamaker.ai) enables you to power a question and answer service from your semi-structured content. 

One of the basic requirements in writing your own Bot service is to seed it with questions and answers. In many cases, the questions and answers already exist in content like FAQ URLs/documents, product manuals, etc. With QnAMaker, users can query your application in a natural, conversational manner. QnAMaker uses machine learning to extract relevant question-answer pairs from your content. It also uses powerful matching and ranking algorithms to provide the best possible match between the user query and the questions.

The easy-to-use graphical user interface enables you to create, manage, train and use your service without any developer experience. 

![Overview](../media/qnamaker-overview-learnabout/overview.png)

## Highlights
- A complete **no-code** experience to [create a FAQ bot](https://aka.ms/qnamaker-docs-create-faqbot).
- **No more throttling**. Pay for hosting the service and not for the number of transactions. See the [pricing page](https://aka.ms/qnamaker-docs-pricing) for more details.
- **Scale as per your needs**. Choose the appropriate SKUs of the individual components that suit your scenario. See how to [choose capacity](https://aka.ms/qnamaker-docs-capacity) for your QnAMaker service.
- **Full data compliance**. The data and runtime components are deployed in the user's Azure subscription and within their compliance boundary. Read below for more details.

## Key QnAMaker processes
A QnAMaker provides two key services for your data:
* **Extraction**: Structured question-answer data is extracted from semi-structured data sources such as FAQs and product manuals. This extraction is done when creating the knowledge base. See [here](https://aka.ms/qnamaker-docs-createkb) to learn how to create your knowledge base.
* **Matching**: Once your knowledge base has been [trained and tested](https://aka.ms/qnamaker-docs-trainkb), you [publish](https://aka.ms/qnamaker-docs-publishkb) it. This enables an endpoint to your QnAMaker knowledge base, which you can then use in your bot or app. This endpoint accepts a user question and responds with the best question/answer match in the knowledge base, along with a confidence score for the match.

## QnAMaker architecture
The QnAMaker stack consists of the following parts:

1. **QnAMaker management services (control plane)**: The management experience for a QnAMaker knowledge base, which includes creation, update, training, and publishing. These activities can be done through the [portal](https://qnamaker.ai) or the [management APIs](https://aka.ms/qnamaker-v4-apis). The management services talk to the runtime component below.

2. **QnAMaker runtime (data plane)**: The data and runtime are deployed in the user's Azure subscription in a region of their choosing. Customer question/answer content is stored in [Azure Search](https://azure.microsoft.com/en-us/services/search/), and the runtime is deployed as as [App service](https://azure.microsoft.com/en-us/services/app-service/). Optionally, you can also choose to deploy an [Application insights](https://azure.microsoft.com/en-us/services/application-insights/) resource for analytics.

![Architecture](../media/qnamaker-overview-learnabout/architecture.png)
