---
title: Supported Languages of the Speech service | Microsoft Docs
description: A list of spoken languages that are supported by Speech service.
services: cognitive-services
author: v-jerkin
manager: wolfma

ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 09/15/2017
ms.author: v-jerkin
---
# Supported languages

Different languages are supported for different Speech services functions. The tables below summarize language support.

## Speech to Text

Language support varies depending on the speech recognition mode.

### Interactive and dictation mode

The Speech to Text API supports the following languages in `interactive` and `dictation` modes. 

|Code | Language | Code | Language |
|-----|-----|-----|-----|
| ar-EG | Arabic (Egypt), modern standard | hi-IN | Hindi (India) |
| ca-ES | Catalan (Spain) | it-IT | Italian (Italy)  |
| da-DK | Danish (Denmark) | ja-JP | Japanese (Japan) |
| de-DE | German (Germany) |ko-KR | Korean (Korea) |
| en-AU | English (Australia) |nb-NO | Norwegian (Bokmål) (Norway)  |
| en-CA | English (Canada) | nl-NL | Dutch (Netherlands)   |
| en-GB | English (United Kingdom) |pl-PL | Polish (Poland) |
| en-IN | English (India) | pt-BR | Portuguese (Brazil)  |
| en-NZ | English (New Zealand) |pt-PT | Portuguese (Portugal)  |
| en-US | English (United States) | ru-RU | Russian (Russia) |
| es-ES | Spanish (Spain) | sv-SE | Swedish (Sweden) |
| es-MX | Spanish (Mexico) |zh-CN | Chinese (Mandarin, simplified)  |
| fi-FI | Finnish (Finland) |zh-HK | Chinese (Hong Kong SAR) |
| fr-CA | French (Canada) | zh-TW | Chinese (Mandarin, Taiwanese)|
| fr-FR | French (France) | ||

### Conversation mode

The Microsoft speech recognition API supports the following languages in `conversation` modes. 

| Code | Language | Code | Language |
|-----|-----|-----|-----|
| ar-EG | Arabic (Egypt), modern standard | It-IT | Italian (Italy) |
| de-DE | German (Germany) | ja-JP | Japanese (Japan) |
| en-US | English (United States) | pt-BR | Portuguese (Brazil) |
| es-ES | Spanish (Spain) | ru-RU | Russian (Russia) |
| fr-FR | French (France) | zh-CN | Chinese (Mandarin, simplified) |

### Customization

The following languages are supported for custom Speech to Text language models.

| Code | Language | Code | Language |
|-|-|-|-|
en-US | English (United States) | de-DE | German
zh-CN | Chinese | pt-BR | Portuguese (Brazil)
sp-SP | Spanish (Spain) | ru-RU | Russian
fr-FR | French (France) | jp-JP | Japanese
it-IT | Italian | ar-EG | Arabic (Egypt)

Custom acoustic models support only US English (en-US). However, Chinese acoustic data sets can be imported for testing Chinese language models. 

Custom pronunciation supports only US English (en-US) and German (de-DE).

## Text to Speech

The Text to Speech API offers the following voices, each of which supports a specific language and dialect, identified by locale.

