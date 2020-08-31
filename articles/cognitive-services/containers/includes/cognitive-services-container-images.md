---
title: Container repositories and images
services: cognitive-services
author: aahill
manager: nitinme
description: Two tables representing the container registries, repositories and image names for all Cognitive Service offerings.
ms.service: cognitive-services
ms.topic: include
ms.date: 04/24/2020
ms.author: aahi
---

### Container repositories and images

The tables below are a listing of the available container images offered by Azure Cognitive Services. For a complete list of all the available container image names and their available tags, see [Cognitive Services container image tags](../container-image-tags.md). 

#### Generally available 

The Microsoft Container Registry (MCR) syndicates all of the generally available containers for Cognitive Services. The containers are also available directly from the [Docker hub](https://hub.docker.com/_/microsoft-azure-cognitive-services).

**LUIS**

| Container | Container Registry / Repository / Image Name |
|--|--|
| LUIS | `mcr.microsoft.com/azure-cognitive-services/luis` |

See [How to run and install LUIS containers](../../LUIS/luis-container-howto.md) for more information.

**Text Analytics**

| Container | Container Registry / Repository / Image Name |
|--|--|
| Sentiment Analysis v3 (English) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-en` |
| Sentiment Analysis v3 (Spanish) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-es` |
| Sentiment Analysis v3 (French) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-fr` |
| Sentiment Analysis v3 (Italian) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-it` |
| Sentiment Analysis v3 (German) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-de` |
| Sentiment Analysis v3 (Chinese - simplified) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zh` |
| Sentiment Analysis v3 (Chinese - traditional) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-zht` |
| Sentiment Analysis v3 (Japanese) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-ja` |
| Sentiment Analysis v3 (Portuguese) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-pt` |
| Sentiment Analysis v3 (Dutch) | `mcr.microsoft.com/azure-cognitive-services/sentiment:3.0-nl` |

See [How to run and install Text Analytics containers](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) for more information.

**Anomaly Detector** 

| Container | Container Registry / Repository / Image Name |
|--|--|
| [Anomaly detector](../../anomaly-detector/anomaly-detector-container-howto.md) | `mcr.microsoft.com/azure-cognitive-services/anomaly-detector` |

**Speech Service**

| Container | Container Registry / Repository / Image Name |
|--|--|
| [Speech-to-text](../../speech-service/speech-container-howto.md?tab=stt) | `containerpreview.azurecr.io/microsoft/cognitive-services-speech-to-text` |
| [Custom Speech-to-text](../../speech-service/speech-container-howto.md?tab=cstt) | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-speech-to-text` |
| [Text-to-speech](../../speech-service/speech-container-howto.md?tab=tts) | `containerpreview.azurecr.io/microsoft/cognitive-services-text-to-speech` |

#### Public "Ungated" preview 

The following preview containers are available publicly. The Microsoft Container Registry (MCR) syndicates all of the publicly available ungated containers for Cognitive Services. The containers are also available directly from the [Docker hub](https://hub.docker.com/_/microsoft-azure-cognitive-services).

| Service | Container | Container Registry / Repository / Image Name |
|--|--|--|
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Key Phrase Extraction | `mcr.microsoft.com/azure-cognitive-services/keyphrase` |
| [Text Analytics](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md) | Language Detection | `mcr.microsoft.com/azure-cognitive-services/language` |


#### Public "Gated" preview

The following gated preview containers are hosted on the Microsoft Container Registry (MCR), and require an application to access. See the following container articles for more information.

| Service | Container | Container Registry / Repository / Image Name |
|--|--|--|
| [Computer Vision](../../Computer-vision/computer-vision-how-to-install-containers.md) | Read | `azure-cognitive-services/vision/read:3.0` |
| [Face](../../face/face-how-to-install-containers.md) | Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face` |
| [Form recognizer](https://go.microsoft.com/fwlink/?linkid=2083826&clcid=0x409) | Form Recognizer | `containerpreview.azurecr.io/microsoft/cognitive-services-form-recognizer` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=ctts) | Custom Text-to-speech | `containerpreview.azurecr.io/microsoft/cognitive-services-custom-text-to-speech` |
| [Speech Service API](../../speech-service/speech-container-howto.md?tab=lid) | Language Detection | `mcr.microsoft.com/azure-cognitive-services/speech/language-detection` |
| [Text Analytics for health](../../text-analytics/how-tos/text-analytics-how-to-install-containers.md?tabs=health) | Text Analytics for health | `containerpreview.azurecr.io/microsoft/cognitive-services-healthcare` |
