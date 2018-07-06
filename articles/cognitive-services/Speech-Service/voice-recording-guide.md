---
title: How to record voice samples for creating a custom voice | Microsoft Docs
description: Here are answers to the most popular questions about the Speech to Text.
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/5/2018
ms.author: v-jerkin
---

# How to record voice samples for a custom voice

Creating a high-quality production custom voice from scratch is not a casual undertaking. The central component of a custom voice is a large collection of audio samples of human speech. It's vital that these audio recordings be of high quality. Choose voice talent that has experience making these kinds of recordings, and have them recorded by a competent recording engineer using professional equipment.

Before you can make the recordings, though, you need a script: the words that will be read by your voice talent to create the audio samples. For best results, your script must have good phonetic coverage and sufficient variety to train the custom voice model.

Many ingredients go into a good custom voice. This guide serves as an introduction to the process, with particular attention paid to issues you are likely to encounter.

> [!TIP]
> For the highest quality results, consider engaging Microsoft to help develop your custom voice. Microsoft has extensive experience producing high-quality voices for its own products, including Cortana and Office.

## Voice recording roles

There are four basic roles in a voice recording project for custom voices.

Role|Purpose
-|-
Voice talent        |The person whose voice will form the basis of the custom voice.
Recording engineer  |The person who oversees the technical aspects of the recording and operates the recording equipment.
Director            |Prepares the script and coaches the voice talent's performance.
Editor              |Finalizes the audio files and prepares them for upload to the Custom Voice portal.

One person may fill more than one role. This guide assumes that you will be primarily filling the director role, and hiring voice talent and a recording engineer. There is, however, some information about the recording engineer role in case you want to make the recordings yourself.

## Choosing voice talent

Actors with experience in voiceover or voice character work make good custom voice talent. You can also often find suitable talent among announcers and newsreaders. Choose voice talent whose natural voice you like. It is possible to create "character" voices, but it's much harder for talent to make these consistent, and may cause voice strain.

> [!TIP]
> Generally, avoid using recognizable voices to create a custom voice—unless, of course, your goal is to produce a celebrity voice. Lesser-known voices are usually less distracting to users.

The most important single factor in choosing voice talent is consistency. Ideally, all your recordings should sound like they were made on the same day in the same room. You can partly reach this ideal through good recording practices and engineering. However, your voice talent must be able to speak with consistent rate, volume level, pitch, and tone. Clear diction is a must. Your talent also needs to be able to strictly control his or her pitch variation, emotional affect, and speech mannerisms.

Recording work for a custom voice can be more fatiguing than other kinds of voice work. Most voice talent can record for two or three hours a day. Limit sessions to three or four a week, with a day off in between if possible.

Recordings made for a voice model should be emotionally neutral. That is, a sad utterance should not be read in a sad way. Neutrality lets mood be added to the synthesized speech later. Work with your voice talent to develop a "persona" that defines the overall sound and emotional tone of the custom voice. This process will pinpoint what "neutral" sounds like for that persona.

A person may have, for example, an upbeat personality. So the character's voice may carry a note of optimism even when speaking neutrally. However, any such personality trait should be subtle and consistent. Listen to some existing TTS voices to get an idea of what you're looking for.

### Legalities

Usually, you'll want to own the voice recordings you make. Your voice talent should therefore be amenable to a work-for-hire contract for the project.

## Creating a script

The starting point of any custom voice is the script, which contains the utterances to be recorded by your voice talent.

> [!NOTE]
> The term "utterances" is used because they may or may not be full sentences. Phrases are fine.

