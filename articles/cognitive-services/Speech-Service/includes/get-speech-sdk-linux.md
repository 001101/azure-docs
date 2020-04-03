---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: dapine
---

:::row:::
    :::column span="3":::
        Currently, the Speech SDK only supports **Ubuntu 16.04**, **Ubuntu 18.04**, **Debian 9**, **Red Hat Enterprise Linux (RHEL) 8**, and **CentOS 8** on the following target architectures:
        - x86 (Debian/Ubuntu), x64, ARM32 (Debian/Ubuntu), and ARM64 (Debian/Ubuntu) for C++ development
        - x64, ARM32 (Debian/Ubuntu), and ARM64 (Debian/Ubuntu) for Java
        - x64 for .NET Core and Python
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Android" src="https://docs.microsoft.com/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

For a native applications, the Speech SDK relies on `libMicrosoft.CognitiveServices.Speech.core.so`. Make sure the target architecture (x86, x64) matches the application. Depending on the Linux version, additional dependencies may be required.

- The shared libraries of the GNU C library (including the POSIX Threads Programming library, `libpthreads`)
- The OpenSSL library (`libssl.so.1.0.0` or `libssl.so.1.0.2`)
- The shared library for ALSA applications (`libasound.so.2`)

# [Ubuntu](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2
```

# [Debian 9](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2
```

# [RHEL / CentOS 8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum install alsa-lib openssl
```

> [!TIP]
> On RHEL/CentOS 8, follow the instructions on [how to configure OpenSSL for Linux](../how-to-configure-openssl-linux.md).

---

### Java

[!INCLUDE [get-speech-sdk-dotnet](get-speech-sdk-java.md)]

### C++

[!INCLUDE [Get Speech SDK C++](get-speech-sdk-cpp.md)]
