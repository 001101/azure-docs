---
title: Prevent content translation - Translator Text API
titlesuffix: Azure Cognitive Services
description: Prevent translation of content with the the Translator Text API.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 11/20/2018
ms.author: v-jansko
---

# How to prevent translation of content with the Translator Text API

The Translator Text API allows you to tag content so that it isn't translated. For example, you may want to tag code, a brand name, or a word/phrase that doesn't make sense when localized. There are a few ways you can do this: 

<ol><li>Escape to a Twitter tag @somethingtopassthrough or #somethingtopassthrough. Un-escape after translation.</li>

<li>Tag your content with `notranslate`. </li>

Example:

```html
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

<li>Use the <a href="dynamic-dictionary.md">dynamic dictionary</a> to prescribe a specific translation.</li>

<li>Don't pass the string to the Translator Text API for translation.</li>

<li>Custom Translator: Use a <a href="custom-translator/what-is-dictionary.md">dictionary in Custom Translator</a> to prescribe the translation of a phrase with 100% probability.</li>
</ol>


## Next steps
> [!div class="nextstepaction"]
> [Avoid translation in your Translator API call](reference/v3-0-translate.md)
