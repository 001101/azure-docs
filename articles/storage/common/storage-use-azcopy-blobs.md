---
title: Transfer data with AzCopy and blob storage | Microsoft Docs
description: Transfer data with AzCopy and blob storage.
services: storage
author: normesta

ms.service: storage
ms.topic: article
ms.date: 01/03/2019
ms.author: normesta
ms.subservice: common
---

# Transfer data with AzCopy and blob storage 

AzCopy is a command-line utility that you can use to copy data to, from, or between blob storage containers.

This article contains a collection of AzCopy example commands that help you create containers, copy files, and synchronize folders between local file systems and containers.

Before you begin, download AzCopy v10. Then, use the `AzCopy login` command to sign into your storage account, or, obtain a SAS token that you can append to each AzCopy command.

For guidance on accomplishing these tasks, see [Get started with AzCopy](storage-use-azcopy-v10.md).

## Create containers

Intro text

    hdfs dfs -mkdir [-p] <path>

Replace the `<path>` placeholder with the root file system name or a folder within your file system.

For example: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

outro-text

## Copy files

Basic usage

azcopy copy [source] [destination] [flags]

For detailed descriptions of each flag, type `azcopy copy`, and then press the ENTER key.

If you are using SAS, append the SAS token to all references to Blob containers. For example:

  - azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"

If you use the `AzCopy login` command to sign into your storage account, you don't have to append the SAS token to the blob URI.

This section contains the following example commands:

> [!div class="checklist"]
> * [Copy data from a local file system to a container]()
> * [Copy data from a container to a local file system]()
> * [Load the data to an Azure SQL database by using Sqoop]()

### Copy data from a local file system to a container

Intro text

    hdfs dfs -mkdir [-p] <path>

Replace the `<path>` placeholder with the root file system name or a folder within your file system.

For example: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

outro-text

### Copy data from a container to a local file system

Intro text

    hdfs dfs -mkdir [-p] <path>

Replace the `<path>` placeholder with the root file system name or a folder within your file system.

For example: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

outro-text

### Copy data from a Virtual Hard Disk (VHD) to a container

Intro text

    hdfs dfs -mkdir [-p] <path>

Replace the `<path>` placeholder with the root file system name or a folder within your file system.

For example: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

outro-text

### Copy data between containers in different storage accounts

Intro text

    hdfs dfs -mkdir [-p] <path>

Replace the `<path>` placeholder with the root file system name or a folder within your file system.

For example: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

outro-text

### Copy containers between different storage accounts

Intro text

    hdfs dfs -mkdir [-p] <path>

Replace the `<path>` placeholder with the root file system name or a folder within your file system.

For example: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

outro-text

### Synchronize a local file system with a container

Intro text

    hdfs dfs -mkdir [-p] <path>

Replace the `<path>` placeholder with the root file system name or a folder within your file system.

For example: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

outro-text

### Synchronize a container with a local file system

Intro text

    hdfs dfs -mkdir [-p] <path>

Replace the `<path>` placeholder with the root file system name or a folder within your file system.

For example: `hdfs dfs -mkdir abfs://my-file-system@mystorageaccount.dfs.core.windows.net/`

outro-text

### Synchronize data


## Copy data to Azure Storage

The following command uploads all files under the folder `C:\local\path` recursively to the container `mycontainer1`, creating `path` directory in the container. When `--put-md5` flag is provided, AzCopy calculates and stores each file's md5 hash in `Content-md5` property of the corresponding blob for later use.

```azcopy
.\azcopy cp "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true --put-md5
```

The following command uploads all files under the folder `C:\local\path` (without recursing into the subdirectories) to the container `mycontainer1`:

```azcopy
.\azcopy cp "C:\local\path\*" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --put-md5
```

To find more examples, use the following command:

```azcopy
.\azcopy cp -h
```

## Copy Blob data between two storage accounts

Copying data between two storage accounts uses the [Put Block From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, and doesn't use the client machine's network bandwidth. Data is copied between two Azure Storage servers directly, while AzCopy simply orchestrates the copy operation. This option is currently only available for Blob storage.

To copy the all of the Blob data between two storage accounts, use the following command:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

To copy a Blob container to another Blob container, use the following command:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/mycontainer/<sastoken>" "https://myotheraccount.blob.core.windows.net/mycontainer/<sastoken>" --recursive=true
```

## Copy a VHD image to a storage account

AzCopy by default uploads data into block blobs. To upload files as Append Blobs, or Page Blobs use the flag `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

```azcopy
.\azcopy cp "C:\local\path\mydisk.vhd" "https://myotheraccount.blob.core.windows.net/mycontainer/mydisk.vhd<sastoken>" --blob-type=PageBlob
```

## Sync: incremental copy and delete (Blob storage only)

The sync command synchronizes contents of a source directory to a directory in the destination, comparing file names and last modified timestamps. This operation includes the optional deletion of destination files if those do not exist in the source when the `--delete-destination=prompt|true` flag is provided. By default, the delete behavior is disabled. 

> [!NOTE] 
> Use the `--delete-destination` flag with caution. Enable the [soft delete](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) feature before you enable delete behavior in sync to prevent accidental deletions in your account. 
>
> When `--delete-destination` is set to true, AzCopy will delete files that do not exist in the source from destination without any prompt to the user. If you want to be prompted for confirmation, use `--delete-destination=prompt`.

To sync your local file system to a storage account, use the following command:

```azcopy
.\azcopy sync "C:\local\path" "https://account.blob.core.windows.net/mycontainer1<sastoken>" --recursive=true
```

You can also sync a blob container down to a local file system:

```azcopy
# The SAS token isn't required for Azure Active Directory authentication.
.\azcopy sync "https://account.blob.core.windows.net/mycontainer1" "C:\local\path" --recursive=true
```

This command incrementally syncs the source to the destination based on the last modified timestamps. If you add or delete a file in the source, AzCopy will do the same in the destination. Before deletion, AzCopy will prompt you to confirm.

## More examples

See these articles:

- [Transfer data with AzCopy and Azure Data Lake Storage Gen2](storage-use-azcopy-data-lake-gen2.md)

- [Transfer data with AzCopy and file storage](storage-use-azcopy-files.md)

- [Transfer data with AzCopy and Amazon S3 buckets](storage-use-azcopy-s3.md)