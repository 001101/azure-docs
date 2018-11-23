---
title: Migrate from Azure Germany storage resources to global Azure
description: This article provides information about migrating storage resources from Azure Germany to global Azure.
author: gitralf
services: germany
cloud: Azure Germany
ms.author: ralfwi 
ms.service: germany
ms.date: 08/15/2018
ms.topic: article
ms.custom: bfmigrate
---

# Migration from Azure Germany storage resources to global Azure

This article has information that can help you migrate Azure storage resources from Azure Germany to global Azure.

## Blobs

AzCopy is a free tool you can use to copy blobs, files, and tables. AzCopy works from Azure to Azure, from on-premises to Azure, and from Azure to on-premises. Use AzCopy for your migration to copy blobs directly from Azure Germany to global Azure.

If you have nonmanaged disks for your source VM, use AzCopy to copy the .vhd files to the target environment. Otherwise, you must complete some steps in advance. For more information, see [Recommendations for managed disks](#managed-disks).

The following example shows how AzCopy works. For a complete reference, see the [AzCopy documentation](../storage/common/storage-use-azcopy.md).

AzCopy uses the terms **Source** and **Dest**, expressed as URIs. URIs for Azure Germany always have this format:

```http
https://<storageaccountname>.blob.core.cloudapi.de/<containername>/<blobname>
```

URIs for global Azure always have this format:

```http
https://<storageaccountname>.blob.core.windows.net/<containername>/<blobname>
```

You get the three parts of the URI (*storageaccountname*, *containername*, *blobname*) from the portal, by using PowerShell, or by using the Azure CLI. The name of the blob can be part of the URI or it can be given as a pattern, like *vm121314.vhd*.

You also need the storage account keys to access the storage account. Get them from the portal, PowerShell, or the CLI. For example:

```powershell
Get-AzureRmStorageAccountKey -Name <saname> -ResourceGroupName <rgname>
```

As always, you need only one of the two keys for each storage account.

Example:
URI part | example value
-------- | --------------
Source storageAccount | `migratetest`
Source container | `vhds`
Source blob | `vm-121314.vhd`
Target storageAccount | `migratetarget`
Target container | `targetcontainer`

This command copies a virtual hard disk from Azure Germany to global Azure (keys are shortened to improve readability):

```cmd
azcopy -v /source:https://migratetest.blob.core.cloudapi.de/vhds /sourcekey:"0LN...w==" /dest:https://migratetarget.blob.core.windows.net/targetcontainer /DestKey:"o//ucDi5TN...w==" /Pattern:vm-121314.vhd
```

To get a consistent copy of the VHD, shut down the VM before copying and plan some downtime. When the VHD is copied, [rebuild your VM in the target environment](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

For more information, see these articles:
- [AzCopy documentation](../storage/common/storage-use-azcopy.md)
- [Create a VM from restored disks](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks)

## Managed Disks

Azure Managed Disks simplifies disk management for Azure infrastructure as a service (IaaS) VMs by managing the storage accounts that are associated with the VM disk. Because you don't have direct access to the .vhd file, you can't directly use tools like AzCopy (see [Storage migration](#blobs)) to copy your files. The workaround is to first export the managed disk by getting a temporary SAS URI, and then download it or copy it with this information. Here's a short example how to get the SAS URI and what to do with it:

### Step 1: Get the SAS URI

1. In the portal, search for your managed disk. (It's in the same resource group as your VM. The resource type is **Disk**).
1. On the **Overview** page, select the **Export** button in the top ribbon (you have to shut down and deallocate your VM first, or unattach it).
1. Define a time when the URI expires (the default is 3,600 seconds).
1. Generate a URL (this should take only a few seconds).
1. Copy the URL (it appears only once).

### Step 2: AzCopy

For examples of how to use AzCopy, see [Blob migration](#blobs). Use AzCopy (or another tool) to copy the disk directly from your source environment to the target environment. For AzCopy, you have to split the URI into the base URI and the SAS part. The SAS part of the URI begins with the character "**?**". The portal provides this URI for the SAS URI:

```http
https://md-kp4qvrzhj4j5.blob.core.cloudapi.de/r0pmw4z3vk1g/abcd?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D>
```

The following commands show the source parameters for AzCopy:

```cmd
/source:"https://md-kp4qvrzhj4j5.blob.core.cloudapi.de/r0pmw4z3vk1g/abcd"
```

```cmd
/sourceSAS:" ?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D"
```

Here's the complete command line:

```cmd
azcopy -v /source:"https://md-kp4qvrzhj4j5.blob.core.cloudapi.de/r0pmw4z3vk1g/abcd" /sourceSAS:"?sv=2017-04-17&sr=b&si=22970153-4c56-47c0-8cbb-156a24b6e4b5&sig=5Hfu0qMw9rkZf6mCjuCE4VMV6W3IR8FXQSY1viji9bg%3D" /dest:"https://migratetarget.blob.core.windows.net/targetcontainer/newdisk.vhd" /DestKey:"o//ucD... Kdpw=="
```

### Step 3: Create a new managed disk in the target environment

You have several options for creating a new managed disk. Here's how to do it in the Azure portal:

1. In the portal, select **New** > **Managed Disk** > **Create**.
1. Give the new disk a name.
1. Select a resource group as you usually do.
1. Under **Source type**, select **Storage blob**. Then, either copy the destination URI from the AzCopy command, or browse to select the destination URI.
1. If you copied an OS disk, select the OS type. For other disk types, select **Create**.

### Step 4: Create the VM

Again, there are various ways to create a VM with this new managed disk.

In the portal, select the disk, and then select **Create VM**. Define the other parameters of your VM as usual.

To create a VM in PowerShell, see [Create a VM from restored disks](../backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## Next steps

- Export to disk [via API](/rest/api/compute/disks/grantaccess) by getting an SAS URI. 
- Create a managed disk [via API](/rest/api/compute/disks/createorupdate#create_a_managed_disk_by_importing_an_unmanaged_blob_from_a_different_subscription.) from an unmanaged blob.
