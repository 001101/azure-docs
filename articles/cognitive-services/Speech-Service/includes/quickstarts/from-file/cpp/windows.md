---
title: 'Quickstart: Recognize speech from an audio file, C++ (Windows) - Speech Service'
titleSuffix: Azure Cognitive Services
description: Learn how to recognize speech in C++ on Windows Desktop by using the Speech SDK
services: cognitive-services
author: wolfma61
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 08/19/2019
ms.author: wolfma
---

## Prerequisites

Before you get started, make sure to:

> [!div class="checklist"]
> * [Create an Azure Speech Resource](../../../../get-started.md)
> * [Setup your development environment](../../../../quickstarts/setup-platform.md?tabs=windows)
> * [Create an empty sample project](../../../../quickstarts/create-project.md?tabs=windows)

## Create a Visual Studio project

[!INCLUDE [Quickstart C++ project](~/includes/cognitive-services-speech-service-quickstart-cpp-create-proj.md)]

## Add sample code

1. Open the source file **helloworld.cpp**.

1. Replace all the code with the following snippet:

   ````C++

   // TODO INSERT C++ CODE HERE... Copy from "from-microphone", and paste directly here, adding AudioConfig/file name portion

   ````

1. In the same file, replace the string `YourSubscriptionKey` with your subscription key.

1. Replace the string `YourServiceRegion` with the [region](~/articles/cognitive-services/Speech-Service/regions.md) associated with your subscription (for example, `westus` for the free trial subscription).

1. From the menu bar, choose **File** > **Save All**.

## Build and run the application

1. From the menu bar, select **Build** > **Build Solution** to build the application. The code should compile without errors now.

1. Choose **Debug** > **Start Debugging** (or press **F5**) to start the **helloworld** application.

1. Speak an English phrase or sentence. The application transmits your speech to the Speech Services, which transcribes to text and sends it back to the application for display.

   ```text

   TODO INSERT CONSOLE OUTPUT HERE

   ```

## Next steps

Additional samples, such as how to read speech from an audio file, are available on GitHub.

> [!div class="nextstepaction"]
> [Explore C++ samples on GitHub](https://aka.ms/csspeech/samples)
