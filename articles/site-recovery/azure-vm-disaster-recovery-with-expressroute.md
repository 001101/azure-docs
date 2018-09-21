---
title: Set up disaster recovery for Azure VMs using Azure Site Recovery and Azure ExpressRoute | Microsoft Docs
description: Describes how to set up disaster recovery for Azure VMs using Azure Site Recovery and Azure ExpressRoute
services: site-recovery
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 09/21/2018
ms.author: manayar

---
# Set up disaster recovery for Azure VMs with Azure ExpressRoute


This article describes how to integrate Azure ExpressRoute with [Azure Site Recovery](site-recovery-overview.md) when you set up disaster recovery for Azure VMs to a secondary Azure region.

Site Recovery enables disaster recovery of Azure VMs.

- If your Azure VMs use [Azure managed disks](../virtual-machines/windows/managed-disks-overview.md), VM data is replicated to an replicated managed disk in the secondary region.
- If your Azure VMs don't use managed disks, VM data is replicated to an Azure Storage account.
- Replication endpoints are public, but replication traffic for Azure VMs doesn't cross the internet.

ExpressRoute enables you to extend on-premises networks into the Microsoft Azure cloud over a private connection, facilitated by a connectivity provider. If you have ExpressRoute configured, you can integrate it with Site Recovery as follows:

- **Replication between Azure regions**: Since replication traffic for Azure VM disaster recovery is within Azure only, ExpressRoute is not needed for replication.
- **Failover between Azure regions**: 


## Before you begin

Before you begin, make sure you understand the following concepts:

-	ExpressRoute [circuits](../expressroute/expressroute-circuit-peerings.md)
-	ExpressRoute [routing domains](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains)
- [ExpressRoute locations](../expressroute/expressroute-locations.md).
- Azure VM [replication architecture](azure-to-azure-architecture.md)
- [Setting up replication](azure-to-azure-tutorial-enable-replication.md) for Azure VMs.
-	[Failing over](azure-to-azure-tutorial-failover-failback.md) Azure VMs.


## General recommendations

For efficient Recovery Time Objectives (RTOs) and best practices for disaster recovery, when you set up Site Recovery replication and failover to integrate with ExpressRoute, we recommend you do the following:

- Provision networking components before you fail Azure VMs over to a secondary region:
    - Site Recovery can automatically deploy networking resources such as networks, subnets, and gateways in the target region when you enable replication for VMs, based on source networking settings.
    - However, Site Recovery doesn't automatically set up networking resources such as VNet gateways, and to reduce the overall recovery time
    - A small downtime is associated with deploying additional resources, and this downtime can impact the overall recovery time, if not accounted for during planning.
- Run regular disaster recovery drills.
    - A drill validates your replication strategy without data loss or downtime, and doesn't affect your production environment. Running a drill avoids last-minute configuration issues that can adversely impact RTO.
    - When you run a test failover for the drill, we recommend that you use a separate Azure VM network for the test failover, instead of the default network that's set up when you enable replication.
- Use different IP address spaces with a single ExpressRoute circuit.
    - We recommend that you use a different IP address space for the target virtual network, to avoid issues when establishing connections during regional outages.
    - If you can't use a separate address space, run the disaster recovery drill test failover on a separate test network with different IP addresses. You can’t connect two virtual networks with overlapping IP address space to the same ExpressRoute circuit.

## Replicate Azure VMs when using ExpressRoute


For Azure VM disaster recovery, as replication data does not leave the Azure boundary, ExpressRoute is not required for replication. If you want to set up replication for Azure VMs, and you're connecting to these VMs from your on-premises site over ExpressRoute, here's what you need to do:

1. [Enable replication](azure-to-azure-tutorial-enable-replication.md) for each Azure VM.
2. You don't need to create any target network:
    - When you configure replication, Site Recovery sets up networks, subnets, and gateway subnets in the target Azure region, to match those in the source region. Site Recovery also maps between the source and target virtual networks.
    - If you don't want Site Recovery to do this automatically, you can create the target-side network resources before you enable replication for VMs in the virtual networks.
