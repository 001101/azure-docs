---
title: How to use Azure Blob storage from .NET
description: Use the Azure Storage Client Library for .NET to interact with Azure Blob storage
services: storage
author: normesta
ms.service: storage
ms.date: 03/21/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
---

# How to use Blob storage from .NET

This guide shows you how to interact with blobs by using .NET. It contains snippets that help you get started with common tasks such as uploading and downloading blobs. It also contains snippets that showcase common tasks with a hierarchical file system.

## Create a storage account

To create a storage account, see [Create a storage account](../common/storage-quickstart-create-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Enable a hierarchical namespace if you want to use the code snippets in this article that perform operations on a hierarchical file system.

![Enabling a hierarchical namespace](media/storage-dot-net-how-to-use-blobs/enable-hierarchical-namespace.png)

## Set up your development environment

What you install depends on the operating system that you are running on your development computer.

### Windows

* Install [.NET Core for Windows](https://www.microsoft.com/net/download/windows) or the [.NET Framework](https://www.microsoft.com/net/download/windows) (included with Visual Studio for Windows)

* Install [Visual Studio for Windows](https://www.visualstudio.com/). If you are using .NET Core, installing Visual Studio is optional. 

* Install the [Azure Storage APIs for .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet).

### Linux

* Install [.NET Core for Linux](https://www.microsoft.com/net/download/linux)

* Optionally install [Visual Studio Code](https://www.visualstudio.com/) and the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp&dotnetid=963890049.1518206068)

* Install the [Azure Storage APIs for .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet).

### macOS

* Install [.NET Core for macOS](https://www.microsoft.com/net/download/macos).

* Optionally install [Visual Studio for Mac](https://www.visualstudio.com/vs/visual-studio-mac/)

* Install the [Azure Storage APIs for .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet).

## Add library references to your code file

Add these using statements to your code file.

```cs
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
```

## Create a container and perform other blob operations

See [Quickstart: Use .NET to create a blob in object storage](storage-quickstart-blobs-dotnet.md) to find snippets that help you accomplish these common blob-related tasks:

> [!div class="checklist"]
> * Connect to your storage account 
> * Create a container
> * Upload blobs to a container
> * List blobs in a container
> * Download blobs from a container
> * Delete blobs from a container
> * Delete a container

## Add directory to a container

Intro text here

```cs
Snippet here
```

### APIs featured in this snippet

> [!div class="checklist"]
> * [Type](url) method.
> * [Type](url) method.
> * [Type](url) method.
> * [Type](url) method.

## Rename or move a directory

Intro text here

```cs
Snippet here
```

### APIs featured in this snippet

> [!div class="checklist"]
> * [Type](url) method.
> * [Type](url) method.
> * [Type](url) method.
> * [Type](url) method.

## Delete a directory from a container

Intro text here

```cs
Snippet here
```

### APIs featured in this snippet

> [!div class="checklist"]
> * [Type](url) method.
> * [Type](url) method.
> * [Type](url) method.
> * [Type](url) method.

## Get the ACL for a directory

Intro text here

```cs
Snippet here
```

### APIs featured in this snippet

> [!div class="checklist"]
> * [Type](url) method.
> * [Type](url) method.
> * [Type](url) method.
> * [Type](url) method.

## Set the ACL for a directory

Intro text here

```cs
Snippet here
```

### APIs featured in this snippet

> [!div class="checklist"]
> * [Type](url) method.
> * [Type](url) method.
> * [Type](url) method.
> * [Type](url) method.

## Next steps

Explore more APIs in the [Microsoft.WindowsAzure.Storage.Blob](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob?view=azure-dotnet) namespace of the [Azure Storage APIs for .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage?view=azure-dotnet) docs.