Locale | Language | Gender | Service name mapping
-------|----------|---------|--------------------
ar-EG* | Arabic (Egypt) | Female | "Microsoft Server Speech Text to Speech Voice (ar-EG, Hoda)"
ar-SA | Arabic (Saudi Arabia) | Male | "Microsoft Server Speech Text to Speech Voice (ar-SA, Naayf)"
bg-BG | Bulgarian | Male | "Microsoft Server Speech Text to Speech Voice (bg-BG, Ivan)"
ca-ES | Catalan (Spain) | Female | "Microsoft Server Speech Text to Speech Voice (ca-ES, HerenaRUS)"
cs-CZ | Czech | Male | "Microsoft Server Speech Text to Speech Voice (cs-CZ, Jakub)"
da-DK | Danish | Female | "Microsoft Server Speech Text to Speech Voice (da-DK, HelleRUS)"
de-AT | German (Austria) | Male | "Microsoft Server Speech Text to Speech Voice (de-AT, Michael)"
de-CH | German (Switzerland) | Male | "Microsoft Server Speech Text to Speech Voice (de-CH, Karsten)"
de-DE | German (Germany) | Female | "Microsoft Server Speech Text to Speech Voice (de-DE, Hedda) "
| | | Female | "Microsoft Server Speech Text to Speech Voice (de-DE, HeddaRUS)"
| | | Male | "Microsoft Server Speech Text to Speech Voice (de-DE, Stefan, Apollo) "
el-GR | Greek | Male | "Microsoft Server Speech Text to Speech Voice (el-GR, Stefanos)"
en-AU | English (Australia) | Female | "Microsoft Server Speech Text to Speech Voice (en-AU, Catherine) "
| | | Female | "Microsoft Server Speech Text to Speech Voice (en-AU, HayleyRUS)"
en-CA | English (Canada) | Female | "Microsoft Server Speech Text to Speech Voice (en-CA, Linda)"
| | | Female | "Microsoft Server Speech Text to Speech Voice (en-CA, HeatherRUS)"
en-GB | English (UK) | Female | "Microsoft Server Speech Text to Speech Voice (en-GB, Susan, Apollo)"
| | |Female | "Microsoft Server Speech Text to Speech Voice (en-GB, HazelRUS)"
| | |Male | "Microsoft Server Speech Text to Speech Voice (en-GB, George, Apollo)"
en-IE | English (Ireland) |Male | "Microsoft Server Speech Text to Speech Voice (en-IE, Sean)"
en-IN | English (India) | Female | "Microsoft Server Speech Text to Speech Voice (en-IN, Heera, Apollo)"
| | |Female | "Microsoft Server Speech Text to Speech Voice (en-IN, PriyaRUS)"
| | |Male | "Microsoft Server Speech Text to Speech Voice (en-IN, Ravi, Apollo)"
en-US | English (US) |Female | "Microsoft Server Speech Text to Speech Voice (en-US, ZiraRUS)"
| | |Female | "Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)"
| | |Male | "Microsoft Server Speech Text to Speech Voice (en-US, BenjaminRUS)"
| | |Male | "Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)" 
| | |Female | "Microsoft Server Speech Text to Speech Voice (en-US, Guy24kRUS)"
es-ES | Spanish (Spain) |Female | "Microsoft Server Speech Text to Speech Voice (es-ES, Laura, Apollo)"
| | |Female | "Microsoft Server Speech Text to Speech Voice (es-ES, HelenaRUS)"
| | |Male | "Microsoft Server Speech Text to Speech Voice (es-ES, Pablo, Apollo)"
es-MX | Spanish (Mexico) | Female | "Microsoft Server Speech Text to Speech Voice (es-MX, HildaRUS)"
| | | Male | "Microsoft Server Speech Text to Speech Voice (es-MX, Raul, Apollo)"
fi-FI | Finnish | Female | "Microsoft Server Speech Text to Speech Voice (fi-FI, HeidiRUS)"
fr-CA | French (Canada) |Female | "Microsoft Server Speech Text to Speech Voice (fr-CA, Caroline)"
| | | Female | "Microsoft Server Speech Text to Speech Voice (fr-CA, HarmonieRUS)"
fr-CH | French (Switzerland)|Male | "Microsoft Server Speech Text to Speech Voice (fr-CH, Guillaume)"
fr-FR | French (France)|Female | "Microsoft Server Speech Text to Speech Voice (fr-FR, Julie, Apollo)"
| | |Female | "Microsoft Server Speech Text to Speech Voice (fr-FR, HortenseRUS)"
| | |Male | "Microsoft Server Speech Text to Speech Voice (fr-FR, Paul, Apollo)"
he-IL| Hebrew (Israel) | Male| "Microsoft Server Speech Text to Speech Voice (he-IL, Asaf)"
hi-IN | Hindi (India) | Female | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana, Apollo)"
| | |Female | "Microsoft Server Speech Text to Speech Voice (hi-IN, Kalpana)"
| | | Male | "Microsoft Server Speech Text to Speech Voice (hi-IN, Hemant)"
hr-HR | Croatian | Male | "Microsoft Server Speech Text to Speech Voice (hr-HR, Matej)"
hu-HU | Hungarian | Male | "Microsoft Server Speech Text to Speech Voice (hu-HU, Szabolcs)"
id-ID | Indonesian| Male | "Microsoft Server Speech Text to Speech Voice (id-ID, Andika)"
it-IT | Italian |Male | "Microsoft Server Speech Text to Speech Voice (it-IT, Cosimo, Apollo)"
ja-JP | Japanese |Female | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ayumi, Apollo)"
| | |Male | "Microsoft Server Speech Text to Speech Voice (ja-JP, Ichiro, Apollo)"
| | |Female | "Microsoft Server Speech Text to Speech Voice (ja-JP, HarukaRUS)"
| | |Female | "Microsoft Server Speech Text to Speech Voice (ja-JP, LuciaRUS)"
| | |Male | "Microsoft Server Speech Text to Speech Voice (ja-JP, EkaterinaRUS)"
ko-KR | Korean |Female | "Microsoft Server Speech Text to Speech Voice (ko-KR, HeamiRUS)"
ms-MY | Malay|Male | "Microsoft Server Speech Text to Speech Voice (ms-MY, Rizwan)"
nb-NO | Norwegian|Female | "Microsoft Server Speech Text to Speech Voice (nb-NO, HuldaRUS)"
nl-NL | Dutch|Female | "Microsoft Server Speech Text to Speech Voice (nl-NL, HannaRUS)"
pl-PL | Polish|Female | "Microsoft Server Speech Text to Speech Voice (pl-PL, PaulinaRUS)"
pt-BR | Portuguese (Brazil)|Female | "Microsoft Server Speech Text to Speech Voice (pt-BR, HeloisaRUS)"
| | |Male | "Microsoft Server Speech Text to Speech Voice (pt-BR, Daniel, Apollo)"
pt-PT | Portuguese (Portugal)|Female | "Microsoft Server Speech Text to Speech Voice (pt-PT, HeliaRUS)"
ro-RO | Romanian|Male | "Microsoft Server Speech Text to Speech Voice (ro-RO, Andrei)"
ru-RU |Russian| Female | "Microsoft Server Speech Text to Speech Voice (ru-RU, Irina, Apollo)"
| | |Male | "Microsoft Server Speech Text to Speech Voice (ru-RU, Pavel, Apollo)"
sk-SK | Slovak|Male | "Microsoft Server Speech Text to Speech Voice (sk-SK, Filip)"
sl-SI | Slovenian|Male | "Microsoft Server Speech Text to Speech Voice (sl-SI, Lado)"
sv-SE | Swedish|Female | "Microsoft Server Speech Text to Speech Voice (sv-SE, HedvigRUS)"
ta-IN | Tamil (India) |Male | "Microsoft Server Speech Text to Speech Voice (ta-IN, Valluvar)"
th-TH | Thai|Male | "Microsoft Server Speech Text to Speech Voice (th-TH, Pattara)"
tr-TR |Turkish| Female | "Microsoft Server Speech Text to Speech Voice (tr-TR, SedaRUS)"
vi-VN | Vietnamese|Male | "Microsoft Server Speech Text to Speech Voice (vi-VN, An)"
zh-CN | Chinese (Mainland)|Female | "Microsoft Server Speech Text to Speech Voice (zh-CN, HuihuiRUS)"
| | |Female | "Microsoft Server Speech Text to Speech Voice (zh-CN, Yaoyao, Apollo)"
| | |Male | "Microsoft Server Speech Text to Speech Voice (zh-CN, Kangkang, Apollo)"
zh-HK | Chinese (Hong Kong)|Female | "Microsoft Server Speech Text to Speech Voice (zh-HK, Tracy, Apollo)"
| | |Female | "Microsoft Server Speech Text to Speech Voice (zh-HK, TracyRUS)"
| || Male | "Microsoft Server Speech Text to Speech Voice (zh-HK, Danny, Apollo)"
zh-TW | Chinese (Taiwan)|Female | "Microsoft Server Speech Text to Speech Voice (zh-TW, Yating, Apollo)"
| || Female | "Microsoft Server Speech Text to Speech Voice (zh-TW, HanHanRUS)"
| || Male | "Microsoft Server Speech Text to Speech Voice (zh-TW, Zhiwei, Apollo)"

