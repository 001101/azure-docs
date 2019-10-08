---
title: Storage options for FSLogix profile containers - Azure
description: Options for storing your Windows Virtual Desktop FSLogix profile on Azure Storage.
services: virtual-desktop
author: Heidilohr

ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: helohr
---
# Storage options for FSLogix profile containers

Azure offers multiple storage solutions that you can use to store your FSLogix profile container. This article compares storage solutions Azure Storage offers for Windows Virtual Desktop FSLogix profile container user profiles.

Windows Virtual Desktop offers FSLogix profile containers as the recommended user profile solution. FSLogix is designed to roam profiles in remote computing environments, such as Windows Virtual Desktop. At sign in, this container is dynamically attached to the computing environment using natively supported Virtual Hard Disk (VHD) and Hyper-V virtual hard disk (VHDX). The user profile is immediately available and appears in the system exactly like a native user profile.

## Comparing Azure storage options

The following tables compare the storage solutions Azure Storage offers for Windows Virtual Desktop FSLogix profile container user profiles.

### Azure platform details

|Features|Azure Files|Azure NetApp Files|Storage Spaces Direct|
|--------|-----------|------------------|---------------------|
|Platform service|Yes, Azure-native solution|Yes, Azure-native solution|No, self-managed|
|Regional availability|Ring 0, broad availability|Ring 1, currently available in [at least nine regions](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)|Azure Compute-supported DC regions, premium disks are broadly available and recommended for Storage Spaces Direct|
|Redundancy|Locally redundant/zone-redundant/geo-redundant|Locally redundant|Locally redundant/zone-redundant/geo-redundant|
|Tiers and performance|Standard<br>Premium<br>Up to max 100k IOPS per share with 5 GBps per share at about 3 ms latency|Standard<br>Premium<br>Ultra<br>Up to 320k (16K) IOPS with 4.5 GBps per volume at about 1 ms latency|Standard HDD: up to 500 IOPS per-disk limits<br>Standard SSD: up to 4k IOPS per-disk limits<br>Premium SSD: up to 20k IOPS per-disk limits|
|Capacity|100 TiB per share|100 TiB per volume, up to 12.5 PiB per subscription|Maximum 32 TiB per disk|
|Required infrastructure|At least one virtual machine|Minimum capacity pool 4 TiB, min volume size 100 GiB|Two VMs on Azure IaaS (+ Cloud Witness) or at least three VMs without and costs for disks|
|Protocols|SMB 2.1/3. and REST|NFSv3, NFSv4.1 (preview), SMB 3.x/2.x|NFSv3, NFSv4.1, SMB 3.1|

### Azure management details

|Features|Azure Files|Azure NetApp Files|Storage Spaces Direct|
|--------|-----------|------------------|---------------------|
|Access|Cloud, on-premises and hybrid (Azure file sync)|Cloud, on-premises (via ExpressRoute)|Cloud, on-premises|
|Azure Active Directory integration|Azure Active Directory and Azure Active Directory Domain Services|Azure Active Directory Domain Services and Native Active Directory|Native Active Directory or Azure Active Directory Domain Services support only|
|Backup|Azure backup snapshot integration|Azure NetApp Files snapshots|Azure backup snapshot integration|
|Security and compliance|[All Azure supported certificates](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|ISO completed|[All Azure supported certificates](https://www.microsoft.com/trustcenter/compliance/complianceofferings)|
|Azure Active Directory integration|Azure Active Directory and Azure Active Directory Domain Services|[Azure Active Directory Domain Services and Native Active Directory](../azure-netapp-files/azure-netapp-files-faqs.md#does-azure-netapp-files-support-azure-active-directory)|Native Active Directory or Azure Active Directory Domain Services support only|

Once you've chosen your storage method, check out [Windows Virtual Desktop pricing](https://azure.microsoft.com/pricing/details/virtual-desktop/) for information about our pricing plans.