---
title: Assessment calculations in Azure Migrate | Microsoft Docs
description: Provides an overview of assessment calculations in the Azure Migrate service.
services: migrate
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
editor: ''

ms.assetid: 39a63769-31eb-49f9-9089-4d3e4e88a412
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/25/2017
ms.author: raynew

---
# Assessment calculations

[Azure Migrate](migrate-overview.md) assesses on-premises workloads for migration to Azure. This article provides information about how assessments are calculated.



## Overview

An Azure Migrate assessment has three stages. The first assessment stage is a suitability analysis, followed by performance-based sizing estimations, and lastly, a monthly cost estimation. A machine only moves along to a later stage if it passes the previous one. For example, if a machine fails the Azure suitability check, it’s marked as unsuitable for Azure, and sizing and costing estimation stages aren’t done. 


## Azure suitability analysis

VMs that you migrate to Azure must meet Azure requirements and limitations. For example, if an on-premises VM disk is more than 1024 GB, it can't be hosted on Azure. Here's what Azure Migrate checks. If a machine complies with all these checks, it's marked as suitable for migrationVMs you want to migrate must meet Azure requirements and limitations. For example, if an on-premises VM disk is more than 4 TB, it can't be hosted on Azure. The checks needed for compliance are summarized in the table.

**Check** | **Details**
--- | ---
**Compute: Boot** | The disk boot type must be BIOS, and not UEFI.
**Compute: Cores** | The number of cores in the machines must be equal to (or less than) the maximum number of cores (32) supported for an Azure VM.<br/><br/> If performance history is available, Azure Migrate considers the utilized cores for comparison. If a comfort factor is specific in the assessment settings, the number of utilized cores is multipied with the comfort factor.<br/><br/> If there's no performance history available, Azure Migrate uses the allocated cores, without applying a comfort factor.
**Compute: Memory** | The machine memory size must be equal to (or less than) the maximum memory (448 GB) allowed for an Azure VM. <br/><br/> If performance history is available, Azure Migrate considers the utilized memory for comparison. If a comfort factor is specified in the assessment settings, the utilized memory is multiplied with the comfort factor.<br/><br/> If there's no history the allocated memory is used, without applying a comfort factor.<br/><br/> 
**Compute: OS Windows Server 2003-2008** | 32-bit and 64-bit support.<br/><br/> Best effort support only.
**Compute: OS Windows Server 2008 R2 and SPs** | 64-bit support.<br/><br/> Fully supported.
**Compute: OS Windows Client 7 and later** | 64-bit support.<br/><br/> Supported with Visual Studio subscription only.
**Linux** | 64-bit support for these [operating systems](../virtual-machines/linux/endorsed-distros.md)<br/><br/> Fully supported.
**Storage disk** | Allocated size of a disk must be 4 TB (4096 GB) or less, including the OS disk.<br/><br/> [Learn about](../azure-subscription-service-limits.md#storage-limits) Azure disk limits per disk and per VM.<br/><br/> Azure Migrate supports [managed disks](../virtual-machines/windows/managed-disks-overview.md) only.
**Networking** | The machines must have 32 or less NICs attached.


## Performance-based sizing

After a machine is marked as suitable for Azure, Azure Migrate maps it to a VM size in Azure, using the following criteria:

- **Storage check**: Azure Migrate tries to map every disk attached to the machine to a disk in Azure:
    - Only managed disks are currently supported.
    - Azure Migrate multiplies the I/O per second (IOPS) throughput ( in MBps) of each disk, with the comfort factor.
    - If the service can't find a disk with the required IOPS & throughput, it marks the machine as unsuitable for Azure.
    - If it finds a set of suitable disks, Azure Migrate selects the ones that support the storage redundancy method and location, specified in the assessment settings. If there are multiple eligible disks, it selects the one with the lowest cost.
- **Network check**: Azure Migrate tries to find an Azure VM that can support the number of NICs on the on-premises machines.
    - To do this, it aggregates the data transmitted per second (MBps) from the machine (network out), across all NICs, and applies the comfort factor to the aggregated number. This number if used to find a suitable Azure VM.
    - Azure Migrate takes the network settings from the VM, and assumes it to be a network outside the datacenter.
    - If no network performance data is available, only the NIC count is considered for VM sizing.
- **Compute check**: After storage and network requirements are calculated, Azure Migrate considers compute
requirements:
    - If the performance data is available for the VM, it looks at the utilized cores and memory, and applies the comfort factor. Based on that number, it tries to find a suitable VM size in Azure.
    - If no suitable size is found, the machine is marked as unsuitable for Azure.
    - If a suitable size is found, Azure Migrate applies the calculated storage and networking requirements. It then applies location and pricing tier settings, for the final VM size recommendation.


## Monthly cost estimation

After sizing recommendations are complete, Azure Migrate calculates compute, storage costs that will be incurred after migration.

- **Compute cost**: Using the recommended Azure VM size, Azure Migrate uses the Billing API to calculate
the monthly cost for the VM. The calculation takes the operating system, software assurance, location, and currency settings into account, and aggregates the cost across all machines. Using these parameters, it comes up with the total monthly compute cost estimation.
- **Storage cost**: The monthly storage cost for a machine is calculated by aggregating the monthly cost of
all disks attached to the Azure VM. Azure Migrate then aggregates the storage cost across all VMs, to calculate
total monthly storage cost. Currently, the calculation doesn't take the offer specified in the assessment settings into account.

Costs are displayed in the currency specified in the assessment settings. 


## Next steps

[Set up an assessment for on-premises VMware VMs](tutorial-assessment-vmware.md)
