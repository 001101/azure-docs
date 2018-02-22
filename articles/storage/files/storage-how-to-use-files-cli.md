---
title: Managing Azure file shares with Azure CLI | Microsoft Docs
description: Learn to use the Azure CLI to manage Azure Files.
services: storage
documentationcenter: na
author: wmgries
manager: jeconnoc
editor: 

ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/22/2018
ms.author: wgries
---

# Managing Azure file shares with the CLI

[Azure Files](storage-files-introduction.md) is Microsoft's easy to use cloud file system. Azure file shares can be mounted in Windows and Windows Server. This guide walks you through the basics of working with Azure file shares using PowerShell. In this article you learn how to:

> [!div class="checklist"]
> * Create a resource group and a storage account
> * Create an Azure file share 
> * Create a directory
> * Upload a file
> * Download a file
> * Create and use a share snapshot

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

If you decide to install and use the Azure CLI locally, this guide requires that you are running the Azure CLI version 2.0.4 or later. Run **az --version** to find the version. If you need to install or upgrade, see [Install Azure CLI 2.0](/cli/azure/install-azure-cli). 


## Create a resource group
A resource group is a logical container into which Azure resources are deployed and managed. If you don't already have an Azure resource group, you can create a new one with the [az group create](/cli/azure/group#create) command. 

The following example creates a resource group named *myResourceGroup* in the *East US* location.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## Create a storage account
A storage account is a shared pool of storage in which you can deploy Azure file share, or other storage resources such as blobs or queues. A storage account can contain an unlimited number of shares, and a share can store an unlimited number of files, up to the capacity limits of the storage account.

This example creates a storage account named `mystorageaccount<random number>` and puts the name of that storage account in the variable `$STORAGEACCT`. Storage account names must be unique, so we use `$RANDOM` to append a number to the end to make it unique. 

