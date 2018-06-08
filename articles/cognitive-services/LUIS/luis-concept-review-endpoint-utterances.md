---
title: Review endpoint utterances to use active learning in Language Understanding (LUIS) - Azure| Microsoft Docs
description: Use the active learning feature named 'Review endpoint utterances' to improve performance predictions faster. 
services: cognitive-services
author: v-geberr
manager: kaiqb 

ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
---
# Enable active learning by reviewing endpoint utterances
Active learning is one of three strategies to improve prediction accuracy. 

## What is active learning
Active learning is a two-step process. First, LUIS selects utterances it receives at the app's endpoint that it wants validated. The second step is performed by the app owner or collaborator to validate the selected utterances for [review](label-suggested-utterances.md), including the correct intent and any entities within the intent. After reviewing the utterances, train and publish the app again. 

* Reviewing suggested utterances doesn't have to be done every day but should be part of your regular maintenance of LUIS. 
* If your app is large, you may choose to review some utterances and delete the rest from the list programmatically. This is done by first [getting](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) the list and then [deleting](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) the utterances by id.

## Which utterances are on the review list
LUIS adds utterances to the review list when the top firing intent has a low score or the top two intents' scores are too close. 

## Where are the utterances from?
Endpoint utterances are taken from end-user queries on the application’s HTTP endpoint. If your app is not published or has not received hits yet, you do not have any utterances to review. If no endpoint hits are received aligned with a specific intent or entity, you do not have utterances to review that contain them. 


## Next steps

* Learn how to add [review](Label-Suggested-Utterances.md) endpoint utterances
