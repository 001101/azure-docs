---
title: What is QnA Maker service?
titleSuffix: Azure Cognitive Services
description: QnA Maker is a cloud-based NLP service that easily creates a natural conversational layer over your data. It can be used to find the most appropriate answer for any given natural language input, from your custom knowledge base (KB) of information.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 08/01/2019
ms.author: diberry
#customer intent: As a developer, I want to know how to use QnA Maker for my FAQs and product manuals so that I can enable conversational question and answer sessions for my customers.
---

# What is the QnA Maker service?

QnA Maker is a cloud-based NLP service that easily creates a natural conversational layer over your data. It can be used to find the most appropriate answer for any given natural language input, from your custom knowledge base (KB) of information.

A client application for QnA Maker is any conversational application that communicates with a user in natural language to answer a question. Examples of client applications include social media apps, chat bots, and speech-enabled desktop applications.

## When to use QnA Maker

* **When you have static information** - Use QnA Maker when you have a static information in your knowledge base of answers. This knowledge base is custom to your needs, which you've built with documents such as PDFs and URLs.
* **When you want to provide the same answer to a request, question, or command** - when different users submit the same question, the same answer is returned to both. 
* **When you want to manage a bot conversation that includes static information** - your knowledge takes a user's conversational text or command and answers it. If the answer is part of a pre-determined conversation flow, represented in your knowledge base with multi-turn context, the bot can easily provide this flow.  

## Use QnA Maker knowledge base in a chat bot

Once a QnA Maker knowledge base is published, a client application sends a question to your knowledge base endpoint and receives the results as a JSON response. A common client application for QnA Maker is a chat bot.

![Ask a bot a question and get answer from knowledge base content](../media/qnamaker-overview-learnabout/bot-chat-with-qnamaker.png)

|Step|Action|
|:--|:--|
|1|The client application sends the user's _question_ (text in their own words), "How do I programmatically update my Knowledge Base?" to your knowledge base endpoint.|
|2|QnA Maker uses the trained knowledge base to provide the correct answer and any follow-up prompts that can be used to refine the search for the best answer. QnA Maker returns a JSON-formatted response.|
|3|The client application uses the JSON response to make decisions about how to continue the conversation. These decisions can include showing the top answer or presenting more choices to refine the search for the best answer. |

## What is a knowledge base? 

QnA Maker [imports your content](../concepts/data-sources-supported.md) into a knowledge base of question and answer sets. The import process extracts information about the relationship between the parts of your structured and semi-structured content to imply relationships between the question and answer sets. You can edit these question and answer sets or add new ones.  

The content of the question and answer set includes all the alternate questions for a specific answer, metadata tags used to filter answer choices during the search, and follow-up prompts to continue the search refinement.

![Example question and answer with metadata](../media/qnamaker-overview-learnabout/example-question-and-answer-with-metadata.png)

After you publish your knowledge base, a client application sends a user's question to your endpoint. Your QnA Maker service processes the question and responds with the best answer. 

## Create, manage, and publish to a bot without code

The QnA Maker portal provides the complete knowledge base authoring experience. You can import documents, in their current form, to your knowledge base. These documents (such as an FAQ, product manual, spreadsheet, or web page) are converted into question and answer sets. Each set is analyzed for follow-up prompts and connected to other sets. The final markdown format supports rich presentation including images. 

Once your knowledge base is edited, publish the knowledge base to a working [Azure Web App bot](https://azure.microsoft.com/en-us/services/bot-service/) without writing any code. Test your bot in the [Azure portal](https://portal.azure.com) or download and continue development. 

## Search quality and ranking provides the best possible answer

QnA Maker provides a layered approach of Azure Search and QnA Maker. Queries are first sent to Azure Search, then the returned results are reranked by QnA Maker with information criteria such as metadata and multi-turn context. 

## QnA Maker improves the conversation process

QnA Maker provides multi-turn prompts and active learning to help you improve your basic question and answer sets. 

**Multi-turn prompts** give you the opportunity to connect question and answer pairs. This connection allows the client application to provide an answer but also provide more questions to refine the answer. 

After the knowledge base receives questions from users at the published endpoint, QnA Maker applies **active learning** to these real-world questions to suggest changes to your knowledge base to improve the quality. 

## Development lifecycle

QnA Maker provides authoring, training, and publishing along with collaboration permissions to integrate into the full development life cycle. 

## How do I start?

**Step 1**: Create a QnA Maker resource in the [Azure portal](https://portal.azure.com). 

**Step 2**: Create a knowledge base in the [QnA Maker](https://www.qnamaker.ai) portal. Add [files and URLs](../concepts/data-sources-supported.md) to create the knowledge base.  

**Step 3**: Publish your knowledge base and test from your custom endpoint using cURL or Postman. 

**Step 4**: From your client application, programmatically call your knowledge base's endpoint and read the JSON response show the best answer to the user.  

## News and updates

Learn what's new with QnA Maker.

* June 2019
    * Improved ranker model for French, Italian, German, Spanish, Portuguese
* April 2019
    * Support website content extraction
    * Sharepoint document support
* March 2019
    * Active learning 
    * Improved NLP ranker model for English, 

e.	Jun: improved ranker model for French, Italian, German, Spanish, Portuguese


## Next steps
QnA Maker provides everything you need to build, manage, and deploy your custom knowledge base. 

> [!div class="nextstepaction"]
> [Create a QnA Maker service](../how-to/set-up-qnamaker-service-azure.md)