Create a new storage account, within the resource group that you created, that will be used to host the file share using [az storage account create](/cli/azure/storage/account#create). This example creates a storage account named *mystorageaccount* using locally redundant storage. When finished, it displays information about the new storage account in a table format.

```azurecli-interactive 
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount$RANDOM \
    --location eastus \
    --sku Standard_LRS \
	--out table
```

The output should list the name of the storage account. We will be using the name of the storage account in the rest of the steps in this article, so create a variable to hold the storage account name. In this example, our storage account name is *mystorageaccount12345*.

```azurecli-interactive
STORAGEACCOUNT=mystorageaccount12345
```

### Get the storage account key
Storage account keys are used to control access to resources in a storage account. They are automatically created when you create a storage account. View the storage account keys using [az storage account keys list](/cli/azure/storage/account/keys#list). This example displays the storage account keys for *mystorageaccount12345* in table format.

```azurecli-interactive
az storage account keys list \
    --resource-group myResourceGroup \
    --account-name $STORAGEACCOUNT \
    --output table
```

We will be using the storage account key in the rest of the steps in this article, so create a variable to hold the storage account key. Replace the value of the key with your own value for **key1**. 

```azurecli-interactive
STORAGEKEY=WaAD6NOz/BYR< snip - replace with your own key >Wv1pWjGOG1Q==
```

## Create an Azure file share

Create a file share using [az storage share create](/cli/azure/storage/share#create) command. This example creates a share named `myshare`. 

Now you can create your first file share. A storage account can contain an unlimited number of shares, and a share can store an unlimited number of files, up to the capacity limits of the storage account. 

Share names need to be all lower case letters, numbers, and single hyphens but cannot start with a hyphen. For complete details about naming file shares and files, see [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Create file shares using [az storage share create](/cli/azure/storage/share#create). This example creates a share named *myshare* with a 10 GiB quota. Be sure to enter your own storage account name and key.

```azurecli-interactive
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCOUNT \
    --account-key $STORAGEKEY
```

## Create a directory
Create  a new directory named *myDirectory* at the root of your Azure file share, use the [`az storage directory create`](/cli/azure/storage/directory#az_storage_directory_create) command.

Adding a directory provides a hierarchical structure for managing your file share. You can create multiple levels, but you must ensure that all parent directories exist before creating a subdirectory. For example, for path myDirectory/mySubDirectory, you must first create directory myDirectory, then create mySubDirectory.


This example creates a directory named *myDirectory* in the file share called *myshare*.

```azurecli-interactive
az storage directory create \
   --account-name $STORAGEACCOUNT \
   --account-key $STORAGEKEY \
   --name myDirectory \
   --share-name myshare
```

## Upload a file
Upload a file to the file share using the [`az storage file upload`](/cli/azure/storage/file#az_storage_file_upload) command. In this example, we create a simple file *SampleUpload.txt* in Cloud Shell and then upload that file to the *myDirectory* directory of the *myshare* file share. 

```azurecli-interactive
date > ~/clouddrive/SampleUpload.txt

az storage file upload \
   --account-name $STORAGEACCOUNT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --source "~/clouddrive/SampleUpload.txt" \
   --path "myDirectory/SampleUpload.txt"
```

Use [`az storage file list`](/cli/azure/storage/file#az_storage_file_list) to make sure that the file was uploaded to your Azure file share.

```azurecli-interactive
az storage file list \
   --account-name $STORAGEACCOUNT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --path "myDirectory" \
   --output table
```

## Download a file

Use [`az storage file download`](/cli/azure/storage/file#az_storage_file_download) to download a copy of the file you just uploaded to your Cloud Shell.

This example downloads the *SampleUpload* file from your Azure file share to your Cloud Shell as a file named *SampleDownload.txt*.

```azurecli-interactive
az storage file download \
   --account-name $STORAGEACCOUNT \
   --account-key $STORAGEKEY \
   --share-name "myshare" \
   --path "myDirectory/SampleUpload.txt" \
   --dest "~/clouddrive/SampleDownload.txt"
```

## Copy files
Copy files from one file share to another file share, using the [az storage file copy](/cli/azure/storage/file/copy). This example creates a second Azure file share named *share2* and a new directory in that share named *myDirectory2*, then copies the file *SampleUpload.txt* as a new file named *SampleCopy.txt*.

```azurecli-interactive
az storage share create \
   --account-name $STORAGEACCOUNT \
   --account-key $STORAGEKEY \
   --name "myshare2"

az storage directory create \
   --account-name $STORAGEACCOUNT \
   --account-key $STORAGEKEY \
   --share-name "myshare2" \
   --name "myDirectory2"

az storage file copy start \
   --account-name $STORAGEACCOUNT \
   --account-key $STORAGEKEY \
   --source-share "myshare" \
   --source-path "myDirectory/SampleUpload.txt" \
   --destination-share "myshare2" \
   --destination-path "myDirectory2/SampleCopy.txt"
```

If you list the files in the new share, you should see your copied file.

```azurecli-interactive
az storage file list \
   --account-name $STORAGEACCOUNT \
   --account-key $STORAGEKEY \
   --share-name "myshare2" \
   --output table
```

While the `az storage file copy start` command is convenient for small file moves between Azure file shares and Azure Blob storage containers, we recommend AzCopy for larger moves. To learn more about AzCopy, see [AzCopy for Linux](../common/storage-use-azcopy-linux.md) and [AzCopy for Windows](../common/storage-use-azcopy.md). AzCopy must be installed locally; it is not available in Cloud Shell.

## Optional: Use snapshots
A snapshot preserves a point in time copy of an Azure file share. File share snapshots are similar to other technologies you may already be familiar with like: 
- [Volume Shadow Copy Service (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) for Windows file systems such as NTFS and ReFS
- [Logical Volume Manager (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) snapshots for Linux systems
- [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) snapshots for macOS. 

Create a share snapshot by using [az storage share snapshot](/cli/azure/storage/share#az_storage_share_snapshot).

```azurecli-interactive
SNAPSHOT=$(az storage share snapshot \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --name "myshare" | \
    jq ".snapshot" |
    tr -d '"')
```

Browse the contents of a share snapshot by passing the timestamp of the share snapshot we captured in the variable **$SNAPSHOT** to the `az storage file list` command.

```azurecli-interactive
az storage file list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --snapshot $SNAPSHOT \
    --output table
```

You can see the list of snapshots you've taken for your share.

```azurecli-interactive
az storage share list \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --include-snapshot | \
jq '.[] | select(.name == "myshare") | select(.snapshot != null) | .snapshot' | \
tr -d '"'
```

Restore a file using [az storage file copy start](/cli/azure/storage/file/copy#az_storage_file_copy_start). For the purposes of this quickstart, we'll first delete our `SampleUpload.txt` file we previously uploaded so we can restore it from the snapshot.

```azurecli-interactive
# Delete SampleUpload.txt
az storage file delete \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --share-name "myshare" \
    --path "myDirectory/SampleUpload.txt"

# Build the source URI for snapshot restore
URI=$(az storage account show \
    --resource-group "myResourceGroup" \
    --name $STORAGEACCT | \
    jq ".primaryEndpoints.file" | \
    tr -d '"')

URI=$URI"myshare/myDirectory/SampleUpload.txt?sharesnapshot="$SNAPSHOT

# Restore SampleUpload.txt from the share snapshot
az storage file copy start \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY \
    --source-uri $URI \
    --destination-share "myshare" \
    --destination-path "myDirectory/SampleUpload.txt"
```


## Clean up resources
When you are done, you can use the [`az group delete`](/cli/azure/group#delete) command to remove the resource group and all related resources. 

```azurecli-interactive 
az group delete --name "myResourceGroup"
```


## Next steps
- [Managing file shares with Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Managing file shares with the Azure portal](storage-how-to-use-files-portal.md)
- [Planning for an Azure Files deployment](storage-files-planning.md)