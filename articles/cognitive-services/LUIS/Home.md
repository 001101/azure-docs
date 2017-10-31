---
title: Learn about Language Understanding Intelligent Service (LUIS) in Azure | Microsoft Docs 
description: Learn how to use Language Understanding Intelligent Service (LUIS) to bring the power of machine learning to your applications.
services: cognitive-services
author: cahann
manager: hsalama

ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 03/01/2017
ms.author: cahann
---

# Learn about Language Understanding Intelligent Service (LUIS)

Language Understanding Intelligent Service (LUIS) allows your application to understand what a person wants in their own words. LUIS uses machine learning to allow developers to build applications that can receive user input in natural language and extract meaning from it. A client application that converses with users, such as a chat bot, can pass user input to a LUIS app and receive relevant, detailed information back.

## What is a LUIS app?

A LUIS app is a domain-specific language model designed by you and tailored to your needs. You can start with a pre-built domain model, build your own, or blend pieces of a pre-built domain with your own custom information.

A model starts with a list of general user intentions such as "Book Flight" or "Contact Help Desk." Once the intentions are identified, you supply example phrases called utterances for the intents. Then you label the utterances with any specific details you want LUIS to pull out of the utterance.

[Pre-built domain models][pre-built-domains] include all these pieces for you and are a great way to start using LUIS quickly.

After the model is designed, trained, and published, it is ready to receive and process utterances. The LUIS app receives the utterance as an HTTP request and responds with extracted user intentions. Your client application sends the utterance and receives LUIS's evaluation. Your app can then take appropriate action.

![image showing speech via Cortana and text via chat bot]()

## Key LUIS concepts

* **Intents** An intent represents actions the user wants to perform. The intent is a purpose or goal expressed in a user's input, such as booking a flight, paying a bill, or finding a news article. You define and name intents that correspond to these actions. A travel app may define an intent named "BookFlight." 
* **Utterances** An [utterance][add-example-utterances] is text input from the user that your app needs to understand. It may be a sentence, like "Book a ticket to Paris", or a fragment of a sentence, like "Booking" or "Paris flight." Utterances aren't always well-formed, and there can be many utterance variations for a particular intent. 
* **Entities** An entity represents detailed information that is relevant in the utterance. For example, in the utterance "Book a ticket to Paris", "Paris" is a location. By recognizing and labeling the entities that are mentioned in the user’s utterance, LUIS helps you choose the specific action to take to answer a user's request. 

|Intent|Sample User Utterance|Entities|Type|
|-----------|-----------|-----------|------|
|BookFlight|"Book a flight to __Seattle__?"|Seattle|Destination|
|StoreHoursAndLocation|"When does your store __open__?"|open|Time|
|ScheduleMeeting|"Schedule a meeting at __1pm__ with __Bob__ in Distribution"|1pm, Bob|Time, Person|

## Key Development concepts

LUIS has two sets of APIs: the authoring APIs and the endpoint APIs.

* **Authoring APIs and LUIS.ai** The authoring APIs give you and your collaborators control of your LUIS model definition. This control includes management of: models, versions, collaborators, external APIs, Azure subscription keys, testing, and training. [LUIS.ai][luis.ai] is the authoring website that sits on top of the authoring APIS, making the API calls for you.

    ![image of people collaborating around computer]()

* **Endpoint APIs** The endpoint APIs allow users to pass a short, 500-character utterance phrase to LUIS. When you publish your LUIS app, the endpoint URL is created. 

![LUIS recognizes user intent](./media/luis-overview/luis-overview-process.png)

> [!NOTE]
> * The Authoring APIs and LUIS.ai use the programmatic key found in your [LUIS.ai][luis.ai] account page.
> * The Endpoint APIs use the LUIS subscription key found in the [Azure portal][azure-portal].

## Author your LUIS model 
Begin your LUIS model with the intents your app will resolve. Intents are just names such as "BookFlight" or "OrderPizza." 

After an intent is identified, you need [sample utterances][add-example-utterances] that you want LUIS to map to your intent such as "Buy a ticket to Seattle tomorrow." Then, [label][label-suggested-utterances] the parts of the utterance that are relevant to your app domain as entities and set a type such as date or location.

Generally, an **intent** is used to trigger an action and an **entity** is used as a parameter to execute an action.

For example, a "BookFlight" intent could trigger an API call to an external service for booking a plane ticket, which requires entities like the travel destination, date, and airline. See [Plan your app](Plan-your-app.md) for examples and guidance on how to choose intents and entities to reflect the functions and relationships in an app.

### Identify Entities  
[Entity][luis-concept-entity-types] identification determines how successfully the end user gets the correct answer. LUIS provides several ways to identify and categorize entities.

* **Prebuilt Entities** LUIS has many prebuilt domains with [prebuilt entities][pre-built-entities]. You can use the entity without having to use the entire domain. The prebuilt entities save you time.

    ![image showing several domains in LUIS.ai]()

* **Custom Entities** LUIS gives you several ways to identify your own custom entities including simple entities, composite entities, list entities, and hierarchical entities.

    ![image showing several entity type examples - 1 of each type]()

* **Regular Expressions and Phrase Features** LUIS provides [features](luis-concept-feature.md) such as regular expression patterns and phrase lists, which also help identify entities. 

    ![image show regular express and phrase features]

## Improve performance using active learning
Once your application is [published][publish-app] and real user utterances are entered, LUIS uses active learning to improve identification. In the active learning process, LUIS identifies the utterances that it is relatively unsure of. You can label them according to intent and entities, retrain, and republish.

![image of suggested utterances]()

This iterative process has tremendous advantages. LUIS knows what it is unsure of, and your help leads to the maximum improvement in system performance. LUIS learns quicker, and takes the minimum amount of your time and effort. LUIS is an active machine learning at its best. 

## Next steps
Create a [new LUIS app](LUIS-get-started-create-app.md) in 5 minutes.

Watch this short [video tutorial][intro-video] on these steps.

<!-- Reference-style links -->
[create-app]:luis-get-started-create-app.md
[luis.ai]:https://www.luis.ai/
[azure-portal]:https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account
[publish-app]:PublishApp.md#test-your-published-endpoint-in-a-browser
[luis-concept-entity-types]:luis-concept-entity-types.md
[add-example-utterances]: add-example-utterances.md
[pre-built-entities]: pre-builtentities.md
[pre-built-domains]: luis-how-to-use-prebuilt-domains.md
[label-suggested-utterances]: label-suggested-utterances.md
[intro-video]:https://aka.ms/LUIS-Intro-Video