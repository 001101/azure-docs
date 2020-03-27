---
 title: include file
 description: include file
 services: virtual-machines
 author: roygara
 ms.service: virtual-machines
 ms.topic: include
 ms.date: 03/27/2020
 ms.author: rogarana
 ms.custom: include file
---

This article explains how to either upload a VHD from your local machine to an Azure managed disk or copy a managed disk to another region.

Previously, you had to follow an involved process that included staging your data in a storage account and managing that storage account. Now, all you need to do is create an empty managed disk and upload a VHD directly to it. This new simplified process of uploading VMs to Azure also enables you to upload a VHD up to 32 TiB directly into a large managed disk or easily copy your managed disks across Azure regions.

If you're providing a backup solution for IaaS VMs in Azure, we recommend using direct upload to restore customer backups to managed disks. When uploading a VHD from a machine external to Azure, speeds would depend on your local bandwidth. When uploading or copying from an Azure VM, then your bandwidth would be the same as standard HDDs.

Currently, direct upload is supported for standard HDD, standard SSD, and premium SSD managed disks. It isn't supported for ultra disks, yet.