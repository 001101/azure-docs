---
title: Requirements and considerations for using Azure NetApp Files volume cross-region replication | Microsoft Docs
description: Describes the requirements and considerations for using the volume cross-region replication functionality of Azure NetApp Files.  
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''

ms.assetid:
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: b-juche
---

# Requirements and considerations for using cross-region replication 

You need to be aware of some requirements and considerations before [using the volume cross-region replication](cross-region-replication-create-peering.md) functionality of Azure NetApp Files:  

## Requirements and considerations 

* You must have an active Azure subscription and your subscription must be whitelisted for Azure NetApp Files before using Azure NetApp Files replication. 
* Azure NetApp Files replication is only available in certain fixed region pairs. See Supported region pairs. 
* SMB volumes are supported along with NFS volumes. Replication of SMB volumes requires an Active Directory connection in the source and destination NetApp accounts. The destination AD connection must have access to the DNS servers or ADDS Domain Controllers that are reachable from the delegated subnet in the destination region. For more information, see Requirements for Active Directory connections. 
* The destination account must be in a different region from the source volume region. You can also select an existing NetApp account in a different region.  
* Azure NetApp Files replication does not currently support multiple subscriptions; all replications must be performed under a single subscription.
* You can set up a maximum of five volumes for replication within a single subscription per region. You can open a support ticket to request for an increase in the default quota of five replication destination volumes (per subscription in a region). 
* There can be a delay up to five minutes for the interface to reflect a newly added snapshot on the source volume.  
* Cascading and fan in/out topologies are not supported.
* Configuring volume replication for source volumes created from snapshot is not supported at this time.
* After you set up cross-region replication, the replication process creates snapmirror snapshots to provide references between the source volume and the destination volume. Snapmirror snapshots are cycled automatically when a new one is created for every incremental transfer. You cannot delete snapmirror snapshots until replication relationship and volume is deleted. 
* You can delete manual snapshots on the source volume of a replication relationship when the replication relationship is active or broken, and also after the replication relationship is deleted. You cannot delete manual snapshots for the destination volume until the replication relationship is broken.

## Next steps
* [Create replication peering](cross-region-replication-create-peering.md)
* [Display health status of replication relationship](cross-region-replication-display-health-status.md)
* [Manage disaster recovery](cross-region-replication-manage-disaster-recovery.md)
* [Troubleshoot cross-region replication](troubleshoot-cross-region-replication.md)


