---
title: include file
description: include file
services: batch
documentationcenter: 
author: dlepow
manager: jeconnoc
editor: ''

ms.assetid: 
ms.service: batch
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 09/26/2018
ms.author: danlep
ms.custom: include file 
---

### General requirements

* The VNet must be in the same subscription and region as the Batch account you use to create your pool.

* When specifying the subnet using the Batch APIs, use the *resource identifier* of the subnet. For example, for a Resource Manager-based VNet, the subnet identifier is of the form:

  ```
  /subscriptions/{subscription}/resourceGroups/{group}/providers/Microsoft.Network/virtualNetworks/{network}/subnets/{subnet}
  ```

* The pool using the VNet can have a maximum of 4096 nodes.

* The subnet specified for the pool must have enough unassigned IP addresses to accommodate the number of VMs targeted for the pool; that is, the sum of the `targetDedicatedNodes` and `targetLowPriorityNodes` properties of the pool. If the subnet doesn't have enough unassigned IP addresses, the pool partially allocates the compute nodes, and a resize error occurs. 

* Your Azure Storage endpoint needs to be resolved by any custom DNS servers that serve your VNet. Specifically, URLs of the form `<account>.table.core.windows.net`, `<account>.queue.core.windows.net`, and `<account>.blob.core.windows.net` should be resolvable. 

Additional VNet requirements differ, depending on whether the Batch pool is in the Virtual Machine configuration or the Cloud Services configuration.

### Pools in the Virtual Machine configuration

**Supported VNets** - Azure Resource Manager-based VNets only

**Permissions** - The user creating the pool must have at least the Contributor role on the specified VNet, in order to access the VNet and deploy VMs in the subnet.

**Additional networking resources** - Batch automatically allocates the following resources per each 50 pool nodes in a VNet: 1 network security group (NSG), 1 public IP address, and 1 load balancer. These resources are created in the subscription that contains the virtual network supplied when creating the Batch pool, and are limited by the subscription's [resource quotas](../articles/azure-subscription-service-limits.md). For large pools you may need to request a quota increase for one or more of these resources.

#### Network security groups

The subnet must allow inbound communication from the Batch service to be able to schedule tasks on the compute nodes, and outbound communication to communicate with Azure storage or other resources. For pools in the Virtual Machine configuration, Batch adds NSGs at the level of network interfaces (NIC) attached to VMs. These NSGs automatically configure inbound and outbound rules to allow the following traffic:

* Inbound TCP traffic on ports 29876 and 29877 from Batch service role IP addresses. 
* Inbound TCP traffic on port 22 (Linux nodes) or port 3389 (Windows nodes) to permit remote access.
* Outbound traffic on any port to the virtual network.
* Outbound traffic on any port to the internet.

If the specified subnet has associated Network Security Groups (NSGs) and/or a firewall, configure the inbound and outbound security rules as shown in the following tables. If communication to the compute nodes in the specified subnet is denied by an NSG, then the Batch service sets the state of the compute nodes to **unusable**.

> [!NOTE]
> Configure inbound traffic on port 3389 (Windows) or 22 (Linux) only if you need to permit remote access to the pool VMs. It is not required for the pool VMs to be usable.

**Inbound security rules**

| Source IP addresses | Source ports | Destination | Destination ports | Protocol | Action |
| --- | --- | --- | --- | --- | --- |
Any <br /><br />Although this requires effectively "allow all", the Batch service applies an NSG at the network interface level on each VM created under virtual machine configuration that filters out all non-Batch service IP addresses. | * | Any | 29876-29877 | TCP | Allow |
| Any | * | Any |  3389 (Windows), 22 (Linux) | TCP | Allow |

**Outbound security rules**

| Source | Source ports | Destination | Destination service tag | Protocol | Action |
| --- | --- | --- | --- | --- | --- |
| Any | 443 | [Service tag](../articles/virtual-network/security-overview.md#service-tags) | Storage (in the same region as your Batch account and VNet)  | Any | Allow |

### Pools in the Cloud Services configuration

**Supported VNets** - Classic VNets only

**Permissions** - The `MicrosoftAzureBatch` service principal must have the `Classic Virtual Machine Contributor` Role-Based Access Control (RBAC) role for the specified VNet.

#### Network security groups

The subnet must allow inbound communication from the Batch service to be able to schedule tasks on the compute nodes, and outbound communication to communicate with Azure storage or other resources.

You do not need to specify an NSG, because Batch configures inbound communication only from Batch IP addresses. However, If the specified subnet has associated NSGs and/or a firewall, configure the inbound and outbound security rules as shown in the following tables. If communication to the compute nodes in the specified subnet is denied by an NSG, then the Batch service sets the state of the compute nodes to **unusable**.

> [!NOTE]
> Configure inbound traffic on port 3389 (Windows) or 22 (Linux) only if you need to permit remote access to the pool VMs. It is not required for the pool VMs to be usable.

**Inbound security rules**

| Source IP addresses | Source ports | Destination | Destination ports | Protocol | Action |
| --- | --- | --- | --- | --- | --- |
Any <br /><br />Although this requires effectively "allow all", the Batch service applies an NSG at the network interface level on each VM created under virtual machine configuration that filters out all non-Batch service IP addresses. | * | Any | 10100, 20100, 30100 | TCP | Allow |
| Any | * | Any |  3389 (Windows), 22 (Linux) | TCP | Allow |

**Outbound security rules**

| Source | Source ports | Destination | Destination ports | Protocol | Action |
| --- | --- | --- | --- | --- | --- |
| Any | * | Any | 443  | Any | Allow |