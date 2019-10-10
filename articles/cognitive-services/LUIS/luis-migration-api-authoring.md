---
title: Authoring changes in the API
titleSuffix: Azure Cognitive Services
description: The authoring V2 and V3 APIs have changed. Use this guide to understand how to use the authoring APIs. 
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: diberry
---

# Authoring changes for V2 and V3

The authoring APIs are updated, for both V2 and V3, to allow for [model decomposition with machine-learned entities](luis-concept-model.md#model-decomposition). 

Adding model decomposition is provided on both authoring API versions so you can choose to migrate your app's authoring and prediction at different times. 

You can author in either V2 or V3 from the APIs. The same app can also choose to use V2 or V3 prediction endpoint responses. You do not have to have the same version of API for authoring and response.

The LUIS portal uses the V3 authoring APIs exclusively.

## Next steps

Review [changes to the prediction endpoint](luis-migration-api-v3.md) for V3 API.
