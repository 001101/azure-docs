---
title: Troubleshoot virtual network peering issues
description: Steps to help resolve most virtual network peering issues.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''

ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika

---

# Troubleshoot virtual network peering issues

This troubleshooting guide provides steps to help you resolve most [virtual network peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) issues.

![Diagram of virtual network peering](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## Scenario 1: Configure virtual network peering between two virtual networks

Are the virtual networks in the same subscription or in different subscriptions?

### Connection type 1: The virtual networks are in the same subscription

To configure virtual network peering for the virtual networks that are in the same subscription, use the applicable methods that are in the following articles:

* If the virtual networks are in the **same region**, see  [Create a peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering).
* If the virtual networks are in the **different regions**, see [Virtual network peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).  

> [!Note]
> Connectivity doesn't work over global virtual network peering for the following resources: 
>
> * Virtual machines (VMs) behind Basic ILB
> * Redis cache (uses Basic ILB)
> * Application gateway (uses Basic ILB)
> * Virtual machine scale sets (uses Basic ILB)
> * Service Fabric clusters (uses Basic ILB)
> * SQL Server Always On (uses Basic ILB)
> * App Service Environment for PowerApps (uses Basic ILB)
> * Azure API Management (uses Basic ILB)
> * Azure Active Directory Domain Services (Azure AD DS) (uses Basic ILB)

For more information, see the [requirements and constraints](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) of global peering.

### Connection type 2: The virtual networks are in different subscriptions or Active Directory tenants

To configure virtual network peering for virtual networks in different subscriptions or Active Directory tenants, see [Create peering in different subscriptions for Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

> [!Note]
> To configure network peering, you must have **Network Contributor** permissions in both subscriptions. For more information, see [Peering permissions](virtual-network-manage-peering.md#permissions).

## Scenario 2: Configure virtual network peering with hub-spoke topology that uses on-premises resources

![Diagram of virtual network peering with on-premises spoke](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### Connection type 1: For Site-to-Site connection or Azure ExpressRoute connection

For this type of connection, see [Configure VPN gateway transit for virtual network peering](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).

### Connection type 2: For Point-to-Site connections

1. For this type of connection, see [Configure VPN gateway transit for virtual network peering](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json).
2. After virtual network peering is established or changed, download and reinstall the Point-to-Site package so that the Point-to-Site clients get the updated routes to the spoke virtual network.

## Scenario 3: Configure virtual network peering with hub-spoke topology for Azure Virtual Network

![Diagram of virtual network peering with a virtual network spoke](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### Connection type 1: The virtual networks are in the same region


1. In the hub virtual network, configure a network virtual appliance (NVA).
1. In the spoke virtual networks, have user-defined routes with the next hop type "network virtual appliance" applied.

For more information, see [Service chaining](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining).

> [!Note]
> If you require help to set up an NVA, [contact the NVA vendor](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

For help with troubleshooting the NVA device setup and routing, see [Network virtual appliance issues in Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

### Connection type 2: The virtual networks are in different regions

Transit over global virtual network peering is now supported. Connectivity does not work over global virtual network peering for the following resources:

* VMs behind Basic ILB
* Redis cache (uses Basic ILB)
* Application gateway (uses Basic ILB)
* Scale sets (uses Basic ILB)
* Service Fabric clusters (uses Basic ILB)
* SQL Server Always On (uses Basic ILB)
* App Service Environment (uses Basic ILB)
* API Management (uses Basic ILB)
* Azure AD DS (uses Basic ILB)

To learn more about global peering requirements and restraints, see [Virtual network peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints).

## Scenario 4: Troubleshoot a connectivity issue between two peered virtual networks

Sign in to the [Azure portal](https://portal.azure.com/) with an account that has the necessary [roles and permissions](virtual-network-manage-peering.md#permissions). Select the virtual network, select **Peering**, and then check the **Status** field. What is the status?

### Connection type 1: The peering status is "Connected"

To troubleshoot this issue:

1. Check the network traffic flows:

   Use [Connection Troubleshoot](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) and [IP flow verify](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) from the source VM to the destination VM to determine whether there is an network security group or user-defined route that is causing interference in traffic flows.

   If you're using a firewall or NVA: 
   1. Document the user-defined route parameters so that you can restore them after this step is complete.
   2. Remove the user-defined route from the source VM subnet or NIC that points to the NVA as the next hop. Verify connectivity from the source VM directly to the destination that is bypassing the NVA. If this step works, see the [NVA troubleshooter](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva).

2. Take a network trace: 
   1. Start a network trace on the destination VM. For Windows, you can use **Netsh**. For Linux, use **TCPDump**.
   2. Run **TcpPing** or **PsPing** from the source to the destination IP.

   * This is an example of a **TcpPing** command: `tcping64.exe -t <destination VM address> 3389`

   3. After the **TcpPing** is complete, stop the network trace on the destination.
   4. If packets arrive from the source, there is no networking issue. Examine both the VM firewall and the application listening on that port to locate the configuration issue.

   > [!Note]
   > You can't connect to the following resource types over global virtual network peering (virtual networks in different regions):
   >
   > * VMs behind Basic ILB
   > * Redis cache (uses Basic ILB)
   > * Application gateway (uses Basic ILB)
   > * Scale sets (uses Basic ILB)
   > * Service Fabric clusters (uses Basic ILB)
   > * SQL Server Always On (uses Basic ILB)
   > * App Service Environment (uses Basic ILB)
   > * API Management (uses Basic ILB)
   > * Azure AD DS (uses Basic ILB)

For more information, see the [requirements and constraints](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) of global peering.

### Connection type 2: The peering status is "Disconnected"

To resolve this issue, delete the peering from both virtual networks, and then re-create them.

## Scenario 5: Troubleshoot a connectivity issue between a hub-spoke virtual network and an on-premises resource

Does your network use a third-party NVA or VPN gateway?

### Connection type 1: My network uses a third-party NVA or VPN gateway

To troubleshoot connectivity issues that affect a third-party NVA or VPN gateway, see the following articles:

* [NVA troubleshooter](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [Service chaining](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### Connection type 2: My network does not use a third-party NVA or VPN gateway

Do the hub virtual network and the spoke virtual network have a VPN gateway?

#### Both the hub virtual network and the spoke virtual network have a VPN gateway

Using a remote gateway isn't supported.

If the spoke virtual network already has a VPN gateway, **Use remote gateway** isn't supported on the spoke virtual network. This is because of a virtual network peering limitation.

#### Neither the hub virtual network nor the spoke virtual network has a VPN gateway

For Site-to-Site or ExpressRoute connections, check the following primary causes of connectivity issues to the remote virtual network from on-premises:

* On the virtual network that has a gateway, verify that the **Allow forwarded traffic** check box is selected.
* On the virtual network that doesn't have a gateway, verify that the **Use remote gateway** check box is selected.
* Have your network administrator check your on-premises devices to verify that they all have the remote virtual network address space added.

For Point-to-Site connections:

* On the virtual network that has a gateway, verify that the **Allow forwarded traffic** check box is selected.
* On the virtual network that doesn't have a gateway, verify that the **Use remote gateway** check box is selected.
* Download and reinstall the Point-to-Site client package. Virtual network routes that are newly peered don't automatically add routes to Point-to-Site clients.

## Scenario 6: Troubleshoot a hub-spoke network connectivity issue between spoke virtual networks in the same region

A hub network must include an NVA. Configure user-defined routes in spokes that have an NVA set as the next hop, and enable **Allow forwarded traffic** in the hub virtual network.

For more information, see [Service chaining](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining), and discuss these requirements with the [NVA vendor](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) of your choice.

## Scenario 7: Troubleshoot a hub-spoke network connectivity issue between spoke virtual networks in different regions

Transit over global virtual network peering is now supported. Connectivity doesn't work over global virtual network peering for the following resources:

* VMs behind Basic ILB
* Redis cache (uses Basic ILB)
* Application gateway (uses Basic ILB)
* Scale sets (uses Basic ILB)
* Service Fabric clusters (uses Basic ILB)
* SQL Server Always On (uses Basic ILB)
* App Service Environment (uses Basic ILB)
* API Management (uses Basic ILB)
* Azure AD DS (uses Basic ILB)

For more information, see the [requirements and constraints](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) of global peering and [Different VPN Topologies](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/).

## Scenario 8: Troubleshoot a hub-spoke network connectivity issue between a web app and the spoke virtual network

To troubleshoot this issue:

1. Sign in to the Azure portal. 
1. In the web app, select **networking**, and then select **VNet Integration**.
1. Check whether you can see the remote virtual network. Manually enter the remote virtual network address space (**Sync Network** and **Add Routes**).

For more information, see the following articles:

* [Integrate your app with an Azure Virtual Network](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [About Point-to-Site VPN routing](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## Scenario 9: Troubleshoot a virtual network peering configuration error message 

### Error 1: Current tenant `<TENANT ID>` isn't authorized to access linked subscription

To resolve this issue, see [Create peering - Azure CLI](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli).

### Error 2: Not connected

To resolve this issue, delete the peering from both virtual networks, and then recreate them.

### Error 3: Failed to peer a Databricks virtual network

To resolve this issue, configure the virtual network peering under **Azure Databricks**, and then specify the target virtual network by using **Resource ID**. For more information, see [Peer a Databricks virtual network to a remote virtual network](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2).

## Next steps

* [Troubleshooting connectivity problems between Azure VMs](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)