The utterances in your script can come from anywhere: fiction, non-fiction, transcripts of speeches, news reports, and anything else available in printed form. If you want to make sure your voice does well on specific kinds of words (such as medical terminology or programming jargon), you may want to include sentences from scholarly papers or technical documents. (However, see [Legalities](#legalities) below.) You can also write your own text.

The utterances don't need come from the same source, or the same kind of source. They don't even need to have anything to do with each other. However, if you will use set phrases (for example, "You have successfully logged in") in your speech application, make sure to include them in your script. This wil give your custom voice a better chance of doing well. As a bonus, if you later decide to use the recording in place of synthesized speech, you'll have it available.

While consistency is key in choosing voice talent, variety is the hallmark of a good script. Your script should include many different words and sentences with a variety of sentence lengths, structures, and moods. Every sound in the language should be represented multiple times and in numerous contexts (called *phonetic coverage).* 

Furthermore, the text should incorporate all the ways that a particular sound can be represented in writing, and place these sounds at varying places in the sentences. Both declarative sentences and questions should be included, and read with appropriate intonation.

It's difficult to write a script that provides *just enough* data to allow the Custom Speech portal to build a good voice. In practice, the simplest way to make a script that achieves robust phonetic coverage is to increase the quantity of samples. Microsoft's standard voices were built from tens of thousands of utterances. You should be prepared to record a few to several thousand utterances to build a production-quality custom voice.

Check the script carefully for errors. If possible, have someone else check it too. When you run through the script with your talent, you'll probably catch a few more.

### Script format

You can easily create your script using Microsoft Word or Microsoft Excel. This document is for your use during the recording session, so you can set it up to suit your needs. (The Custom Voice portal requires a text file.)

The basic script format requires three columns:

* The number of the utterance, starting at 1. Numbering will make it easy for everyone in the studio to refer to a particular utterance ("let's try number 56 again").
* A blank column where you'll write in the time code of each utterance: the time at which you'll find it in the recorded audio file.
* The text of the utterance itself.

![Sample script](media/custom-voice/script.png)

Leave enough space after each row to write notes. Be sure that no utterance is split between pages. Number the pages and print your script on one side of the paper.

Print three copies of the script: one for the talent, one for the engineer, and one for the director (you). Don't staple the pages together: an experienced voice artist will take them out before recording to avoid making noise as the pages are turned.

### Legalities

Under copyright law, n actor's reading of a copyrighted work is a performance for which the author of the work may need to be compensated. This performance will not be recognizable in the final product: the custom voice. Even so, the legality of using a copyrighted work for this purpose is not well-established. Microsoft cannot provide legal advice on this issue; consult your own counsel.

Fortunately, it is easy to avoid these issues entirely. There are many sources of text you can use without permission or license.

|||
|-|-|
|[CMU Arctic corpus](http://festvox.org/cmu_arctic/)|About 1100 sentences selected from out-of-copyright works specifically for use in speech synthesis projects. An excellent starting point.|
|Works with<br>expired copyright|Typically works published prior to 1923. For English, Project Gutenberg offers tens of thousands of such works. You may want to focus on newer works, as the language will be closer to modern English.|
|Government&nbsp;works|Works created by the United States government are not copyrighted in the United States, though the government may claim copyright in other countries.|
|Public domain|Works for which copyright has been explicitly disclaimed or that have been dedicated to the public domain.|
|Permissively-licensed works|Works distributed under a license such as Creative Commons or the GNU Free Documentation License. Wikipedia uses the GFDL. Some licenses, however, may impose restrictions on performance of the licensed content that may impact the creation of a custom voice model, so read the license carefully.|

## Recording your script

Preferably, you should ecord your script at a professional recording studio that specializes in voiceover work. They'll have a recording booth, the right equipment for the job, and the right people to operate it. It pays not to skimp on recording.

Discuss your project with the studio's recording engineer and listen to his or her advice. The recording should have little or no dynamic range compression (maximum of 4:1). It is critical that the audio have consistent volume and a high signal-to-noise ratio, while being free of unwanted sounds.

### Doing it yourself

If you want to try doing the recording yourself, here are some tips.

* Your "recording booth" should be a small room with no noticeable echo or "room tone" and which is as quiet and soundproof as possible. Drapes on the walls can be used to reduce echo and neutralize or "deaden" the sound of the room.

* Use a high-quality studio condenser microphone. Sennheiser, AKG, and even newer Zoom mics can yield good results. You can rent one from a local audio-visual rental firm. Look for one with a USB interface. This type of mic conveniently combines the mic, preamp, and analog-to-digital converter into one product, simplifying hookup..

    You may also use an analog mic. Many rental houses offer "vintage" microphones. Professional analog gear includes balanced XLR connectors, rather than the 1/4" plug used in consumer equipment. So you'll need a preamp and an audio interface with these connectors.

* Install the microphone on a stand and use a pop filter on the microphone to eliminate noise from "plosive" sounds like "p" and "b." 

* The voice talent must always be the same distance from the microphone. Use tape on the floor to mark where they should stand. If the talent prefers to sit, take special care. Sitting may complicate maintaining a constant distance from the mic, and adds a new source of unwanted sounds to watch out for (the chair).

* Use a stand to hold the script; avoid angling the stand so that it can reflect sound toward the microphone.

* The person actually doing the recording—the engineer—should be in a separate room from the talent, with some way to talk to the talent in the recording booth (a "talkback").

* The recording should contain as little noise as possible, with a goal of an 80 db signal-to-noise ratio or better.

    Listen closely to a recording of silence, figure out where any noise is coming from, and eliminate the cause. Common sources of noise are air vents, fluorescent light ballasts, traffic on nearby roads, and equipment fans (even notebook PCs may have fans). Microphones and cables can pick up electrical noise from AC wiring, usually a hum or buzz.

    > [!TIP]
    > In some cases, you may be able to use an equalizer or a noise reduction software plug-in to help remove noise from your recordings, though it is always best to stop it at its source.

* Levels should be set so that most of the available dynamic range of digital recording is used without overdriving into distortion. That means loud, but not so loud that the audio distorts. Below is an example of the waveform of a good recording.

    ![good recording waveform](media/custom-voice/good-recording.png)

    You can see that most of the range (height) is used, but the highest peaks of the signal do not reach the top. You can also see that the silence in the recording is very close to being a horizontal line, indicating a low noise floor. This recording has acceptable dynamic range and signal-to-noise ratio.

* Record directly into the computer using a high-quality audio interface or a USB port, depending on the kind of mic you're using. Keep the audio chain simple: mic, preamp, audio interface, computer. Use Pro Tools if you have it available; if you're on a budget, try the free [Audacity](https://www.audacityteam.org/).

* Record at 44.1 KHz 16 bit monophonic (CD quality) or better. Current state-of-the-art is 48 KHz 24-bit, if your equipment supports it. You will downsample your audio to 16-bit 16 KHz before you submit it to the Custom Voice portal. But it it pays to have a high-quality original recording in case edits are needed.

* Ideally, have different people serve in the roles of director, engineer, and talent. Don't try to do it all yourself! In a pinch, the director and engineer could be a single person.

### Before the session

To avoid wasting studio time, run through the script with your voice talent before the session. The voice talent gets a chance to become familiar with the text, and can clarify the pronunciation of any unfamiliar words.

> [!NOTE]
> Most recording studios offer electronic display of scripts in the recording booth. In this case, take notes from your run-through directly into the script's document. You'll still want a paper copy to take notes on during the session, though. Most engineers will want a hardcopy, too. And you'll still want the third printed copy as a backup for the talent in case the copmuter is down!

Your voice talent may ask which word you want emphasized in an utterance. Actors often call this the *operative word.* You don't want any particular word emphasized, so tell them you want a natural reading. Emphasis can be added when new speech is synthesized; it should not be a part of the original recordings.

Direct the talent to pronounce words distinctly. Every word of the script should be pronounced as written. Sounds should not be omitted or slurred together, as is common in casual speech.

|Written text|Unwanted pronunciation|
|-|-|
|never going to give you up|never gonna give you up|
|there are four lights|there're four lights|
|how's the weather today|how's th' weather today|

Talent should *not* insert distinct pauses between words. The sentence should still flow naturally. This fine distinction may take some practice to get right.

### The recording session

Create a reference recording early in the session of a typical utterance and play it back to the voice talent regularly to help them keep their performance consistent. The engineer can use it as a reference for levels and overall consistency of sound. This is especially important when resuming work after a break, or on another day.

Coach your talent to take a deep breath and pause before each utterance. Record a couple of seconds of silence between utterances.

Record a good five seconds of silence before the first recording to capture the "room tone." This helps the Custom Voice portal compensate for any remaining noise in the recordings.

> [!TIP]
> All you really need is the voice talent's work, so you can record only one channel. However, if you record in stereo, the second channel can be used to record the conversation in the control room so you can refer to it later. Remove this track from the version uploaded to the Custom Voice portal.

Listen closely, using headphones, to the voice talent's performance. You're looking for good but natural diction, correct pronunciation, and a lack of unwanted sounds. Don't hesitate to ask your talent to re-record an utterance that doesn't meet these standards. Pacing should be consistent; a metronome played through the talent's headphones may be helpful if they're having trouble. Words should be pronounced the same way each time they appear.

> [!TIP] 
> If you are recording a high volume of utterances, losing a single utterance may not affect the resulting voice in any noticeable way. So it may be more expedient to simply note any utterances that have problems, exclude them from your data set, and see how your voice turns out. You can always come back to the studio and record missed utterances later.

Most studios have a large "time code" display indicating the current time in the recording. Take a note of the time on your script for each utterance. Ask the engineer if they can mark each utterance in the recording's metadata or cue sheet.

Take regular breaks to let the voice talent keep his or her voice in good shape. Provide them with something to drink to keep their throat from getting dry.

### After the session

Modern recording studios record on a computer, so at the end of the session, you receive one or more audio files, not a tape. These files will probably be WAV or AIFF format in CD quality (44.1 KHz 16-bit) or better. 48 KHz 24-bit is common. Even higher sampling rates, such as 96 KHz, are generally not needed.

The studio will probably deliver one audio file per session. To upload the recordings to the Custom Voice portal, each utterance must be in a separate file. The recording engineer may have placed a marker in the file (or provided a separate cue list) to indicate where each utterance starts. This metadata can be used to extract each utterance.

You'll need to go through the full recording and make a WAV file for each utterance. Use your notes to find the exact utterances you want, then use a sound editing utility such as [Audacity](https://www.audacityteam.org/) to copy each into a new file. 

Leave only about 0.2 seconds of silence at the beginning and end of each clip except for the first. That file should start with a full five seconds of silence. Do not use the audio editor to "zero out" silent parts of the file.

Listen to each file carefully. At this stage, you can edit out small unwanted sounds that you missed during recording—as long as it does not overlap any actual speech. If you can't fix a file, remove it from your data set entirely.

Downsample each file to 16 KHz and 16 bits before saving and, if you recorded in stereo, remove the second channel. Save each file in WAV format.

Archive the original recording in a safe place in case you need to go back to it later. Save your script and notes in a safe place, too.

### Next steps

You're reading to your recordings to create your custom voice!

> [!div class="nextstepaction"]
> [Creating custom voice fonts](how-to-customize-voice-font.md)
