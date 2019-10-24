---
title: 'How To: Fulfill Custom Commands on the client with the Speech SDK (Preview)'
titleSuffix: Azure Cognitive Services
description: In this article, handle Custom Custom Commands activities on client with the Speech SDK
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: sausin
---

# How To: Connect Custom Commands application as a remote skill to an existing v4 bot (Preview)

In this article, you'll connect a Custom Commands application as a [Remote Skill](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-skills-overview?view=azure-bot-service-4.0) to an existing bot.

## Prerequisites

- Knowledge about [Bot Framework](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0)
- Building and hosting a v4 based bot and remote skills
- A Custom Commands app:
   - [Quickstart: Create a Custom Command (Preview)](./quickstart-custom-speech-commands-create-new.md)
   - [Quickstart: Create a Custom Command with Parameters (Preview)](./quickstart-custom-speech-commands-create-parameters.md)

## Get the skill manifest for your Custom Commands app

Each Custom Command application by default is enabled as a remote skill. This means for each of the authored applications, a skill manifest file is generated. This skill manifest file can be used to configure an existing Bot Framework's v4 bot to start calling in to your Custom Command application remotely.

To obtain the skill manifest of the Custom Commands application, first publish your application and then click the button **Download Skill Manifest**.

## Configure and register your Custom Commands app as a skill dialog to v4 bot

Based on the skill manifest file from the previous step, proceed with the steps for configuring and registering Custom Command skill as a skill dialog. For more information, see [Enable skills on an existing v4 bot](https://microsoft.github.io/botframework-solutions/howto/skills/addskillsupportforv4bot/).
	
## Route utterances of the v4 bot to Custom Commands Remote Skill

Use dispatcher of the v4 bot to route utterances to Custom Command skills. The dispatcher logic should be built in a way that it's referring to the same LUIS application which is configured as part of the Custom Commands application. Luis application's configuration is present as part of the skill manifest file.
