---
title: Understand storage hierarchy of Azure NetApp Files | Microsoft Docs
description: Describes the Understand storage hierarchy concept, including Azure NetApp Files accounts, capacity pools, and volumes.
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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
---
# Understand storage hierarchy of Azure NetApp Files

Understanding storage hierarchy helps you set up and manage your Azure NetApp Files resources. 
 
Creating a volume enables you to store data.  Before creating a volume, you must purchase and set up a pool for provisioned capacity.  To set up a capacity pool, you must have a NetApp account. 

## <a name="azure_netapp_files_account"></a>Azure NetApp Files accounts

- A NetApp account serves as an administrative grouping of the constituent capacity pools.  
- A NetApp account is not the same as your general Azure storage account. 
- A NetApp account is regional in scope.  
  You can have multiple NetApp accounts in a region, but each NetApp account is tied to only a single region.

## <a name="capacity_pools"></a>Capacity pools

- A capacity pool is measured by its provisioned capacity.  
- The capacity is provisioned by purchasing fixed SKUs (for example, a 4TB capacity).
- A capacity pool can have only one service level.  
  Currently, only the Premium service level is available.
- Each capacity pool belongs to only one NetApp account.  
- A capacity pool cannot be moved across Azure   accounts.  
  For example, in the [Conceptual diagram of storage hierarchy](#conceptual_diagram_of_storage_hierarchy) below, Capacity Pool 1 cannot be moved from US East NetApp account to US West 2 NetApp account.  

## <a name="volumes"></a>Volumes

- A volume is measured by logical capacity consumption and is scalable up to 100TB per volume.
- A volume's capacity consumption counts against its pool's provisioned capacity.
- Each volume belongs to only one pool, but a pool can contain multiple volumes. 
- Within the same NetApp account, you can move a volume across pools. 
  For example, in the [Conceptual diagram of storage hierarchy](#conceptual_diagram_of_storage_hierarchy) below, you can move the volumes from Capacity Pool 1 to Capacity Pool 2.
- A volume can present multiple mount targets.

## <a name="conceptual_diagram_of_storage_hierarchy"></a>Conceptual diagram of storage hierarchy 
The following example shows the relationships of the Azure subscription, NetApp accounts, capacity pools,  and volumes.   
    ![Conceptual diagram of storage hierarchy](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## Related information

- [Create a NetApp account](azure-netapp-files-create-netapp-account.md)
- [Set up a capacity pool](azure-netapp-files-set-up-capacity-pool.md)
- [Create a volume for Azure NetApp Files](azure-netapp-files-create-volumes.md)