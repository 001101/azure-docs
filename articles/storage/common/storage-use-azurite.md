---
title: Use the Azurite open-source emulator for blob storage development and testing (preview)
description: The Azurite open-source emulator (preview) provides a free local environment for testing your Azure Blob storage applications.
services: storage
author: mhopkins-msft

ms.service: storage
ms.topic: article
ms.date: 06/12/2019
ms.author: mhopkins
ms.subservice: common
---

# Use the Azurite open-source emulator for blob storage development and testing (preview)

The Azurite open-source emulator (preview) provides a free local environment for testing your Azure Blob storage applications. When you're satisfied with how your application is working, switch to using an Azure Storage account in the cloud. The emulator provides cross-platform experiences on Windows, Linux, and MacOS. Azurite version 3 supports most of the APIs implemented by the Azure Blob Storage service. <!-- Which APIs are NOT supported? -->

## Prerequisites

* [GitHub account](https://github.com)
* [Git](https://git-scm.com/)
* [Node.js version 8.0 or later](https://nodejs.org)

## Install Azurite

To get the Azurite emulator on your local system, clone the GitHub repository for the project by using the following console command.

```console
git clone https://github.com/Azure/Azurite.git
```

After cloning the source code, build and install Azurite by using **npm**, which is the package management tool included with every Node.js installation. Execute following **npm** commands from the local root directory of the cloned repo.

```console
npm install
npm run build
npm install -g azurite
```

## Command-line syntax

```console
azurite [--blobHost <IP address>] [--blobPort <port address>] [-l | --location <workspace path>] [-s | --silent] [-d | --debug <log file path>]
```

The **-l** switch is a shortcut for **--location**, **-s** is a shortcut for **--silent**, and **-d** is a shortcut for **--debug**.

### Supported command-line options

This section details the command line switches available when launching Azurite v3.

#### Listening host

**Optional** By default, Azurite v3 will listen to 127.0.0.1 as the local server. Use the **--blobHost** switch to set the address to your requirements.

Accept requests on the local machine only:

```console
azurite --blobHost 127.0.0.1
```

Allow remote requests (may be unsafe):

```console
azurite --blobHost 0.0.0.0
```

#### Listening port configuration

**Optional** By default, Azurite v3 will listen for the blob service on port 10000. You can customize the listening port for your own requirements.

> [!WARNING]
> After using a customized port, you need to update the connection string or corresponding configuration in your Azure Storage tools or SDKs.

Customize the Blob Service listening port:

```console
azurite --blobPort 8888
```

Let the system auto select an available port:

```console
azurite --blobPort 0
```

> [!NOTE]
> The port in use is displayed on Azurite startup.

#### Workspace path

**Optional** Azurite v3 needs to store metadata and binary data to the local disk during execution. You can provide a customized path as the workspace location. By default, the current process working directory will be used.

```console
azurite -l c:\azurite
```

```console
azurite --location c:\azurite
```

#### Access log

**Optional** By default the access log is displayed in the console window. Disable it by using the **--silent** switch:

```console
azurite -s
```

```console
azurite --silent
```

#### Debug log

**Optional** Debug log includes detailed information on every request and exception stack traces. Enable the debug log by providing a valid local file path.

```console
azurite -d path/debug.log
```

```console
azurite --debug path/debug.log
```

## Run Azurite

Start Azurite with the following command:

```console
azurite --silent --location c:\azurite --debug c:\azurite\debug.log
```

This command tells Azurite to store all data in a particular directory, **c:\azurite**. If the **--location** option is omitted, it will use the current working directory.

## Authentication

Azurite v3 supports SharedKey, account Shared Access Signature (SAS), Service SAS, and Public Container Access authentications. You may use any Azure Storage SDKs or tools like Storage Explorer to connect Azurite v3 with any authentication strategy. Authentication is required in Azurite v3.

### Default Storage Account

Azurite v3 provides support for a General Storage Account v2 and associated features by default.

* Account name: `devstoreaccount1`
* Account key: `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

> [!NOTE]
> In addition to SharedKey authentication, Azurite v3 supports account and service SAS authentication. Anonymous access is also available when a container is set to allow public access.

### Connection string

Typically, you can pass following connection strings to SDKs or tools (like Azure CLI2.0 or Storage Explorer). Using the blob service as an example, the full connection string is:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
```

Or if the SDK or tools support following short connection string:

```
UseDevelopmentStorage=true;
```

### Storage Explorer

Connect to Azurite by clicking the **Add Account** icon, then select **Attach to a local emulator** and click **Connect**.

## Differences between Azurite and Azure Storage

Because it runs as a local instance, there are functional differences between Azurite and an Azure storage account in the cloud.

### Storage accounts

Azurite v3 supports a default account as General Storage Account v2 and provides features.

* Account name: `devstoreaccount1`
* Account key: `Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==`

### Endpoint and connection URL

The service endpoints for Azurite are different from the endpoints of an Azure storage account. The local computer doesn't do domain name resolution, requiring Azurite endpoints to be local addresses.

When you address a resource in an Azure storage account, use the following scheme. The account name is part of the URI host name. The resource being addressed is part of the URI path:

`<http|https>://<account-name>.<service-name>.core.windows.net/<resource-path>`

For example, the following URI is a valid address for a blob in an Azure storage account:

`https://myaccount.blob.core.windows.net/mycontainer/myblob.txt`

Since the local computer doesn't do domain name resolution, the account name is part of the URI path instead of the host name. Use the following URI format for a resource in Azurite:

`http://<local-machine-address>:<port>/<account-name>/<resource-path>`

For example, the following address might be used for accessing a blob in Azurite:

`http://127.0.0.1:10000/myaccount/mycontainer/myblob.txt`

The service endpoints for Azurite blob service:

`http://127.0.0.1:10000/<account-name>/<resource-path>`

### Scaling and performance

Azurite doesn't support many concurrent clients. There's no performance guarantee. Performance depends highly on the environment in which Azurite is deployed.

### Error handling

We've tried to align Azurite with Azure Storage error handling logic, but there are differences. For example, error messages may be different, while error status code will align.

### API version strategy

We strive to make Azurite v3 compatible with Azure Storage APIs:

* An Azurite v3 instance has a baseline Azure Storage API version.
  * A Swagger definition with the same API version will be used to generate protocol layer APIs and interfaces.
  * Azurite should implement all the possible features provided in this API service version.
* If an incoming request has the same API version Azurite provides, Azurite should handle the request with parity to Azure Storage.
* If an incoming request has a higher API version than Azurite, Azurite will return a **VersionNotSupportedByEmulator** error (HTTP status code 400 - Bad Request).
* If an incoming request has a lower API version header, the emulator attempts to handle the request with the Azurite baseline API version behavior.
* Azurite will return API version in the response header as the baseline API version

### RA-GRS

Azurite supports read-access geo-redundant replication (RA-GRS). For storage resources, you can access the secondary location by appending -secondary to the account name. For example, the following address might be used for accessing a blob using the read-only secondary in Azurite:

`http://127.0.0.1:10000/devstoreaccount1-secondary/mycontainer/myblob.txt`

## Azurite v3 is open-source

Contributions and suggestions for Azurite v3 are welcome. Go to the Azurite [GitHub project](https://github.com/Azure/Azurite/projects) page or [GitHub issues](https://github.com/Azure/Azurite/issues) for milestones and work items we are tracking for upcoming features and bug fixes. Detailed work items are also tracked in GitHub.

See the [Contribution](https://github.com/Azure/Azurite/blob/master/CONTRIBUTION.md) file for ways to participate. You can also open GitHub issues voting for any missing features in Azurite v3.

Most contributions require you to agree to a Contributor License Agreement (CLA). A CLA declares that you have the right to, and actually do, grant us the rights to use your contribution. For details, see [Microsoft Contributor License Agreement](https://cla.microsoft.com).

When you submit a pull request, a CLA-bot will automatically determine whether you need to provide a CLA. The bot will add a label or comment to the pull request. Follow the instructions provided by the bot. You'll only need to provide a CLA once across all our repos.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/). For more information, see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with additional questions or comments.

## Next steps

* [Azure Storage samples using .NET](../storage-samples-dotnet.md) contains links to several code samples you can use when developing your application.
