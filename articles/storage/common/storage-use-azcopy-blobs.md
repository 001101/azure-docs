---
title: Transfer data with AzCopy and blob storage | Microsoft Docs
description: This article contains a collection of AzCopy example commands that help you create containers, copy files, and synchronize folders between local file systems and containers.
services: storage
author: normesta
ms.service: storage
ms.topic: article
ms.date: 01/03/2019
ms.author: normesta
ms.subservice: common
---

# Transfer data with AzCopy and blob storage 

AzCopy is a command-line utility that you can use to copy data to, from, or between storage accounts.

This article contains a collection of AzCopy example commands. You can use them to create containers, upload files, download files, copy files, and synchronize folders.

> [!IMPORTANT]
> Before you begin, download AzCopy v10. Then, use the `AzCopy login` command to sign into your storage account, or, obtain a SAS token that you can append to each AzCopy command.
>
>For guidance on accomplishing these tasks, see [Get started with AzCopy](storage-use-azcopy-v10.md).

## Create containers

Use this command to create a blob container.

```
azcopy make "https://<storage-account-name>.blob.core.windows.net/<container-name>"
```

Example:

`azcopy make "https://mystorageaccount.blob.core.windows.net/mycontainer"`

## Upload files

You can use AzCopy to upload files and folders from your local computer or a Virtual Hard Disk (VHD).

### Upload a file

Use this command to upload a file from your local computer to a blob in a container.

```
azcopy cp <local-file-path> https://<storage-account-name>.blob.core.windows.net/<container-name>/<blob-name>
```

Example:

`azcopy copy "C:\myFolder\myTextFile.txt" "https://mystorageaccount.blob.core.windows.net/mycontainer1/myTextFile.txt"`

> [!TIP]
> If you append the `--put-md5` flag to this command, AzCopy will calculate each file's md5 hash code, and then store that code in the `Content-md5` property of each corresponding blob for later use.

### Upload a directory

### Upload files by using wildcard characters

### Upload files and directories by using wildcard characters

### Upload data from a VHD

## Download files

### Copy data from a container to a local file system

## Copy files

### Copy data between containers in different storage accounts

### Copy containers between different storage accounts

## Synchronize files

### Synchronize a local file system with a container

### Synchronize a container with a local file system

## Raw material

### Copy data to Azure Storage

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

### Copy Blob data between two storage accounts

Copying data between two storage accounts uses the [Put Block From URL](https://docs.microsoft.com/rest/api/storageservices/put-block-from-url) API, and doesn't use the client machine's network bandwidth. Data is copied between two Azure Storage servers directly, while AzCopy simply orchestrates the copy operation. This option is currently only available for Blob storage.

To copy the all of the Blob data between two storage accounts, use the following command:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/<sastoken>" "https://myotheraccount.blob.core.windows.net/<sastoken>" --recursive=true
```

To copy a Blob container to another Blob container, use the following command:
```azcopy
.\azcopy cp "https://myaccount.blob.core.windows.net/mycontainer/<sastoken>" "https://myotheraccount.blob.core.windows.net/mycontainer/<sastoken>" --recursive=true
```

### Copy a VHD image to a storage account

AzCopy by default uploads data into block blobs. To upload files as Append Blobs, or Page Blobs use the flag `--blob-type=[BlockBlob|PageBlob|AppendBlob]`.

```azcopy
.\azcopy cp "C:\local\path\mydisk.vhd" "https://myotheraccount.blob.core.windows.net/mycontainer/mydisk.vhd<sastoken>" --blob-type=PageBlob
```

### Sync: incremental copy and delete (Blob storage only)

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