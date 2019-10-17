---
title: 'Quickstart: Create a Custom Command with Parameters (Preview)'
titleSuffix: Azure Cognitive Services
description: In this article, you'll add parameters to a Custom Commands application.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
---

# Quickstart: Create a Custom Command with Parameters (Preview)

In the [previous article](./quickstart-custom-speech-commands-create-new.md), we created a new Custom Commands application to respond to commands without parameters.

In this article, we will extend this application with parameters so that it can handle turning on and turning off multiple devices.

## Create Parameters

Open the application we created previously.

Since the Command will now handle on and off, rename the Command to "TurnOnOff".  Hover over the name of the Command and select the edit icon to change the name.

Create a new parameter to represent whether the user wants to turn the device on or off.  Select the `+` icon next to the Parameters section.

![Create parameter](media/custom-speech-commands/create-on-off-parameter.png)

Setting|Suggested value|Description
---|---|---
Name | OnOff |Choose a descriptive name for your parameter
Required|true|Checkbox indicating whether a value for this parameter is required before completing the Command
Prompt |"On or off?"| A prompt to ask for the value of this parameter when it isn't known
Type|String|The type of parameter, such as Number, String, or Date Time
Configuration|String List|For Strings, a String List limits inputs to a set of possible values
Normalized Values|on, off|For a String List parameter, the set of possible values and their synonyms


Next add a second parameter to represent the name of the devices.  For this example, a tv and a fan.

Setting|Suggested value|Description
---|---|---
Name | SubjectDevice |Choose a descriptive name for your parameter
Required|true|Checkbox indicating whether a value for this parameter is required before completing the Command
Prompt |"On or off?"| A prompt to ask for the value of this parameter when it isn't known
Type|String|The type of parameter, such as Number, String, or Date Time
Configuration|String List|For Strings, a String List limits inputs to a set of possible values
Normalized Values|tv, fan|For a String List parameter, the set of possible values and their synonyms
Synonyms (tv)|television, telly|Optional synonyms for each possible value of a String List Parameter


## Add Sample Sentences

With parameters, it's helpful to add sample sentences with:
- Full parameter information - `"turn {OnOff} the {SubjectDevice}"`
- Partial parameter information - `"turn it {OnOff}"`
- No parameter information - `"turn something"`

Examples with different amounts of information allow the Custom Commands application to resolve both one-shot resolutions and multi-turn resolutions with partial information.

Select Sample Sentences to access the sample sentences editor.

> [!TIP]
> In the Sample Sentences editor use curly braces to refer to your parameters. - `turn {OnOff} the {SubjectDevice}`
> Use tab completion to refer to previously created parameters.
> Valid parameter references will be highlighted in Green.  Invalid references will be highlighted in Red.

- turn \{OnOff\} the \{SubjectDevice\}
- \{SubjectDevice\} \{OnOff\}
- turn it \{OnOff\}
- turn something \{OnOff\}
- turn something

> Screenshot: Syntax highlighted sample sentences

## Add Parameters to Completion rule

Modify the Completion rule that you created in the previous quickstart
- Add Required Parameter condition and select OnOff and SubjectDevice
- Edit the Speech Response to refer to the required parameters - `Ok, turning {OnOff} the {SubjectDevice}`


## Try it out
Open the Test chat panel and try a few interactions

> - A: turn off the tv
> - B: Ok, turning off the tv

> - A: turn off the television
> - B: Ok, turning off the tv

> - A: turn it off
> - B: Which device?
> - A: the tv
> - B: Ok, turning off the tv

## Next steps
> [!div class="nextstepaction"]
> [Quickstart: Connect to a Custom Command application with the Speech SDK (Preview)](./quickstart-custom-speech-commands-speech-sdk.md)

