---
title: Artificial intelligence
description: LUIS uses artificial intelligence to provide language understanding to your data, based on the schema you defined.
ms.topic: conceptual
ms.date: 06/29/2020
---

# Artificial intelligence in Language Understanding (LUIS)

LUIS uses artificial intelligence to provide language understanding to your data, based on the schema you defined.

## Natural language processing

Natural Language Understanding (NLU) is a specific subtopic of Natural Language Processing (NLP). NLU is intended to be the ability to _transform_ a linguistic statement to a representation that enables you to understand your users naturally.

This includes being able to identify:
* What the user wants
* What they are talking about.

**Natural language understanding** remains a very challenging problem and is defined as an _AI-hard_ problem.

**Natural language processing** is a broader concept that handles any form of processing of textual data, this includes things like:

* Tokenization,
* Part of Speech (pos) Tagging
* Segmentation
* Morphological analysis
* Semantic similarity
* Discourse
* Translation

LUIS is intended to focus on intention and extraction, without any knowledge of the broader _NLP_ aspects. It has no notion of semantic similarity without explicit identification in examples.

For example, the following tokens (words) are three different things until they are used in similar contexts in the examples provided:

* buy
* buying
* bought

Some aspects of NLP are configurable in LUIS using settings, and others you need to provide them through your example utterances or your dictionary lists used in the model. Some are provided by other Cognitive Services, such as Translation.

## Next steps

* Learn about the [development lifecycle](luis-concept-app-iteration.md) for a LUIS app