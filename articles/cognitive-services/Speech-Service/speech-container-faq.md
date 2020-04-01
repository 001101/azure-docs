---
title: Speech service containers frequently asked questions (FAQ)
titleSuffix: Azure Cognitive Services
description: Install and run speech containers. speech-to-text transcribes audio streams to text in real time that your applications, tools, or devices can consume or display. Text-to-speech converts input text into human-like synthesized speech.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: dapine
---

# General questions

**Q: How do containers work and what is the best way to set them up?**

**A:** When setting up the production cluster, there are several things to consider. 
First, setting up single language, multiple containers, on the same machine, should not be a big issue. If you are hitting problems there, it may be hardware related so we would love to have the CPU and memory specs (lscpu and free commands output). Let me explain:

The `ja-JP` container is on our latest model. Acoustic model is the most demanding piece CPU-wise, while language model demands the most memory. When we benchmarked the use, it takes about 0.6 CPU cores to process a single speech-to-text request when audio is flowing in at real-time (like from the microphone). If you are feeding audio faster than real-time (like from a file), that usage can double (1.2 cores). Meanwhile, the memory listed below (in Tiger's email) is operating memory for decoding speech. It does *not* take into account the actual full size of the language model, which will reside in file cache. For ja-JP that's additional 2 GB; for `en-US`, it may be more (6-7 GB).

If you have a machine where memory is scarce, and you are trying to deploy multiple languages on it, it is entirely possible that file cache is completely full, and OS is forced to page models in and out. For a running transcription, that could be disastrous, and may lead to slowdowns you described below.

Furthermore, we prepackage executables for machines with AVX2 instructions set. A machine with AVX512 instruction set will require code generation for that target, and starting 10 containers for 10 languages may temporarily exhaust CPU. A message like this one will appear in the docker logs:

2020-01-16 16:46:54.981118943 [W:onnxruntime:Default, tvm_utils.cc:276 LoadTVMPackedFuncFromCache] Cannot find Scan4_llvm__mcpu_skylake_avx512 in cache, using JIT...

Finally, you can set how many decoders you want inside a *single* container using `DECODER MAX_COUNT`.

So, basically, we should start with your SKU (CPU/memory), and we can suggest how to get the best out of it.

**Q: Why is punctuation is missing from the transcription?**

**A:** The `speech_recognition_language=<YOUR_LANGUAGE>` should be explicitly configured in the request if they are using Carbon client.

For example:

```python
if not recognize_once(
    speechsdk.SpeechRecognizer(
        speech_config=speechsdk.SpeechConfig(
            endpoint=template.format("interactive"),
            speech_recognition_language="ja-JP"),
            audio_config=audio_config)):

    print("Failed interactive endpoint")
    exit(1)
```
Here is the output:

```
RECOGNIZED: SpeechRecognitionResult(
    result_id=2111117c8700404a84f521b7b805c4e7, 
    text="まだ早いまだ早いは猫である名前はまだないどこで生
          まれたかとんと見当を検討をなつかぬ。何でも薄暗いじめじめした所でながら泣いていた事だけは記憶している。まだは今ここで初めて人間と言うも
          のを見た。しかも後で聞くと、それは書生という人間中で一番同額同額。",
    reason=ResultReason.RecognizedSpeech)
```

**Q: When using the speech-to-text service, I'm seeing this error - why?**

```
Error in STT call for file 9136835610040002161_413008000252496:
{
    "reason": "ResultReason.Canceled",
    "error_details": "Due to service inactivity the client buffer size exceeded. Resetting the buffer. SessionId: xxxxx..."
}
```

**A:** This typically happens when you feed the audio faster than SR container can take it. Client buffers fill up, and the cancellation is triggered. you need to control the concurrency AND the RTF at which you send the audio.

**Q: When testing the text-to-speech container, I'm experiencing issues with the C++ examples - why?**

**A:** If the container version is older than 1.3, then this code should be used:

```cpp
const auto endpoint = "http://localhost:5000/speech/synthesize/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Older containers don't have the required endpoint for Carbon to work with the `FromHost` API. If the containers used for version 1.3, then this code should be used:

```cpp
const auto host = "http://localhost:5000";
auto config = SpeechConfig::FromHost(host);
config->SetSpeechSynthesisVoiceName("Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text1}}}").get();
```

Below is an example of using the `FromEndpoint` API:

```cpp
const auto endpoint = "http://localhost:5000/cognitiveservices/v1";
auto config = SpeechConfig::FromEndpoint(endpoint);
config->SetSpeechSynthesisVoiceName("Microsoft Server Speech Text to Speech Voice (en-US, Jessa24kRUS)");
auto synthesizer = SpeechSynthesizer::FromConfig(config);
auto result = synthesizer->SpeakTextAsync("{{{text2}}}").get();
```

 The `SetSpeechSynthesisVoiceName` function is called because, the containers with an updated text-to-speech engine require the voice name to be set.

**Q: How to use a custom acoustic model and language model along with container services options in Azure. (Currently able to pass only one model ID either custom language model or custom acoustic model)?**

**A:** Decision to not support both acoustic and language models concurrently until a unified id is created to reduce API breaks. So unfortunately this is not supported now.

**Q: What are these errors when using Custom-speech-to-text container?**

```
Failed to fetch manifest: Status: 400 Bad Request Body:
{
    "code": "InvalidModel",
    "message": "The specified model is not supported for endpoint manifests."
}
```

**A:** It looks like you're training with the latest custom model, which we currently don't support. If you train with an older version it should be possible to use. We are still working on supporting the latest versions.

Essentially, the custom containers do not support Halide or ONNX-based acoustic models (which is the default in the custom training portal). This is due to custom models are not encrypted and we do not want to expose ONNX models.
Language models are fine.
Customer need to explicitly select an order non-ONNX model for custom training. Accuracy will not be affected. Model size may be larger (by 100MB).

Support model > 20190220 (v4.5 Unified)

**Q: Failure with custom speech-to-text container?**

```
HTTPAPI result code = HTTPAPI_OK.
HTTP status code = 400.
Reason:  Synthesis failed.
StatusCode: InvalidArgument,
Details: Voice does not match.
```

**A:** Need to provide the correct voice name in the request for it to work
it might be case sensitive, if not try the full service name mapping.

You have to use `en-US-JessaRUS`, as `en-US-JessaNeural` is not available right now in container version of Text-to-speech.

**Q: Failure with custom speech-to-text container?**

```
{
    "code": "InvalidProductId",
    "message": "The subscription SKU \"CognitiveServices.S0\" is not supported in this service instance."
}
```

**A:** You reed to create a Speech resource (not a cognitive service resource).

**Q: Do you support punctuation?**

**A:** We have capitalization (ITN) in the on-prem container. Punctuation is language dependent, and not supported for some languages, including Chinese and Japanese.

We *do* have the implicit, basic punctuation support in the existing containers, but it is off by default. What that means is that you can get `.` character in your example, but not `。`. To enable that logic, here's what you would do in Python using our Speech SDK (it would be very similar in other languages):

```python
speech_config.set_service_property(
    name='punctuation',
    value='implicit',
    channel=speechsdk.ServicePropertyChannel.UriQueryParameter
)
```

**Q: What API protocol do you support, REST or WS?**

**A:** For speech containers, we currently only support the websocket based protocol, the SDK only supports calling in WS but not REST. There's a plan to add REST support, but not ETA for the moment.

**Q: Capacity planning & cost estimation of on-prem Speech container solution?**

**A:** For container capacity in batch processing mode, each decoder could handle 2-3x in real time, with 2 CPU cores, for a single recognition. We do not recommend keeping more than 2 concurrent recognitions per container instance, but recommend running more instances of containers for reliability/availability reasons, behind a load balancer.

Though we could have each container instance running with more decoders. For example, we may be able to set up 10 decoders per container instance on an 8 core machine, to handle 20x. (there's a param `DECODER_MAX_COUNT`). For the extreme case, reliability and latency suffers, with throughput increased significantly. For a microphone, it will be at 1x real-time. The overall usage should be at about 1 core for a single recognition.

For scenario of processing 1K hours/day in batch processing mode, in an extreme case, 3 VMs could handle it within 24 hours but not guaranteed. To handle spike days, failover, update, and to provide minimum backup/BCP, we recommend 4-5 machines instead of 3 per cluster, and with 2+ clusters.

For hardware, we use standard Azure VM `DS13_v2` as a reference (each core must be 2.6GHz or better, with AVX2 instruction set enabled).

| Instance | vCPU(s) | RAM | Temp storage | Pay as you go<br>with AHB | 1 year reserve with<br>AHB (% Savings) | 3 year reserved with<br>AHB (% Savings) |
|----------|---------|-----|--------------|------------------------|---|---|
| `DS13 v2` | 8 | 56 GiB | 112 GiB   | $0.598/hour            | $0.3528/hour (~41%) | $0.2333/hour (~61%) |

Based on the design reference (2 clusters of 5 VMs to handle 1K hours/day audio batch processing), 1-year hardware cost will be:

> 2 (clusters) * 5 (VMs per cluster) * $0.3528/hour * 365 (days) * 24 (hours) = $31K / year

When mapping to physical machine, a general estimation is 1 vCPU = 1 Physical CPU Core. In reality, 1vCPU is actually more powerful than a single core.

For on-prem, all of these additional factors come into play:

- On what type the physical CPU is and how many cores on it
- How many CPUs running together on the same box/machine
- How VMs are set up
- How hyper-threading / multi-threading is used
- How memory is shared
- The OS, etc.

Normally it is not as well tuned as Azure the environment. Considering other overhead, I would say a safe estimation is 10 physical CPU cores = 8 Azure vCPU. Though popular CPUs only have 8 cores. With on-prem deployment, the cost will be higher than using Azure VMs. This also depends on depreciation rate.

Service cost is same as the online service: $1/hour for speech-to-text.
So speech service cost is:

> $1 * 1000 * 365 = $365K

Maintain cost paid to MS depends on the service level and content of the service. It various from $29.99/month for basic level to hundreds of thousands if onsite service involved. A rough number is $300/hour for service/maintain. People cost is not included. Other infrastructure cost (such as storage, network, load balancer) are not included.

**Q: Could not run container on CentOS**

**A:** CentOS 7 is not supported by Python SDK yet.
19.04 ubuntu is not supported yet

Referring to prerequisites: https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-python#prerequisites

The Python Speech SDK package is available for these operating systems:
- **Windows** - x64 and x86
- **Mac** - macOS X version 10.12 or later
- **Linux** - Ubuntu 16.04, Ubuntu 18.04, Debian 9 on x64

https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=dotnet%2Cwindows%2Cjre&pivots=programming-language-python

so 18.04 is the recommended ubuntu version as of now.

**Q: How to use V1.8 SDK with Speech container?**

**A:** There's a new `FromHost` API. This does not replace or modify any existing APIs. It just adds an alternative way to create a speech config using a custom host.

Host API descriptions
•	C++ : FromHost https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig (find FromHost on the page)
•	C# : FromHost https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-dotnet
•	Java : fromHost https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromhost?view=azure-java-stable
•	Objective-C : initWithHost https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration (find initWithHost on the page)
•	Python : SpeechConfig with host parameter https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python (note, as of this writing the page has not been updated yet)
•	JavaScript : not supported at the moment and no schedule for it

Parameters: host (mandatory), subscription key (optional, if you can use the service without it).

Format for host is "protocol://hostname:port" where ":port" is optional (see below).
•	If the container is running locally, the hostname is "localhost".
•	If the container is running on a remote server, use the hostname or IPv4 address of that server.

Host parameter examples:
•	"ws://localhost:5000" - non-secure connection to a local container using port 5000
•	"ws://some.host.com:5000" - non-secure connection to a container running on a remote server

"ws://" = non-secure websocket connection, default port is 80
"wss://" = secure websocket connection, default port is 443
(this is for STT; text-to-speech would use http or https instead)

Python samples as above, but use 'host' parameter instead of 'endpoint' e.g.

    speech_config = speechsdk.SpeechConfig(host="ws://localhost:5000")

**Q: How to use V1.7 SDK with Speech container?**

**A:** There are 3 endpoints at Speech Container for different usages
Interactive: 
•	Meant for command and control scenarios.
•	Has a segmentation timeout value of X.
•	At the end of one recognized utterance, the service stops processing audio from that request ID and ends the turn. The connection is not closed.
•	Maximum limit for recognition is 20s.
•	Typical Carbon call to invoke is `RecognizeOnceAsync`

Conversation:
•	Meant for longer running recognitions.
•	Has a segmentation timeout value of Y. (Y != X)
•	Will process multiple complete utterances without ending the turn.
•	Will end the turn for too much silence.
o	Carbon will continue with a new request ID and replaying audio as needed.
•	The service will forcibly disconnect after 10 minutes of speech recognition.
o	Carbon will reconnect and replay unacknowledged audio.
•	Invoked in Carbon with `StartContinuousRecognition`

Dictation: 
•	Allows users to specify punctuation by speaking it.
•	Invoked in Carbon by specifying `EnableDictation` on the `SpeechConfig` object regardless of the API call that starts recognition.
•	The 1st party cluster returns speech.fragment messages for intermediate results, the 3rd party return speech.hypothesis messages.

They are for different purpose and are used differently.

Python basic samples:
https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py
•	for single recognition (interactive mode) with a custom endpoint (i.e. SpeechConfig with an endpoint parameter), see speech_recognize_once_from_file_with_custom_endpoint_parameters()
•	for continuous recognition (conversation mode), see (and just modify to use a custom endpoint as above) speech_recognize_continuous_from_file()
•	to enable dictation in samples like above (only if you really need it), right after you create speech_config, add code
o	speech_config.enable_dictation()

Had no time for refs to C# samples now, but the speech config method to enable dictation is .EnableDictation()

Endpoint API descriptions
•	C++ : FromEndpoint https://docs.microsoft.com/cpp/cognitive-services/speech/speechconfig (find FromEndpoint on the page)
•	C# : FromEndpoint https://docs.microsoft.com/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-dotnet
•	Java : fromEndpoint https://docs.microsoft.com/java/api/com.microsoft.cognitiveservices.speech.speechconfig.fromendpoint?view=azure-java-stable
•	Objective-C : initWithEndpoint https://docs.microsoft.com/objectivec/cognitive-services/speech/spxspeechconfiguration (find initWithEndpoint on the page)
•	Python : SpeechConfig with endpoint parameter https://docs.microsoft.com/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig?view=azure-python
•	JavaScript : fromEndpoint, the doc pages seemsto be temporarily offline

Q:
I am using the LUIS container in an IoT Edge deployment and am attempting to call the LUIS prediction endpoint from another container. The LUIS container is listening on port 5001, and the URL I'm using is this:
var config = SpeechConfig.FromEndpoint(new Uri($"ws://192.168.1.91:5001/luis/prediction/v3.0/apps/{luisAppId}/slots/production/predict"));

The error I'm getting is this:
WebSocket Upgrade failed with HTTP status code: 404 SessionId: 3cfe2509ef4e49919e594abf639ccfeb
I see the request in the LUIS container logs and the message says: "The request path /luis//predict" does not match a supported file type".
What does this mean? What am I missing?
I was following the example for the Speech SDK, from here:
https://github.com/Azure-Samples/cognitive-services-speech-sdk
The scenario is that we are detecting the audio directly from the PC microphone and trying to determine the intent, based on the LUIS app we trained. The example I linked to does exactly that. And it works very well with the LUIS cloud-based service. Using the Speech SDK seemed to save us from having to make a separate explicit call to the speech-to-text API and then a second call to LUIS.
So, all I am attempting to do is switch from the scenario of using LUIS in the cloud to using the LUIS container. I can't imagine if the Speech SDK works for one, it won't work for the other.

**A:**
The Speech SDK should not be used against a LUIS container. For using the LUIS container, the LUIS SDK or LUIS REST API should be used. Speech SDK should be used against a speech container.

A cloud is different than a container. A cloud can be composed of multiple aggregated containers (sometimes called micro services). So there is a LUIS container and then there is a Speech container -- 2 separate containers. The Speech container only does speech. The LUIS container only does LUIS. In the cloud, because both containers are known to be deployed, and it is bad performance for a remote client to go to the cloud, do speech, come back, then go to the cloud again and do LUIS, we provide a feature that allows the client to go to Speech, stay in the cloud, go to LUIS then come back to the client. Thus even in this scenario the Speech SDK goes to Speech cloud container with audio, and then Speech cloud container talks to LUIS cloud container with text. The LUIS container has no concept of accepting audio (it would not make sense for LUIS container to accept streaming audio -- LUIS is a text based service). With on-prem, we have no certainty our customer has deployed both containers, we don't presume to orchestrate between containers in our customers prem, and if both containers are deployed on-prem, given they are more local to the client, it is not a burden to go the SR first, back to client, and have the customer then take that text and go to LUIS.
TODO: sample code needed here to show how to use Speech container + LUIS container sequentially.

**Q: Running on Mac (macOS + container + Python SDK), help?**

When we send a wav file to be transcribed, the result comes back with:
recognition is running....
Speech Recognition canceled: CancellationReason.Error
Error details: Timeout: no recognition result received.
When creating a websocket connection from the browser a test, we get:
wb = new WebSocket("ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1")
WebSocket {url: "ws://localhost:5000/speech/recognition/dictation/cognitiveservices/v1", readyState: 0, bufferedAmount: 0, onopen: null, onerror: null, …}
So we know the websocket is setup correctly.

**A:**
If that is the case then
https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310
We have a work around
https://github.com/Azure-Samples/cognitive-services-speech-sdk/issues/310#issuecomment-527542722

Carbon fixed at V1.8.

Q: differences of the endpoints.
Could you help fill the following test metrics, including what functions to test, and how to test (SDK/REST)?
Esp. "interactive" & "conversation" which I did not see from existing doc/sample.

Endpoints	Functions to Test	SDK	REST API
/speech/synthesize/cognitiveservices/v1
Synthesize Text (text-to-speech)			Y
/speech/recognition/dictation/cognitiveservices/v1
The cognitive services on-prem dictation v1 websocket endpoint		Y	N
/speech/recognition/interactive/cognitiveservices/v1
The cognitive services on-prem interactive v1 websocket endpoint			
/speech/recognition/conversation/cognitiveservices/v1
The cognitive services on-prem conversation v1 websocket endpoint			

**A:**
This is a horrible fusion of:
•	People trying the dictation endpoint for containers. (I'm not sure how they got that URL)
•	The 1st party endpoint being the one in a container.
•	The 1st party endpoint returning speech.fragment messages instead of the speech.hypothesis messages the 3rd part endpoints return for the dictation endpoint.
•	The Carbon quickstarts all use RecognizeOnce (Interactive mode)
•	Carbon having an Assert that for speech.fragment messages requiring they aren't returned in Interactive Mode.
•	Carbon having the Asserts fire in Release builds (killing the process)

The workaround is either switch to using continuous recognition in your code, or (quicker) connect to either the interactive or continuous endpoints in the container.
For your code, set the endpoint to <host:port>/speech/recognition/interactive/cognitiveservices/v1

Interactive: 
•	Meant for command and control scenarios.
•	Has a segmentation timeout value of X.
•	At the end of one recognized utterance, the service stops processing audio from that request ID and ends the turn. The connection is not closed.
•	Maximum limit for recognition is 20s.
•	Typical Carbon call to invoke is "RecognizeOnceAsync()"

Conversation:
•	Meant for longer running recognitions.
•	Has a segmentation timeout value of Y. (Y != X)
•	Will process multiple complete utterances without ending the turn.
•	Will end the turn for too much silence.
o	Carbon will continue with a new request ID and replaying audio as needed.
•	The service will forcibly disconnect after 10 minutes of speech recognition.
o	Carbon will reconnect and replay unacknowledged audio.
•	Invoked in Carbon with "StartContinuousRecognition()"

Dictation: 
•	Allows users to specify punctuation by speaking it.
•	Invoked in Carbon by specifying "EnableDictation()" on the SpeechConfig object regardless of the API call that starts recognition.
•	The 1st party cluster returns speech.fragment messages for intermediate results, the 3rd party return speech.hypothesis messages.

The proper fix is coming with SDK 1.8, which has on-prem support (will pick the right endpoint, so we will be no worse than online service). In the meantime, there is a sample for continuous recognition, why don't we point to it?

https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/6805d96bf69d9e95c9137fe129bc5d81e35f6309/samples/python/console/speech_sample.py#L196

I've already mentioned to Rob that we need to support RecognizeAll(), which is a call Google supports for these types of scenarios (recognition from file).


**Q: I thought I read somewhere that the non-batch API could only handle audio <15 seconds long.  I'll work with the SDK and get it running.**

**A:** This is in interactive mode. If you use dictation or conversation that is not a problem.

**Q: Few more applicative/proper use questions**

1. API to use, we have wav files at various lengths, spanning from few seconds to dozens of seconds, should we use - Interactive/conversation/dictation for such type?
2. should we use : StartContinuousRecognitionAsync or RecognizeOnceAsync ?
in our tests we used: StartContinuousRecognitionAsync & conversation

**A:** here's a 5 min quickstart using Python. You can find the other languages nearby on that web site:

https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-python

Just to clarify for the interactive/conversation/dictation: this is an advanced way of specifying the particular way in which our service will handle the speech request. Unfortunately, for the on-prem containers we have to specify the full URI (since it includes local machine), so this information leaked from the abstraction. We are working with the SDK team to make this more usable in the future.
Q: we are trying to help the team capacity plan for hardware by benchmarking a rough measure of transactions/second/core.

**A:** Here are some of the rough numbers to expect from existing model (will change for the better in the one we will ship in GA):

•	For files, the throttling will be in the speech SDK, at 2x. First 5s of audio are not throttled. Decoder is capable of doing about 3x real-time. For this, the overall CPU usage will be close to 2 cores for a single reco.
•	For mic, it will be at 1x real-time. The overall usage should be at about 1 core for a single reco.

This can all be verified from the docker logs. We actually dump the line with session and phrase/utterance statistics, and that includes the RTF numbers.

**Q: How many concurrent requests will 4 core , 4 GB RAM handle.  If we have to serve for e.g. 50 concurrent requests, how many Core and RAM is recommended?**

**A:** 
at real-time, 8 with our latest en-US, use more docker containers beyond 6 concurrent requests

(gets crazier beyond 16 cores, and it becomes numa node sensitive)

For resource requirement per container instance:
Container	Minimum	Recommended
cognitive-services-speech-to-text	2 core
2-GB memory	4 core
4-GB memory
•	Each core must be at least 2.6 gigahertz (GHz) or faster. 
•	For files, the throttling will be in the speech SDK, at 2x. First 5s of audio are not throttled.
•	Decoder is capable of doing about 2-3x real-time. For this, the overall CPU usage will be close to 2 cores for a single reco. That's why I mentioned "do not recommend keep more than 2 active connections per container instance". To extreme it, you may try put about 10 decoders at 2x real-time in an 8 core machine like DS13_V2, for the new 1.3 container coming out soon in 1-2 weeks. There's a param you could try: "DECODER_MAX_COUNT=20"
•	For mic, it will be at 1x real-time. The overall usage should be at about 1 core for a single reco.
 
How many hours audio do you have in total?
If the number is big, to improve reliability/availability, I would suggest running more instances of containers, either on a single box or on multiple boxes, behind a load balancer.

Orchestration could be done by K8S+Helm or docker-comp.
 
As an example, to handle 1000 hours/24 hours, we have tried setting up 3-4 VMs, with 10 instances/decoders per VM.

**Q: My current plan is to take an existing audio file and split it up into 10 second chunks and send those through the container. Is that an acceptable scenario?  Is there a better way to process larger audio files with the container?**

**A:** Just use the speech SDK and give it the file, it will do the right thing. Why do you need to chunk the file?

**Q: How do I make multiple containers run on the same host? The doc says to expose a different port, which I do, but luis container when deployed is still listening on 5000?**

**A:** Try `-p <outside_unique_port>:5000`. For example, `-p 5001:5000`.

**Q: I'm working on getting a DBE demo stood up that shows video/audio transcription.  I'm working with the speech-to-text container and I'm getting 404 errors when I try and post data to its endpoints.?**

```http
POST /speech/recognition/conversation/cognitiveservices/v1?language=en-US&format=detailed HTTP/1.1
Accept: application/json;text/xml
Content-Type: audio/wav; codecs=audio/pcm; samplerate=16000
Transfer-Encoding: chunked
User-Agent: PostmanRuntime/7.18.0
Cache-Control: no-cache
Postman-Token: xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Host: 10.0.75.2:5000
Accept-Encoding: gzip, deflate
Content-Length: 360044
Connection: keep-alive
HTTP/1.1 404 Not Found
Date: Tue, 22 Oct 2019 15:42:56 GMT
Server: Kestrel
Content-Length: 0
```

**A:** We do not support REST API in container, we only support Websockets through SpeechSDK.
Please refer to the docs https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-container-howto#speech-to-text-2


# Technical questions

## Next steps

