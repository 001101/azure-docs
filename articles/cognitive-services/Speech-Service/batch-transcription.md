---
title: How to use Batch Transcription - Speech Services
titlesuffix: Azure Cognitive Services
description: Batch transcription is ideal if you want to transcribe a large quantity of audio in storage, such as Azure Blobs. By using the dedicated REST API, you can point to audio files with a shared access signature (SAS) URI and asynchronously receive transcriptions.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: panosper
ms.custom: seodec18
---

# Why use Batch transcription?

Batch transcription is ideal if you want to transcribe a large quantity of audio in storage, such as Azure Blobs. By using the dedicated REST API, you can point to audio files with a shared access signature (SAS) URI and asynchronously receive transcriptions.

## Prerequisites

### Subscription Key

As with all features of the Speech service, you create a subscription key from the [Azure portal](https://portal.azure.com) by following our [Get started guide](get-started.md). If you plan to get transcriptions from our baseline models, creating a key is all you need to do.

>[!NOTE]
> A standard subscription (S0) for Speech Services is required to use batch transcription. Free subscription keys (F0) will not work. For additional information, see [pricing and limits](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/speech-services/).

### Custom models

If you plan to customize acoustic or language models, follow the steps in [Customize acoustic models](how-to-customize-acoustic-models.md) and [Customizing language models](how-to-customize-language-models.md). To use the created models in batch transcription you need their model IDs. This ID is not the endpoint ID that you find on the Endpoint Details view, it is the model ID that you can retrieve when you select the details of the models.

## The Batch Transcription API

The Batch Transcription API offers asynchronous speech-to-text transcription, along with additional features. It is a REST API that exposes methods for:

1. Creating batch processing requests
1. Query Status
1. Downloading transcriptions

> [!NOTE]
> The Batch Transcription API is ideal for call centers, which typically accumulate thousands of hours of audio. The API is guided by a "fire and forget" philosophy, which makes it easy to transcribe large volumes of audio recordings.

### Supported formats

The Batch Transcription API supports the following formats:

| Format | Codec | Bitrate | Sample Rate |
|--------|-------|---------|-------------|
| WAV | PCM | 16-bit | 8 or 16 kHz, mono, stereo |
| MP3 | PCM | 16-bit | 8 or 16 kHz, mono, stereo |
| OGG | OPUS | 16-bit | 8 or 16 kHz, mono, stereo |

For stereo audio streams, the Batch transcription API splits the left and right channel during the transcription. The two JSON files with the result are each created from a single channel. The timestamps per utterance enable the developer to create an ordered final transcript. The following JSON shows a sample request, includuing properties for setting up the profanity filter, the punctuation model, and word level timestamps.

### Configuration

Parameters for the batch transcription are supplied in JSON formatted:

```json
{
  "recordingsUrl": "<URL to the Azure blob to transcribe>",
  "models": ["<optional acoustic model ID>, <optional language model ID>"],
  "locale": "<local to us, for example en-US>",
  "name": "<user define name of the transcription batch>",
  "description": "<optional description of the transcription>",
  "properties": {
    "ProfanityFilterMode": "Masked",
    "PunctuationMode": "DictatedAndAutomatic",
    "AddWordLevelTimestamps" : "True"
  }
}
```

> [!NOTE]
> The Batch Transcription API uses a REST service for requesting transcriptions, their status, and associated results. You can use the API from any language. The next section describes how the API is used.

### Configuration properties

| Parameter | Description | Required / Optional |
|-----------|-------------|---------------------|
| `ProfanityFilterMode` | Specifies how to handle profanity in recognition results. Accepted values are `none` which disables profanity filtering, `masked` which replaces profanity with asterisks, `removed` which removes all profanity from the result, or `tags` which adds "profanity" tags. The default setting is `masked`. | Optional |
| `PunctuationMode` | Specifies how to handle punctuation in recognition results. Accepted values are `none` which disables punctuation, `dictated` which implies explicit punctuation, `automatic` which lets the decoder deal with punctuation, or `dictatedandautomatic` which implies dictated punctuation marks or automatic. | Optional |
 | `AddWordLevelTimestamps` | Specify if word level timestamps should be added to the output. Accepted values are `true` which enables word level timestamps and `false` (the default value) to disable it. | Optional |

## Sample code

The complete sample is available in the [GitHub sample repository](https://aka.ms/csspeech/samples) inside the `samples/batch` subdirectory.

You have to customize the sample code with your subscription information, the service region, the SAS URI pointing to the audio file to equires transcribe, and model IDs in case you want to use a custom acoustic or language model. 

```cs
        // Replace with your subscription key
        private const string SubscriptionKey = "<YourSubscriptionKey>";

        // Update with your service region
        private const string HostName = "<YourServiceRegion>.cris.ai";
        private const int Port = 443;

        // recordings and locale
        private const string Locale = "en-US";
        private const string RecordingsBlobUri = "URI pointing to an audio file stored Azure blob";

        // For usage of baseline models, no acoustic and language model needs to be specified.
        private static Guid[] modelList = new Guid[0];

        // For use of specific acoustic and language models:
        // - comment the previous line
        // - uncomment the next lines and create an array containing the guids of your required model(s)
        // private static Guid AdaptedAcousticId = new Guid("<id of the custom acoustic model>");
        // private static Guid AdaptedLanguageId = new Guid("<id of the custom language model>");
        // private static Guid[] modelList = new[] { AdaptedAcousticId, AdaptedLanguageId };
```

The sample code will setup the client and submit the transcription request. It will then poll for status information and print details about the transription progress.

```cs

            // get all transcriptions for the user
            transcriptions = await client.GetTranscriptionAsync().ConfigureAwait(false);

            // for each transcription in the list we check the status
            foreach (var transcription in transcriptions)
            {
                switch(transcription.Status)
                {
                    case "Failed":
                    case "Succeeded":
                        // we check to see if it was one of the transcriptions we created from this client.
                        if (!createdTranscriptions.Contains(transcription.Id))
                        {
                            // not created from here, continue
                            continue;
                        }

                        completed++;

                        // if the transcription was successful, check the results
                        if (transcription.Status == "Succeeded")
                        {
                            var resultsUri = transcription.ResultsUrls["channel_0"];
                            WebClient webClient = new WebClient();
                            var filename = Path.GetTempFileName();
                            webClient.DownloadFile(resultsUri, filename);
                            var results = File.ReadAllText(filename);
                            Console.WriteLine("Transcription succeeded. Results: ");
                            Console.WriteLine(results);
                        }
                        break;

                    case "Running":
                        running++;
                        break;

                    case "NotStarted":
                        notStarted++;
                        break;
                    }
                }
            }
        }
```

For full details about the preceding calls, see our [swagger document](https://westus.cris.ai/swagger/ui/index). For the full sample shown here, go to [GitHub](https://aka.ms/csspeech/samples) in the `samples/batch` subdirectory.

Take note of the asynchronous setup for posting audio and receiving transcription status. The client that you create is a .NET HTTP client. There's a `PostTranscriptions` method for sending the audio file details and a `GetTranscriptions` method for receiving the results. `PostTranscriptions` returns a handle, and `GetTranscriptions` uses it to create a handle to get the transcription status.

The current sample code doesn't specify a custom model. The service uses the baseline models for transcribing the file or files. To specify the models, you can pass on the same method as the model IDs for the acoustic and the language model.

> [!NOTE]
> For baseline transcriptions, you don't have to declare the ID of the baseline models. If you specify onlay a language model id (and no acoustic model id), we will pick automatically a matching acoustic model. If you only specify an acoustic model, we will pick a matching language model.

### Supported storage

Currently, the only storage supported is Azure Blob storage.

## Download the sample

You can find the sample in the `sample/batch` directory in the [GitHub sample repository](https://aka.ms/csspeech/samples).

> [!NOTE]
> Batch transcription jobs are scheduled on a best effort basis, there is no time estimate when a job will change into the running state. Once in running state, the actual transcription is processed faster than the audio real time.

## Next steps

* [Get your Speech trial subscription](https://azure.microsoft.com/try/cognitive-services/)
