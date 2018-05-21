---
title: Understanding intents in LUIS apps in Azure | Microsoft Docs
description: Describes what intents are in Language Understanding Intelligent Service (LUIS) apps.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/08/2018
ms.author: v-geberr
---
# Intents in LUIS

An intent represents a task or action the user wants to perform. It is a purpose or goal expressed in a user's [utterance](luis-concept-utterance.md).

Define a set of intents that correspond to actions users want to take in your application. For example, a travel app defines an intent named `BookFlight`.

Example intent   |   Example utterances   | 
------|------|
 BookFlight     |   "Book me a flight to Rio next week" <br/> "Fly me to Rio on the 24th" <br/> "I need a plane ticket next Sunday to Rio de Janeiro"    |
 Greeting     |   "Hi" <br/>"Hello" <br/>"Good morning"  |
 CheckWeather | "What's the weather like in Boston?" <br/> "Show me the forecast for this weekend" |
 None         | "Get me a cookie recipe" |

All applications come with the predefined intent, **"[None](#none-intent-is-fallback-for-app)"**. 

## Intent compared to entity
The intent represents the prediction of the entire utterance. By comparison, the entity represents the prediction for a word or phrase inside the utterance that you want extracted.

## Prebuilt domains provide intents
In addition to intents that you define, you can use prebuilt intents from one of the prebuilt domains. See [Use prebuilt domains in LUIS apps](luis-how-to-use-prebuilt-domains.md) to learn about how to customize intents from a prebuilt domain for use in your app.

You assign an utterance to a single intent. When LUIS receives an utterance on the endpoint, it returns the one top intent for that utterance. If you want all intents, you can provide `verbose=true` flag on the query string of the API [endpoint call](https://aka.ms/v1-endpoint-api-docs). 

## How do intents relate to entities?
Create an intent when this intent would trigger an action in your client application, like a call to the checkweather() function, and create an entity to represent parameters required to execute the action. 

|Example intent   | Entity | Entity in example utterances   | 
|------------------|------------------------------|------------------------------|
| CheckWeather | { "type": "location", "entity": "seattle" } | What's the weather like in `Seattle`? |
| CheckWeather | { "type": "date_range", "entity": "this weekend" } | Show me the forecast for `this weekend` | 

## Custom intents

Similar intentioned [utterances](luis-concept-utterance.md) correspond to a single intent. Utterances in your intent can use any [entity](luis-concept-entity-types.md) in the app since entities are not intent-specific. 

## Prebuilt domain intents

[Prebuilt domains](luis-how-to-use-prebuilt-domains.md) have intents with utterances.  

## None intent is fallback for app
The **None** intent is a catch-all or fallback intent. It is used to teach LUIS utterances that are not important in the app domain (subject area). The **None** intent should have between 10 and 20 percent of the total utterances in the application. Do not leave it empty. 

## None intent helps conversation direction
When an utterance is predicted as the None intent and returned to the bot with that prediction, the bot can ask more questions or provide a menu to direct the user to valid choices in the bot. 

## No utterances in None intent skews predictions
If you do not add any utterances for the **None** intent, LUIS forces an utterance that is outside the domain into one of the domain intents. This will skew the prediction scores by teaching LUIS the wrong intent for the utterance. 

## Add utterances to the None intent
The **None** intent is created but left empty on purpose. Fill it with utterances that are outside of your domain. A good utterance for **None** is something completely outside the app as well as the industry the app serves. For example, a travel app should not use any utterances for **None** that can relate to travel, reservations, billing, food, hospitality, cargo, inflight entertainment, etc. What type of utterances are left for the None intent? Start with something specific that your bot shouldn't answer such "What kind of dinosaur can fly?" This is a very specific question far outside of a travel app. 

## None is a required intent
The **None** intent is a required intent and can't be deleted or renamed.

## Negative intentions 

If you want to determine negative and positive intentions, such as "I **want** a car" and "I **don't** want a car", you can create two intents (one positive, and one negative) and add appropriate utterances for each. Or you can create a single intent and mark the two different positive and negative terms as an entity.  

## If you need more than the maximum number of intents 

First, consider whether your system is using too many intents. Intents that are too similar can make it more difficult for LUIS to distinguish between them. Intents should be varied enough to capture the main tasks that the user is asking for, but they don't need to capture every path your code takes. For example, BookFlight and BookHotel might be separate intents in a travel app, but BookInternationalFlight and BookDomesticFlight are too similar. If your system needs to distinguish them, use entities or other logic rather than intents. 

If you cannot use fewer intents, divide your intents into multiple LUIS apps using the dispatcher model, and group related intents. This approach is a best practice if you're using multiple apps for your system. For example, let's say you're developing an office assistant that has over 500 intents. If 100 intents relate to scheduling meetings, 100 are about reminders, 100 are about getting information about colleagues, and 100 are for sending email, you can group intents so that each group is in a single app, then create a top-level group with each intent. Base the utterance to LUIS twice, first to the top-level app, then based on the results, to the group-level app.  
 
Another method is to do some preprocessing on the utterance, such as matching on regular expressions, to determine which LUIS app or set of apps receives the utterance. 

To improve responsiveness, ordinarily a system is designed to reduce the number of REST API calls. Instead, consider sending the utterance to multiple LUIS apps simultaneously and asynchronously, then pick the intent with the highest score.  

If reducing the number of intents or dividing your intents into multiple apps doesn't work for you, contact support. To do so, gather detailed information about your system, go to the [LUIS][LUIS] website, and then select **Support**. If your Azure subscription includes support services, contact [Azure technical support](https://azure.microsoft.com/en-us/support/options/). 

## Next steps

* Learn more about [entities](luis-concept-entity-types.md), which are important words relevant to intents
* Learn how to [add and manage intents](Add-intents.md) in your LUIS app.
* Review intent [best practices](luis-concept-best-practices.md)
