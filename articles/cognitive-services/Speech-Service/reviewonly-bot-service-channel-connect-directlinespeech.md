---
title: Connect a bot to Direct Line Speech
titleSuffix: Bot Service
description: Overview and steps required to connect an existing Bot Framework bot to the Direct Line Speech channel for voice in, voice out interaction with high reliability and low latency.
services: bot-service
author: trrwilson
manager: 
ms.service: bot-service
ms.subservice: bot-service
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: travisw
ms.custom: 
---

> [!NOTE]
> **PRE-PUBLISH NOTE TODO:** remove this note prior to actual publishing! This page will be published to the bot framework docs as a peer to [the Direct Line page](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-channel-connect-directline?view=azure-bot-service-3.0). It's included here only to put it in preliminary source control/review.

# Connect a bot to Direct Line Speech

You can configure your bot to allow client applications to communicate with it through the Direct Line Speech channel.

Once you have [built a Bot Framework bot with the Bot Builder SDK v4](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/), onboarding your bot with Direct Line Speech will enable a low latency, high reliability connection with client applications using the [Cognitive Services Speech SDK](https://aka.ms/speech/sdk). These connections are optimized for voice in, voice out conversational experiences.

## Add the Direct Line Speech channel

1. To add the Direct Line Speech Channel, first open the bot in the [Azure Portal](https://portal.azure.com), and click on **Channels** in the configuration blade.

  ![highlight of the location for selecting channels to connect to](media/voice-first-virtual-assistants/reviewonly-bot-service-channel-directlinespeech-selectchannel.png "selecting channels")

1. In the channel selection page, find and click `Direct Line Speech` to choose the channel.

  ![selecting direct line speech channel](media/voice-first-virtual-assistants/reviewonly-bot-service-channel-directlinespeech-connectspeechchannel.png "connecting Direct Line Speech")

1. This will take you to a configuration page for Direct Line Speech. Once you've reviewed the terms of use, click `Save` to confirm your channel selection.

## Manage secret keys

Client applications will need a bot secret key to connect to your bot through the Direct Line Speech channel. Once you've saved your channel selection, you can retrieve these secret keys from the **Configure Direct Line Speech** page in the Azure Portal.

![getting secret keys for Direct Line Speech](media/voice-first-virtual-assistants/reviewonly-bot-service-channel-directlinespeech-getspeechsecretkeys.png "getting secret keys for Direct Line Speech")

## Adding protocol support to your bot

> [!NOTE]
> TODO: Add details here -- maybe an aka.ms redirect to a blog post later?