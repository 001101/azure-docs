---
 title: include file
 description: include file
 services: virtual-wan
 author: cherylmc
 ms.service: virtual-wan
 ms.topic: include
 ms.date: 07/06/2018
 ms.author: cherylmc
 ms.custom: include file
---


### What is the difference between Azure Virtual Network Gateway VPN) and Azure Virtual WAN VPN Gateway

Virtual WAN provides large scale branch to branch connectivity and is built for throughput, scalability and ease of use. CPE branch devices auto provision and connect into Azure Virtual WAN and are now available from a growing ecosystem of SD-WAN and VPN partners.

### Who are the device-partners supported at launch time and can Virtual WAN only be used with those partners?

At time of launch, Citrix and Riverbed is supporting the fully automated Virtual WAN experience, more partners will be on-boarding in coming months: Nokia Nuage, Palo Alto, Checkpoint. However, customers can utilize any VPN capable device that adheres to the preview requirements for IKEv2 IPsec support.

### How can providers that provides branch devices offering VPN connectivity over IPsec/IKEv2 automate connectivity with Azure Virtual WAN?

Software defined connectivity solutions typically manage their branch devices using a controller or a device provisioning center. This controller can use Azure API to automate connectivity to Azure Virtual WAN.  For more information, <Link to How to partner>

### Are there any changes in terms of new features or changes to existing features, with this announcement?   

There are no changes to existing Azure connectivity features.

### Are there any ARM resources available for this service?
  
Yes, virtual WAN introduces a selection of new ARM resources. Please see the [Overview](https://go.microsoft.com/fwlink/?linkid=875726).

### Are there any special requirements to join the preview? 

See the [Overview](https://go.microsoft.com/fwlink/?linkid=875726) for information about how to join the managed preview.

Considerations:

* Limited to Azure public regions only
* Up to 100 connections supported. Each connection is an active-active tunnel terminating in a VPN gateway inside an Azure virtual hub
* Customers looking to deploy aggregated bandwidth less than a Gbps
* Customers with VPN devices that support route based configuration and IKEv2 IPsec connectivity
* Customers exploring SD-WAN and operating branch devices from the launch partners (Riverbed and Citrix) 
* Customers wanting to manually set up branch to branch and branch to Azure connectivity including configuration management of their on-premise devices

### Is Global VNet peering supported with Azure Virtual WAN? 
 No

### Can I deploy and use my favorite network virtual appliance (in a transit hub) with Azure Virtual WAN?

Yes, you can connect your favorite network virtual appliance transit hub to the Azure Virtual WAN. All spokes connected to the transit hub must should additionally be connected to the Virtual WAN hub.

### Is there support for BGP?
Yes, there is support for BGP. Spokes connected to a transit hub that in turn if connected to a Virtual WAN hub must disable BGP to ensure routes from transit hub are advertised appropriately.

### Can the transit hub have ExpressRoute or VPN Virtual Network Gateway ?
No

### Is there an ability to direct traffic using UDR in the Virtual Hub? 
Routing functionality will be available by GA of Virtual WAN

### Can spoke VNets connected to a Virtual WAN Hub communicate with each other ?
If spoke VNets need to talk to each other, as well as sites on the Virtual WAN, or have more advanced security policy or logging needs, a transit hub can be used. No NAT functionality is required between the spokes. You can also set up VNet peering between the Azure spokes if necessarily. For more information, see [Virtual Network Peering](../articles/virtual-network/virtual-network-peering-overview.md)

### Q. What are the pricing and/or licensing implications, if any, for this announcement?
 
A. There is no additional charge; current [Azure VPN and egress pricing](https://azure.microsoft.com/pricing/details/vpn-gateway/) remains in effect during preview.