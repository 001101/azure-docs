---
title: Features - LUIS
description: Add features to a language model to provide hints about how to recognize input that you want to label or classify.
ms.topic: conceptual
ms.date: 04/23/2020
---
# Machine-learning features

A machine learning _feature_ is a distinguishing trait or attribute of data that your system observes & learns through. In Language Understanding (LUIS), a feature describes and explains what is significant about your intents and entities.

LUIS provides the following machine-learning features:
* Phrase list feature
* Model (intent or entity) as a feature

Features should be considered a necessary part of your schema design.

## What is a phrase list

A phrase list is a list of words or phrases that encapsulates a particular concept.

### When to use a phrase list

When you need your LUIS app to be able to generalize and identify new items for the concept, use a phrase list. Phrase lists are like domain-specific vocabulary that help with enhancing the quality of understanding of both intents and entities.

### How to use a phrase list

With a phrase list, LUIS considers context and generalizes to identify items that are similar to, but not an exact text match.

Steps to use a phrase list:
* Start with a machine-learned entity
    * Add example utterances
    * Label with a machine-learned entity
* Add a phrase list
    * Add words with similar meaning - do **not** add every possible word or phrase. Instead, add a few words or phrases at a time, then retrain and publish.
    * Review and add suggested words

### A typical scenario for a phrase list

A typical scenario for a phrase list is to boost words of a specific or central idea, for example, names. A name can be any type of word such as a noun, verb, adjective, slang, or made-up words.

An example of names that may need a phrase list to boost their significance are medical terms. The terms can have specific physical, chemical, therapeutic, or abstract meaning. LUIS won't know the words are important to your subject domain without a phrase list.

If you want to have names that are unique to your subject domain extracted:
* First create example utterances and label names within those utterances.
* Then create a phrase list with examples of the names within the subject domain.
* Add the phrase list to the entity model that extracts the concept used in the phrase list. The most common scenario is a component (child) of a machine-learned entity. If the phrase list should be applied across all intents or entities, mark the phrase list as a global phrase list. The `enabledForAllModels` flag controls this model scope in the API.

<a name="how-to-use-phrase-lists"></a>
<a name="how-to-use-a-phrase-lists"></a>
<a name="phrase-lists-help-identify-simple-exchangeable-entities"></a>

## When to use an entity as a feature to an intent

Add an entity as a feature to an intent when the detection of that entity is significant for the intent.

For example, if the intent is for booking a flight and the entity is ticket information (such as the number of seats, origin, and destination), then finding the ticket information entity should add significant weight to the prediction of the book flight intent.

## When to use an entity as a feature to another entity

An entity (A) should be added as a feature to another entity (B) when the detection of that entity (A) is significant for the prediction of entity (B).

For example, if the street address entity (A) is detected, then finding the street address (A) adds significant weight to the prediction for the shipping address entity (B).

## Global features

While the most common use is to apply a feature to a specific model, configure the feature as a **global** feature to boost the concept for your entire schema.

An example is to add an additional vocabulary, such as words from another language, to the app. If your customers use a primary language, but expect to be able to use another language within the same utterance, add examples of those expected tokens to a phrase list.

Because the user expected to use the second language across any intent or entity, it should be added in a phrase list with the phrase list configured as a global feature.

## Best practices
Learn [best practices](luis-concept-best-practices.md).

## Next steps

* [Extend](schema-change-prediction-runtime.md) your app models at prediction runtime
* See [Add Features](luis-how-to-add-features.md) to learn more about how to add features to your LUIS app.
