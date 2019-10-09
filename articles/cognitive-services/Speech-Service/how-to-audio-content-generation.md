---
title: Audio Content Generation - Speech Service
titleSuffix: Azure Cognitive Services
description: Audio Content Creation is an online tool that allow you to customize and fine-tune Microsoft's text-to-speech output for your apps and products.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: erhopf
---

# Audio Content Generation

Audio Content Creation is an online tool that allow you to customize and fine-tune Microsoft's text-to-speech output for your apps and products. You can use this tool to fine-tune public and custom voices for more accurate natural expressions, and manage your output in the cloud.

The Audio Content Creation tool is based on [Speech Synthesis Markup Language (SSML)](speech-synthesis-markup.md). To simplify customization and tuning, Audio Content Generation allows you to visually inspect your text-to-speech outputs in real time.

## How does it work?

This diagram shows the steps it takes to tune and export customized speech-to-text outputs. Use the links below to learn more about each step.

![](media/audio-content-generation/audio_content_generation_diagram.png)

1. The first step is to [create an Azure account, register a Speech resource, and get a subscription key](#create-a-speech-resource). Make sure that when you create your Speech resource, that you select the **S0** pricing tier, F0 resources aren't supported. Once you have a subscription key, you can use it to call the Speech service, and to access [Speech Studio](https://speech.microsoft.com).
2. [Create an audio tuning file](#create-an-audio-tuning-file) using plain text or SSML.
3. Choose the voice and the language that you'd like to tune. Audio Content Generation includes all of the [Microsoft text-to-speech voices](language-support.md#text-to-speech). You can use standard, neural, or your own custom voice.
   >[!NOTE]
   > Gated access is available for Custom Neural Voices, which allow you to create high-definition voices similar to natural-sounding speech. For additional details, see [Gating process](#link-to-ethics-content).

4. Review the default result. Then use the tuning tool to adjust pronunciation, pitch, rate, intonation, voice style, and more. For a complete list of options, see [Speech Synthesis Markup Language](speech-synthesis-markup.md).
5. Save and [export your tuned audio](#export-tuned-audio). When you save the tuning track in the system, you can continue to work and iterate on the  output. When you're satisfied with the output, you can create an audio generation task with the export feature. You can observe the status of the export task, and download the output for use with your apps and products.
6. The last step is to use the custom tuned voice in your apps and products.

## Create a Speech resource

Follow these steps to create a Speech resource and connect it with Speech Studio.

1. Follow these instructions to [create a Speech resource](#create-a-speech-resource). Make sure that your pricing tier is set to **S0**. If you are using one of the Neural voices, make sure that you create your resource in a [supported region](regions.md#standard-and-neural-voices).
2. Sign into [Speech Studio](https://speech.microsoft.com/portal?projecttype=voicetuning).
3. Select an existing project, or click **Create New**.
4. You can modify your subscription at any time with the **Settings** option, located in the top nav.

## Create an audio tuning file

There are two ways to get your content into the Audio Content Generation tool.

**Option 1:**

1. After you sign into [Audio Content Generation](https://speech.microsoft.com/portal?projecttype=voicetuning), click **Audio Tuning** to create a new audio tuning file.
2. When the editing window appears, you can input up to 10,000 characters.
3. Don't forget to save.

**Option 2:**

1. After you sign into [Audio Content Generation](https://speech.microsoft.com/portal?projecttype=voicetuning), click **Upload** to import one or more text files. Both plain text and SSML are supported.
2. When you upload your text files, make sure that the content meets these requirements.
   | Property | Value / Notes |
   |----------|-------|
   | File format | Plain text (.txt)<br/> SSML text (.txt)<br/> Zip files aren't supported |
   | Encoding format | UTF-8 |
   | File name | Each file must have a unique name. Duplicates aren't supported. |
   | Text length | Text files must not exceed 10,000 characters. |
   | SSML restrictions | Each SSML file can only contain a single piece of SSML. |

### Plain text example

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### SSML text example

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## Export tuned audio

After you've reviewed your audio output and are satisfied with your tuning and adjustment, you can export the audio.

1. From the [Audio Content Generation](https://speech.microsoft.com/portal?projecttype=voicetuning) tool, click **Export** to create an audio generation task.
2. Choose the output fromat for your tuned audio. These formats are supported:
   | Format | 16 kHz sample rate | 24 kHz sample rate |
   |--------|--------------------|--------------------|
   | wav | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
   | mp3 | audio-16khz-128kbitrate-mono-mp3 | audio-24khz-160kbitrate-mono-mp3 |
3. You can view the status of the task on the **Export task** tab. If the task fails, see the detailed information page for a full report.
4. When the task is complete, your audio is available for download on the **Audio Library** tab.
5. Click **Download**. Now you're ready to use your custom tuned audio in your apps or products.

## Next steps

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
