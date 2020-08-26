---
title: Select a protocol
description: Select a protocol before creating an Azure file share
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 08/25/2020
ms.author: rogarana
ms.subservice: files
---

# Azure file share protocols

Azure Files offers two protocols for connecting and mounting your Azure file shares. Server Message Block (SMB) and Network File System (NFS) (preview). Azure Files does not currently support multi-protocol access, so a share can only be either an NFS share, or an SMB share. Due to this, we recommend determining which protocol best suits your needs before creating Azure file shares.

## Differences at a glance

|Feature  |NFS (preview)  |SMB  |
|---------|---------|---------|
|Access protocols     |NFS 4.1         |SMB 2.1, SMB 3.0, REST         |
|Supported OS     |Linux kernel version 4.3+         |Windows 2008 R2+, Linux kernel version 4.11+         |
|Available tiers     |Premium storage         |Premium storage, standard storage         |
|Replication     |LRS, ZRS         |LRS, ZRS, GRS         |
|Authentication     |Host-based authentication only        |Identity-based authentication, user-based authentication         |
|Permissions     |UNIX-style permissions         |NTFS-style permissions         |
|File system semantics     |POSIX compliant         |Not POSIX compliant         |
|Case sensitivity     |Case sensitive         |Not case sensitive         |
|Hard link support     |Supported         |Not supported         |
|Symbolic links support     |Supported         |Not supported         |
|Deleting or modifying open files     |Supported         |Not supported         |
|Locking     |Byte-range advisory network lock manager         |Supported         |
|Public IP allowlisting | Not supported | Supported|
|Protocol interop| Not supported | File-REST|

## SMB shares

SMB description here.

### Features

- Azure file sync
- Identity-based authentication
- Azure Backup support
- Snapshots
- Soft delete
- Encryption-in-transit and encryption-at-rest

### Use cases

SMB with Azure Files is ideal for:

- Windows environments
- Customers that require any of the features listed in [Features](#features)
- Production environments (NFS is currently in preview)

Do not use SMB if you need Unix style permissions (UID/GID), case sensitivity, or POSIX delete behavior.

## NFS shares (preview)

Azure Files offers NFS v4.1 protocol that is fully managed, network-attached storage. It is highly scalable, highly durable, and highly available. This is a fully POSIX-compliant offer that is a standard across variants of Unix and other *nix based operating systems. This enterprise-grade file storage service scales up to meet your storage needs and can be accessed concurrently by thousands of compute instances. You can start with a file system that contains only 100 GiB data to 100 TiB per volume. Moreover, your data and metadata are protected with encryption at rest by default. NFS is currently in preview and should not be used for production data.

### Limitations

- If the majority of your requests are metadata centric, then the latency will be worse when compared to open/close operations.
- Encryption-in-transit is not currently available
- Must create a new storage account in order to create an NFS share.
- Does not currently support storage explorer, Data Box, or AzCopy.

Also, the following Azure Files features are not available with NFS shares:

- Azure File Sync
- Identity-based authentication
- Azure Backup support
- Snapshots
- Soft delete

#### Regional availability

- Australia East 
- East US
- East US 2
- Southeast Asia
- UK South
- West Europe
- West US 2

### Best suited

NFS with Azure Files is ideal for:

- Linux environments.
- SAP

#### Container storage 

Containers deliver "build once, run anywhere" capabilities that enable developers to accelerate innovation. For the containers that access raw data at every start, a shared file system allows these containers to access the file system no matter which instance they run on. NFS is ideal for container storage because it provides persistent shared access to file data and has low attach-detach latencies.

#### Enterprise Applications (Databases, CRM, LOB apps) 

With high scalability, elasticity, availability, and persistence, NFS can be used to store the files of enterprise applications and the applications delivered as services.   

Many More 

Web applications, DevOps, HPC, Log Directories, Video Streaming etc. Basically, any application ever written for Linux, assumes POSIX semantics. NFS is the native choice for any Linux applications. 

- Linux-centric workloads that do not require SMB access.
- Inherent locking system that you do not need to manage.