3. Site Recovery does not replicate/create route tables,vNet gateways, vNet gateway connections, vNet peering, or other networking resources and connections in the secondary region. 
    - You need to created these in the secondary region any time before running a failover from the primary region.
    - You can use [recovery plans](site-recovery-create-recovery-plans.md) and automation script to automate the creation and connection of networking resources.
4. If you have a network virtual appliance (NVA) deployed to control the flow of network traffic, note the following:
    - Azure's default system route for Azure VM replication is 0.0.0.0/0.
    - Typically, NVA deployments also define a default route (0.0.0.0/0) that forces outbound Internet traffic to flow through the NVA. The default route is used when no other specific route configuration can be found.
    - If this is the case, the NVA might be overloaded if all replication traffic passes through the NVA. The same limited also applies when using default routes for routing all Azure VM traffic to on-premises deployments. 
    - In this scenario, we recommend that you [create a network service endpoint](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) in your virtual network for the Microsoft.Storage service, so that the replication traffic doesn't leave Azure boundary.

## Replication example

Typically enterprise deployments have workloads split across multiple Azure vNets, with a central connectivity hub for external connectivity to the internet and to on-premises sites. A hub and spoke topology is typically used together with ExpressRoute

![On-premises-to-Azure with ExpressRoute before failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

- **Region**. Apps are deployed in the Azure East Asia region.
- **Spoke vNets**. Apps are deployed in two spoke vNets:
    - **Source vNet1**: 10.1.0.0/24. 
    - **Source vNet2**: 10.2.0.0/24. 
    - Each spoke virtual network is connected to **Hub vNet**.
- **Hub vNet**. There's a hub vNet **Source Hub vNet**: 10.10.10.0/24.
    - This hub vNet acts as the gatekeeper.
    - All communications across subnets go through this hub.
 - ****Hub vNet subnets**. The hub vNet has two subnets:
     - **NVA subnet**: 10.10.10.0/25. This subnet contains an NVA (10.10.10.10).
     - **Gateway subnet**: 10.10.10.128/25. This subnet contains an ExpressRoute gateway connected to an ExpressRoute connection that routes to the on-premises site via a private peering routing domain.
- The on-premises datacenter has an ExpressRoute circuit connection through a partner edge in Hong Kong.
- All routing is controlled through Azure route tables (UDR).
- All outbound traffic between vNets, or to the on-premises datacenter is routed through the NVA.

### Hub and spoke peering settings

**Direction** | **Setting** | **State**
--- | --- | ---
Spoke to hub | Allow virtual network address | Enabled
Spoke to hub | Allow forwarded traffic | Enabled
Spoke to hub | Allow gateway transit | Disabled
Spoke to hub | Use remove gateways | Enabled
Hub to spoke | Allow virtual network address | Enabled
Hub to spoke | Allow forwarded traffic | Enabled
Hub to spoke | Allow gateway transit | Enabled
Hub to spoke | Use remove gateways | Disabled

 ![Spoke to hub peering configuration](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)
 ![Hub to spoke peering configuration](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### Example steps 

In this example, the following should happen when enabling replication for Azure VMs in the source network:

1. You [enable replication](azure-to-azure-tutorial-enable-replication.md) for a VM.
2. Site Recovery will create replica vNets, subnets, and gateway subnets in the target region.
3. Site Recovery will created mappings between the source networks and the replica target networks it creates.
4. You will need to manually create virtual network gateways, virtual network gateway connections, virtual network peering, or any other networking resources or connections. 


## Fail over Azure VMs when using ExpressRoute

After you fail Azure VMs over to the target Azure region using Site Recovery, you can access them using ExpressRoute [private peering](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

You need to connect ExpressRoute to the target vNet with a new connection. The existing ExpressRoute connection isn't automatically transferred. The way in which you set up your ExpressRoute connection to the target vNet depends on your ExpressRoute topology.


### Access with two circuits

#### Two circuits with two peering locations

This configuration helps protects ExpressRoute circuits against regional disaster. If your primary peering loation goes down, connections can continue from the other location.

- The circuit connected to the production environment is usually the primary. The secondary circuit typically has lower bandwidth, which can be increased if a disaster occurs. 
- After failover, you can establish connections from the secondary ExpressRoute circuit to the target vNet. Alternatively, you can have connections set up and ready in case of disaster, to reduce overall recovery time.
- With simultaneous connections to both primary and target vNets, make sure that your on-premises routing only uses the secondary circuit and connection after failover.
- The source and target vNets can receive new IP addresses, or keep the same ones, after failover. In both cases, the secondary connections can be established prior to failover.


#### Two circuits with single peering location

This configuration helps protect against failure of the primary ExpressRoute circuit, but not if the single ExpressRoute peering location goes down, impacting both circuits. 

- You can have simultaneous connections from the on-premises datacenter to source vNEt with the primary circuit, and to the target vNet with the secondary circuit.
- With simultaneous connections to primary and target, make sure that  on-premises routing only uses the secondary circuit and connection after failover.
-	You can’t connect both circuits to the same vNet when circuits are created at the same peering location.

### Access with a single route circuit
In this configuration there's only one Expressroute circuit. Although the circuit has a redundant connection in case one goes down, a single route circulate will not provide resilience if your peering region goes down. Note that:

- You can replicate Azure VMs to to any Azure region in the [same geographic location](azure-to-azure-support-matrix.md#region-support). If the target Azure region isn't in the same location as the source, you need to enable ExpressRoute Premium if you’re using a single ExpressRoute circuit. Learn about [ExpressRoute locations](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) and [ExpressRoute pricing](https://azure.microsoft.com/pricing/details/expressroute/).
- You can’t connect source and target vNets simultaneously to the circuit if the same IP address space is used on the target region. In this scenario:    
    -  Disconnect the source side connection, and then establish the target side connection. This connection change can be scripted as part of a Site Recovery recovery plan. Note that:
        - In a regional failure, if the primary region is inaccessible, the disconnect operation could fail. This could impact connection creation to the target region.
        - If you created the connection in the target region, and primary region recovers later, you might experience packet drops if two simultaneous connections attempt to connect to the same address space.
        - To prevent this, terminate the primary connection immediately.
        - After VM failback to the primary region, the primary connection can again be established, after you disconnect the secondary connection.
-	If a different address spaces is used on the target vNet, you can simultaneously connect to the source and target vNets from the same ExpressRoute circuit.


## Failover example

For a simple topology that uses a single ExpressRoute circuit, with same IP address after failover, [review this article](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity).

The example we're providing uses a topology as follows:

- Two different ExpressRoute circuits in two different peering locations.
- Retain private IP addresses for the Azure VMs after failover.
- The target recovery region is Azure SouthEast Asia.
- A secondary ExpressRoute circuit connection is established through a partner edge in Singapore.

### Example steps
To automate recovery in this example, here's what you need to do:

1. Follow the steps needed to [set up replication](#azure-vm-replication-steps).
2. [Fail over the Azure VMs](azure-to-azure-tutorial-failover-failback.md), with these additional steps during or after the failover.
    a. Create the Azure ExpressRoute Gateway in the target region hub vNet. This is need to connect the target hub vNet to the ExpressRoute circuit.
    b. Create the connection from the target hub vNet to the target ExpressRoute circuit.
    c. Set up the VNet peerings between the target region’s hub and spoke virtual networks. The peering properties on the target region will be the same as those on the source region.
    d. Set up the UDRs in the hub VNet, and the two spoke VNets.
        - The properties of the target side UDRs are the same as those on the source side when using the same IP addresses.
        - With different target IP addresses, the UDRs should be modified accordingly.


The above steps can be scripted as part of a [recovery plan](site-recovery-create-recovery-plans.md). Depending on the application connectivity and recovery time requirements, the above steps can also be completed prior to starting the failover.

Post the recovery of the virtual machines and completion of the other connectivity steps, the recovery environment looks as follows:
![On-premises-to-Azure with ExpressRoute after Failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)



## Next steps

Learn more about [recovery plans](site-recovery-create-recovery-plans.md) to automate app failover.