\* *ar-EG supports Modern Standard Arabic (MSA).*

### Customization

Custom Text to Speech voice fonts support US English (en-US) and Chinese (zh-CN).

## Speech Translation

The Speech Translation API supports different languages for speech-to-speech and speech-to-text translation. The source language must always be from the Speech Language table below. The available target languages depend on whether the translation target is speech or text.

You can obtain either list of languages programmatically using the REST API's Languages endpoint. The list provides each language code, as well as the language name in English—or in any other supported language. This list is updated by the Microsoft Translator service whenever a new language becomes available.

### Speech languages

| Speech language   | Language code |
|:----------- |-|
| Arabic (Modern Standard)      | `ar` |
| Chinese (Mandarin)      | `zh` |
| English      | `en` |
| French      | `fr` |
| German      | `de` |
| Italian      | `it` |
| Japanese      | `jp` |
| Portuguese (Brazilian)     | `pt` |
| Russian      | `ru` |
| Spanish      |  `es` |

### Text languages

| Text language    | Language code |
|:----------- |:-------------:|
| Afrikaans      | `af`          |
| Arabic       | `ar`          |
| Bangla      | `bn`          |
| Bosnian (Latin)      | `bs`          |
| Bulgarian      | `bg`          |
| Cantonese (Traditional)      | `yue`          |
| Catalan      | `ca`          |
| Chinese Simplified      | `zh-Hans`          | 
| Chinese Traditional      | `zh-Hant`          |
| Croatian      | `hr`          |
| Czech      | `cs`          |
| Danish      | `da`          |
| Dutch      | `nl`          |
| English      | `en`          |
| Estonian      | `et`          |
| Fijian      | `fj`          |
| Filipino      | `fil`          |
| Finnish      | `fi`          |
| French      | `fr`          |
| German      | `de`          |
| Greek      | `el`          |
| Haitian Creole      | `ht`          |
| Hebrew      | `he`          |
| Hindi      | `hi`          |
| Hmong Daw      | `mww`          |
| Hungarian      | `hu`          |
| Indonesian      | `id`          |
| Italian      | `it`          |
| Japanese      | `ja`          |
| Kiswahili      | `sw`          |
| Klingon      | `tlh`          |
| Klingon (plqaD)      | `tlh-Qaak`          |
| Korean      | `ko`          |
| Latvian      | `lv`          |
| Lithuanian      | `lt`          |
| Malagasy      | `mg`          |
| Malay      | `ms`          |
| Maltese      | `mt`          |
| Norwegian      | `nb`          |
| Persian      | `fa`          |
| Polish      | `pl`          |
| Portuguese      | `pt`          |
| Queretaro Otomi      | `otq`          |
| Romanian      | `ro`          |
| Russian      | `ru`          |
| Samoan      | `sm`          |
| Serbian (Cyrillic)      | `sr-Cyrl`          |
| Serbian (Latin)      | `sr-Latn`          |
| Slovak     | `sk`          |
| Slovenian      | `sl`          |
| Spanish      | `es`          |
| Swedish      | `sv`          |
| Tahitian      | `ty`          |
| Tamil      | `ta`          |
| Thai      | `th`          |
| Tongan      | `to`          |
| Turkish      | `tr`          |
| Ukrainian      | `uk`          |
| Urdu      | `ur`          |
| Vietnamese      | `vi`          |
| Welsh      | `cy`          |
| Yucatec Maya      | `yua`          |
