---
title: Azure Files Share Snapshot Overview| Microsoft Docs
description: File Share Snapshots provide a point in time state of the contents of a cloud file share. Only the incremental changes to individual files in the share will be written to the Snapshot.
services: storage
documentationcenter: .net
author: renash
manager: aungoo
editor: tysonn

ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: tamram

---

#Azure Files Share Snapshot Overview

Azure Storage provides the capability to take snapshots of file shares. Snapshots
capture the share state at that point in time. In this article, we describe
what capabilities share snapshots provide and how you can leverage them in your custom use case.


## When to use snapshots

### Protection against application error - Protection against data corruption
-------------------------------------------------------------------------

Applications using Azure file shares perform various operations such as writing, reading, storage, transmission, or processing. An application can get misconfigured
or an unintentional bug can get introduced which causes accidental overwrite or
damage to a few blocks. To protect against these scenarios, you can take a snapshot before deploying new application code and if a bug or application error is introduced with new deployment, you can go back to a previous version of your data residing on that file share. 

### Protection against human error - Protection against accidental deletions or unintended changes
----------------------------------------------------------------------------------------------

Imagine a user is working on a text file residing in a file share. Once the text file is closed, we lose
the ability to undo our changes. In these case we will need to recover a previous
version of the file. Snapshots allow you to recover previous versions of the file if it gets accidentally renamed or deleted.

### General backup purposes
---------------------------

After creating an Azure File share, you can periodically create a snapshot of
your Azure file share to use it for data backup. Fileshare snapshot when taken periodically, helps maintain previous versions of data that can be used for future audit requirement or disaster recovery.

## Snapshot Capabilities

Snapshot is a point in time read-only copy of your data. You can create, delete and manage snapshots using REST API. Same capabilities are also available in Client Library, CLI, and Azure portal. Powershell integration for snapshot is also coming soon. You can view snapshots of a share using both REST API, and SMB. Customers can retrieve the list of versions of directory or file, and they can also mount a specific version directly as a drive. Once a snapshot has been created, it can be read, copied, or deleted, but not modified. You can't copy a whole snapshot to another storage account, you have to do that file by file using azcopy or other copying mechanisms.

Snapshot capability is provided at the file share level, while
retrieval is provided at individual file level to allow for restoring individual
files. You can restore a complete file share using SMB, REST API, portal, Client
Library, or use PowerShell/CLI tooling.

A snapshot of a file share is identical to its base file share, except that the
share URI has a \*\*DateTime\*\* value appended to the share URI to indicate the
time at which the snapshot was taken. For example, if a file share URI is
http://storagesample.core.file.windows.net/myshare , the snapshot URI is
similar to
```
http://storagesample.core.file.windows.net/myshare?snapshot=2011-03-09T01:42:34.9360000Z.
```

Snapshots persist until they are explicitly deleted. A snapshot cannot outlive
its base file share. You can enumerate the snapshots associated with the base
file share to track your current snapshots. When you create a snapshot of a file
share, the files residing in share’s system properties are copied to the
snapshot with the same values. The base files and the file share’s metadata is also
copied to the snapshot, unless you specify separate metadata for the snapshot
when you create it.

You cannot delete a share that has snapshots without deleting all its snapshots first.


## Snapshot space usage 

Snapshots are incremental in nature, which means that only the data that have
changed after your most recent snapshot are saved. This minimizes the time
required to create the snapshot and saves on storage costs. Any write operation to the object or property or metadata update operation is counted towards "content changed" and will be stored in the snapshot. 

In order to conserve space, you can delete the snapshot during which period the churn was highest.

Even though snapshots are saved incrementally, the snapshot deletion process is
designed so that you need to retain only the most recent snapshot in order to
restore the share. When you delete a snapshot, only the data unique to that
snapshot is removed. Active snapshots contain all of the information needed to
browse and restore your data (from the time the snapshot was taken) to original
or alternate location. The restore can be done at item-level.

Snapshots are not counted towards your 5 TB share Limit. There is no limit to
how much space in total is occupied by snapshot. The amount of storage
space occupied by a snapshots is independent of the total limit for your storage
account. 

## Limits

Maximum number of snapshot Azure Files allows today is 200. After 200
snapshots, older snapshots have to be deleted by user in order to create new
snapshots. There is no limit to the simultaneous calls for create snapshot.
There is no limit to amount of space snapshots of a particular file share can
consume. 

## Copy data back to a share from snapshot

Copy operations involving files and snapshots follow these rules:

You can copy individual files in a file share snapshot over to its base share or
any other location. You can restore an earlier version of a file or restore complete file share by copying file by file from the snapshot. The snapshot does not get promoted to base share. The snapshot remains intact post copying, but the base file share is overwritten with a copy of the data that was available in the snapshot. All the restored files count towards "changed content".

You can copy a file in a snapshot to a destination with a different name. The
resulting destination file is a writable file and not a snapshot.

When a destination file is overwritten with a copy, any
snapshots associated with the original destination file remain intact.

## General Best Practices 

When running infrastructure on Azure, automate backups for data recovery
whenever possible. Automated actions are more reliable than manual processes,
helping to improve data protection and recoverability. You can use REST API, Client SDK or scipting for automation.

Carefully consider your snapshot frequency and retention settings before
deploying the snapshot scheduler to avoid incurring unnecessary snapshot
charges.

Snapshots provide only file level protection and any fat finger deletes on file share or storage account are not protected by snapshots . You may lock your storage account or resource group in order to protect the storage account from accidental deletions.

## Next Steps
* [How to create a file share snapshot](storage-snapshots-create.md)
* [How to list and browse snapshot](storage-snapshots-list-browse.md)
* [How to delete snapshot](storage-snapshots-delete.md)
* [Snapshot FAQ](storage-files-faq.md#snapshots)

