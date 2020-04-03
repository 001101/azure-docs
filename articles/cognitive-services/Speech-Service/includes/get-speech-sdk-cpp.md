---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
---

:::row:::
    :::column span="3":::
        The C++ Speech SDK is available on Windows, Linux, and macOS. For more information, see <a href="https://www.nuget.org/packages/Microsoft.CognitiveServices.Speech" target="_blank">Microsoft.CognitiveServices.Speech <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="C++" src="https://docs.microsoft.com/media/logos/logo_Cplusplus.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

### NuGet package

To install the C++ Speech SDK from the **Package Manager** run the following command.

```powershell
Install-Package Microsoft.CognitiveServices.Speech
```

### Binaries

To install the C++ Speech SDK from the binaries, download the SDK as a <a href="https://aka.ms/csspeech/linuxbinary" target="_blank">.tar package <span class="docon docon-navigate-external x-hidden-focus"></span></a> and unpack the files in a directory of your choice. The following table shows the SDK folder structure (which include header files for both x86 and x64 target architectures):

  | Path                   | Description                                          |
  |------------------------|------------------------------------------------------|
  | `license.md`           | License                                              |
  | `ThirdPartyNotices.md` | Third-party notices                                  |
  | `include`              | Header files for C++                                 |
  | `lib/x64`              | Native x64 library for linking with your application |
  | `lib/x86`              | Native x86 library for linking with your application |

  To create an application, copy or move the required binaries (and libraries) into your development environment. Include them as required in your build process.
