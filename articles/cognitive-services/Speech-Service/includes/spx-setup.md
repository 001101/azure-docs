---
author: v-demjoh
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/15/2020
ms.author: v-demjoh
---

## Prerequisites

The only prerequisite is an Azure Speech subscription. See the [guide](../get-started.md#new-resource) on creating a new subscription if you don't already have one.

## Download and install

SPX is available on Windows and Linux. Start by downloading the [zip archive](https://aka.ms/speech/spx-zips.zip), then extract it. SPX requires either the .NET Core or .NET Framework runtime, and the following versions are supported by platform:

* Windows: [.NET Framework 4.7](https://dotnet.microsoft.com/download/dotnet-framework/net471), [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)
* Linux: [.NET Core 3.0](https://dotnet.microsoft.com/download/dotnet-core/3.0)

After you've installed a runtime, go to the root directory `spx-zips` that you extracted from the download, and extract the subdirectory that you need (`spx-net471`, for example). In a command prompt, change directory to this location, and then run `spx` to start the application.

## Create subscription config

To start using SPX, you first need to enter your Speech subscription key and region information. See the [region support](https://docs.microsoft.com/azure/cognitive-services/speech-service/regions#speech-sdk) page to find your region identifier. Once you have your subscription key and region identifier (ex. `eastus`, `westus`), run the following commands.

```shell
spx config @key --set YOUR-SUBSCRIPTION-KEY
spx config @region --set YOUR-REGION-ID
```

Your subscription authentication is now stored for future SPX requests. If you need to remove either of these stored values, run `spx config @region --clear` or `spx config @key --clear`.
