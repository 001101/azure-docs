---
title: Text to Speech overview | Microsoft Docs
description: An overview of the capabilities of Microsoft Text to Speech.
services: cognitive-services
author: v-jerkin
manager: wolfma

ms.service: cognitive-services
ms.technology: speech
ms.topic: article
ms.date: 03/22/2018
ms.author: v-jerkin
---
# Text to Speech overview

Microsoft Text-to-Speech (TTS) is a cloud-based API that converts input text into natural-sounding speech (also called speech synthesis).

With the Text to Speech API, your application sends HTTP requests to a cloud server, where text is synthesized into human-sounding speech and returned as an audio file. The API can be used in many different contexts to provide real-time text-to-speech conversion in a variety of voices and languages.

Scenarios in which we're witnessing a growing adoption of speech synthesis include:

* *Improving accessibility:* Text to Speech technology enables content owners and publishers to respond to the different ways people interact with the content. It can be a very useful tool for those with visual impairment or reading difficulties. Voice output also makes it easier for people to enjoy textual content, such as newspapers or blogs, on mobile devices while commuting or exercising.

* *Responding in multitasking scenarios:* Text to Speech allows people to absorb important information quickly and comfortably while driving or otherwise outside a convenient reading environment. Navigation is a very common application in this area. 

* *Enhancing learning with multiple modes:* Different people learn best different ways. Online learning experts have shown that providing voice and text together can help make information easier to learn and retain. 

* *Delivering intuitive bots or assistants:* The ability to talk is an integral part of an intelligent chat bot or a virtual assistant. More and more companies are developing chat bots to provide engaging customer service experiences for their customers. Voice adds another dimension by allows the bot's responses to be received aurally (for exmaple, by phone).

The Microsoft Text-to-Speech (TTS) service offers more than 75 voices in more than 45 languages and locales. To use these standard "voice fonts," you just specify the voice name with a few other parameters when you call the service's REST API. For the details of the voices supported, see [Supported Languages](supported-languages.md). 

If you need to support a new dialect or simply want a unique voice for your application, the Text to Speech API supports training [custom voice models](customize-tts) from your own data.
