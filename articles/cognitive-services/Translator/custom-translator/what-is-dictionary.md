---
title: What is a dictionary? - Custom Translator
titleSuffix: Azure Cognitive Services
description: A dictionary is an aligned document that specifies a list of phrases or sentences (and their translations) that you always want Microsoft Translator to translate the same way. Dictionaries are sometimes also called glossaries or term bases.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
#Customer intent: As a Custom Translator, I want to understand how to use a dictionary to build a custom translation model.
---

# What is a dictionary?

A dictionary is an aligned pair of documents that specifies a list of phrases or sentences and their corresponding translations. When a dictionary is used during training, Microsoft Translator will always translate any instances of the source phrase or sentence using the translation you provide.  Dictionaries are sometimes called glossaries or term bases. You can think of the dictionary as a brute force “copy and replace” for all the terms you list.

Dictionaries only work for projects in language pairs that have a fully supported Microsoft neural machine translation (NMT) system behind them. See http://www.aka.ms/translatorlanguages for a complete list of languages.

## Phrase dictionary 
When you include a phrase dictionary in training your model, any word or phrase listed is translated in the way you specified. The rest of the sentence is translated as usual. You can use a phrase dictionary to specify phrases that shouldn't be translated by providing the same untranslated phrase in the source and target file in the dictionary.

## Sentence dictionary
The sentence dictionary allows you to specify an exact target translation for a source sentence. For a sentence dictionary match to occur, the entire submitted sentence must match the source dictionary entry.  If only a portion of the sentence matches, the entry wont match.  When a match is detected, the target entry of the sentence dictionary will be returned.

## Dictionary-only trainings
You can train a model using only dictionary data. To do this, select only the dictionary document (or multiple dictionary documents) that you wish to include and tap Create model. Since this is a dictionary-only training, there is no minimum number of training sentences required. Your model will typically complete training much faster than a standard training.  The resulting models will use the Microsoft baseline models for translation with the addition of the dictionaries you have added.  You will not get a test report.

## Recommendations

- Dictionaries are not a substitute for a trained model with training data.  Dictionaries essentially find and replace words or sentences.  Letting the system learn from your training material in full sentences is generally a better choice than using a dictionary. 
- The phrase dictionary should be used sparingly. When a phrase within a sentence is replaced, the context within that sentence is lost or limited for translating the rest of the sentence. The result is that while the phrase or word within the sentence will translate according to the phrase dictionary, the overall translation quality of the sentence will often suffer.
- The phrase dictionary works well for compound nouns like product names (“Microsoft SQL Server”), proper names (“City of Hamburg”), or features of the product (“pivot table”). It does not work equally well for verbs or adjectives because these are typically highly inflected in the source or in the target language. Avoid phrase dictionary entries for anything but compound nouns. 
- When using a dictionary, capitalization and punctuation in your translations will reflect the capitalization and punctuation provided in your target file. Capitalization and punctuation are ignored when trying to identify matches between your input sentence and the source sentences in your dictionary file. For example, let’s say we trained an English to Spanish system that used a dictionary that specified “City of Hamburg” in the source file, and “Ciudad de hamburg” in the target file. If I requested translation of a sentence that included the phrase “city of Hamburg”, then “city of Hamburg” would match to my dictionary file for the entry “City of Hamburg”, and would map to “Ciudad de hamburg” in my final translation.
- If a word appears more than once in a dictionary file, the system will always use the last entry provided. Your dictionary should not contain multiple translations of the same word.

## How to create a dictionary file?

To create a dictionary, follow the steps listed here.

1.  Create an Excel file. Custom Translator supports only
    “.xlsx” files created using Excel 2007 and later. This file contains a
    list of source-language terms and a list of corresponding
    target-language equivalents in the first sheet of the Workbook. Other
    sheets in the workbook will be ignored.

2.  In cell A1 of the first sheet, enter the ISO standard Language IDs for
    the source language (for example “en” or “en-us” for English)

3.  In cell B1 of the first sheet, enter the ISO standard language IDs for
    the target language (for example “es” or “es-es” for Spanish)

4.  Enter the source language terms in Column A, and the equivalent
    translations for these terms in the target Language in Column B. HTML
    tags in the dictionary will be ignored.

The image below shows an Excel file containing a dictionary of terms mapped from
English to Spanish.

![Dictionary file](media/how-to/how-to-create-dictionary.png)

## Next steps

- Read about [guidelines on document formats](document-formats-naming-convention.md).