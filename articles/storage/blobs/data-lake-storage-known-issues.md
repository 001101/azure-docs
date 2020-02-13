---
title: Known issues with Azure Data Lake Storage Gen2 | Microsoft Docs
description: Learn about the limitations and known issues with Azure Data Lake Storage Gen2
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 01/24/2020
ms.author: normesta
ms.reviewer: jamesbak
---
# Known issues with Azure Data Lake Storage Gen2

This article lists the features and tools that are not yet supported or only partially supported with storage accounts that have a hierarchical namespace (Azure Data Lake Storage Gen2).

<a id="blob-storage-features" />

## Feature, tool and service support

[!INCLUDE [storage-data-lake-blob-feature-support](../../../includes/storage-data-lake-blob-feature-support.md)]
|||
|---|---|
|[AzCopy](../common/storage-use-azcopy-v10.md) |Version-specific support|
|[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) |Version-specific support |
|Browsing directories and files in the Azure portal |Limited support |
|Third party applications |Limited support |
|[blobfuse](storage-how-to-mount-container-linux.md)|Not yet supported|
[!INCLUDE [storage-data-lake-sdk-support](../../../includes/storage-data-lake-sdk-support.md)]
[!INCLUDE [storage-data-lake-service-ecosystem-support](../../../includes/storage-data-lake-service-ecosystem-support.md)]

<a id="life-cycle-notes" />

## Known issues with Blob features

### Lifecycle management policies

* All access tiers are supported. 

* The archive access tier is currently in preview. 

* The deletion of blob snapshots is not yet supported.  

* There are currently some bugs affecting lifecycle management policies and the archive access tier. 

<a id="diagnostic-logs-notes" />

### Diagnostic logs

Azure Storage Explorer 1.10.x can't be used for viewing diagnostic logs. To view logs, please use AzCopy or SDKs.

<a id="object-level-tiers-notes" />

### Object-level tiers

* Cool and archive tiers are supported.

* The archive tier is in preview. 

* All other access tiers are not yet supported.

* There are currently some bugs affecting the archive access tier. 

<a id="tools-and-applications" />

## Known issues with tools and applications

<a id="az-copy" />

### AzCopy

Use only the latest version of AzCopy ([AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=%2fazure%2fstorage%2ftables%2ftoc.json)). Earlier versions of AzCopy such as AzCopy v8.1, are not supported.

<a id="storage-explorer" />

### Azure Storage Explorer

Use only versions `1.6.0` or higher.There is currently a storage bug affecting version `1.11.0` that can result in authentication errors in certain scenarios. A fix for the storage bug is being rolled out, but as a workaround, we recommend that you use version `1.10.x` which is available as a [free download](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-relnotes). `1.10.x` is not affected by the storage bug.

<a id="explorer-in-portal" />

### Storage Explorer in the Azure portal

ACLs are not yet supported.

<a id="third-party-apps" />

### Third party applications

Third party applications that use REST APIs to work will continue to work if you use them with Data Lake Storage Gen2
Applications that call Blob APIs will likely work.

<a id="sdk-powershell-cli" />

## Known issues with SDK / PowerShell / CLI

<a id="api-scope-data-lake-client-library" />

### File system support in SDKs

- [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md) and [Python](data-lake-storage-directory-file-acl-python.md) support are in public preview. Other SDKs are not currently supported.
- Get and set ACL operations are not currently recursive.

### File system support in PowerShell and Azure CLI

- [PowerShell](data-lake-storage-directory-file-acl-powershell.md) and [Azure CLI](data-lake-storage-directory-file-acl-cli.md) support are in public preview.
- Get and set ACL operations are not currently recursive.

<a id="blob-apis-disabled" />

### Blob APIs

Blob APIs and Data Lake Storage Gen2 APIs can operate on the same data.

This section describes issues and limitations with using blob APIs and Data Lake Storage Gen2 APIs to operate on the same data.

* You can't use both Blob APIs and Data Lake Storage APIs to write to the same instance of a file. If you write to a file by using Data Lake Storage Gen2 APIs, then that file's blocks won't be visible to calls to the [Get Block List](https://docs.microsoft.com/rest/api/storageservices/get-block-list) blob API. You can overwrite a file by using either Data Lake Storage Gen2 APIs or Blob APIs. This won't affect file properties.

* When you use the [List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) operation without specifying a delimiter, the results will include both directories and blobs. If you choose to use a delimiter, use only a forward slash (`/`). This is the only supported delimiter.

* If you use the [Delete Blob](https://docs.microsoft.com/rest/api/storageservices/delete-blob) API to delete a directory, that directory will be deleted only if it's empty. This means that you can't use the Blob API delete directories recursively.

These Blob REST APIs aren't supported:

* [Put Blob (Page)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Put Page](https://docs.microsoft.com/rest/api/storageservices/put-page)
* [Get Page Ranges](https://docs.microsoft.com/rest/api/storageservices/get-page-ranges)
* [Incremental Copy Blob](https://docs.microsoft.com/rest/api/storageservices/incremental-copy-blob)
* [Put Page from URL](https://docs.microsoft.com/rest/api/storageservices/put-page-from-url)
* [Put Blob (Append)](https://docs.microsoft.com/rest/api/storageservices/put-blob)
* [Append Block](https://docs.microsoft.com/rest/api/storageservices/append-block)
* [Append Block from URL](https://docs.microsoft.com/rest/api/storageservices/append-block-from-url)

Unmanaged VM disks are not supported in accounts that have a hierarchical namespace. If you want to enable a hierarchical namespace on a storage account, place unmanaged VM disks into a storage account that doesn't have the hierarchical namespace feature enabled.








