---
title: Use Python with Azure Data Lake Storage Gen2
description: Use the Azure Storage Client Library to interact with Azure Blob storage accounts that have a hierarchical namespace
services: storage
author: normesta
ms.service: storage
ms.date: 06/28/2019
ms.author: normesta
ms.topic: article
ms.component: data-lake-storage-gen2
---

# Use Python with Azure Data Lake Storage Gen2

This guide shows you how to use Python to interact with objects, manage directories, and set directory-level access permissions (access-control lists) in storage accounts that have a hierarchical namespace. 

To use the snippets presented in this article, you'll need to create a storage account, and then enable the hierarchical namespace feature on that account. See [Create a storage account](data-lake-storage-quickstart-create-account.md).

> [!NOTE]
> The content featured in this article uses terms such as *blobs* and *containers* instead of *files* and *file systems*. That's because Azure Data Lake Storage Gen2 is built on blob storage, and in blob storage a *file* is persisted as a *blob*, and a *file system* is persisted as a *container*. 

## Install Python and the Azure SDK for Python

See these resources:

* [Python](https://www.python.org/downloads/)

* [Azure Storage SDK for Python](https://github.com/Azure/azure-sdk-for-python)

## Add Python modules

Add these import statements to your code file.

```python
import os, uuid, sys
from azure.storage.blob import BlockBlobService, PublicAccess
from azure.storage.blob.models import PathProperties

```

### Modules featured in this snippet

> [!div class="checklist"]
> * [BlockBlobService](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blockblobservice?view=azure-python) module
> * [PublicAccess](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.models.publicaccess?view=azure-python) module
> * [PathProperties](https://www.microsoft.com) module.

## Create an instance of the Blob service

Create an instance of the [BlockBlobService](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blockblobservice.blockblobservice?view=azure-python) class. This class represents the blob service in your storage account.

```python
global block_blob_service
block_blob_service = BlockBlobService(account_name=storage_account_name, account_key=storage_account_key)
```

### APIs featured in this snippet

> [!div class="checklist"]
> * [BlockBlobService](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blockblobservice.blockblobservice?view=azure-python) class

## Perform common tasks with your files (blobs)

You can use the same set of APIs to interact with your data objects regardless of whether the account has a hierarchical namespace. To find snippets that help you perform common tasks such as creating a container (file system), uploading and downloading blobs (files), and deleting blobs and containers, see [Quickstart: Upload, download, and list blobs with Python](storage-quickstart-blobs-python.md).

The rest of this article presents snippets that help you perform tasks related only to accounts that have a hierarchical namespace.

## Add directory to a file system (container)

Add a directory by calling the [BlockBlobService.create_directory](https://www.microsoft.com) method. Pass these items as parameters to the method:

* The name of the container.

* The path of the new directory.

This example adds a directory named `my-directory` to a container named `my-file-system`. 

```python
block_blob_service.create_directory("my-file-system", "my-directory")
```

This example adds a sub-directory named `my-subdirectory` to the directory named `my-directory`.

```python
block_blob_service.create_directory("my-file-system", "my-directory/my-sub-directory")
```

### APIs featured in these snippets

> [!div class="checklist"]
> * [BlockBlobService](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blockblobservice.blockblobservice?view=azure-python) class
> * [BlockBlobService.create_directory](https://www.microsoft.com) method.

## Rename or move a directory

Move or rename a directory by calling the [BlockBlobService.rename_path](https://www.microsoft.com) method. Pass these items as parameters to the method:

* The name of the container.

* The path that you want to give the directory.

* The path of the existing directory.

This example moves a directory named `my-directory` to a sub-directory of another directory named `my-directory-2`. 

```python
block_blob_service.rename_path("my-file-system", "my-directory", "my-directory-2/my-directory") 
```

This example renames that sub-directory to `my-directory-renamed`.

```
block_blob_service.rename_path("my-file-system", "my-directory-2/my-directory", "my-directory-2/my-directory-renamed") 
```

### APIs featured in this snippet

> * [BlockBlobService](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blockblobservice.blockblobservice?view=azure-python) class
> * [BlockBlobService.rename_path](https://www.microsoft.com) method.

## Delete a directory from a file system (container)

The following example deletes a directory by calling the [BlockBlobService.delete_directory](https://www.microsoft.com) method. Pass these items as parameters to the method:

* The name of the container.

* The path of the directory that you want to delete.

This method deletes a directory named `my-sub-directory` from the `my-directory` directory.  

```python
    block_blob_service.delete_directory("my-file-system","my-directory/my-sub-directory")
```

### APIs featured in this snippet

> [!div class="checklist"]
> * [BlockBlobService](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blockblobservice.blockblobservice?view=azure-python) class
> * [BlockBlobService.service.delete_directory](https://www.microsoft.com) method.

## Get the access control list (ACL) for a directory

Get the access permissions of a directory by calling the [BlockBlobService.get_path_access_control](https://www.microsoft.com) method. Pass these items as parameters to the method:

* The name of the container.

* The path of the directory.

The following example returns a [PathProperties](https://www.microsoft.com) instance that contains the access control list (ACL) of the directory.

This example gets the ACL of the `my-directory` directory and then prints the short form of ACL to the console.

```python
    path_properties = PathProperties()
    path_properties = block_blob_service.get_path_access_control("my-file-system","my-directory")       
    print(path_properties.acl)
```

The short form of an ACL might look something like the following:

`user::rwx,group::r-x,other::---`

This string means that the owning user has read, write, and execute permissions. The owning group has only read and execute permissions. For more information about access control lists, see [Access control in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### APIs featured in this snippet

> [!div class="checklist"]
> * [BlockBlobService](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blockblobservice.blockblobservice?view=azure-python) class
> * [get_path_access_control](https://www.microsoft.com) method.

## Set the ACL for a directory

Set the access permissions of a directory by calling the [BlockBlobService.set_directory_permissions](https://www.microsoft.com) method. Pass these items as parameters to the method:

* The name of the container.

* The path of the directory.

* The short form of the desired ACL.

This example gives read access to all users.

```python
    block_blob_service.set_path_access_control(container_name, directory_name, acl='other::r--')
```

For more information about access control lists, see [Access control in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### APIs featured in this snippet

> [!div class="checklist"]
> * [BlockBlobService](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob.blockblobservice.blockblobservice?view=azure-python) class
> * [BlockBlobService.set_path_access_control](https://www.microsoft.com) method.

## Next steps

Explore more APIs in the [blob package](https://docs.microsoft.com/python/api/azure-storage-blob/azure.storage.blob?view=azure-python) section of the [Azure Client SDK for Python](https://docs.microsoft.com/python/api/overview/azure/storage/client?view=azure-python) docs.
