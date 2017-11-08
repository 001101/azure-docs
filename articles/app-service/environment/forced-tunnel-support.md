---
title: Configure your ASE to be force tunneled
description: Enable your ASE to work when outbound traffic is force tunneled
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch

ms.assetid: 384cf393-5c63-4ffb-9eb2-bfd990bc7af1
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: ccompy
---
The App Service Environment is a deployment of the Azure App Service in a customer's Azure Virtual Network (VNet). Many customers configure their VNets to be extentions of their on premises networks with VPNs or ExpressRoute connections. Due to corporate policies or other security constraints, they configure routes to send all outbound traffic on premises before it can go out to the internet. Changing the routing of the VNet so that the outbound traffic from the VNet flows through the VPN or ExpressRoute connection to on premises is called forced tunneling.  

Forced tunneling can cause problems for an ASE. The ASE has a number of external dependencies which are enumerated in this [ASE Network Architecture][network] document. The ASE, by default, requires that all outbound communication goes through the VIP that is provisioned with the ASE.

In an Azure Virtual Network, routing is done based on Longest Prefix Match (LPM).  If there is more than one route with the same LPM match then a route is selected based on its origin in the following order:

1. User defined route
1. BGP route (when ExpressRoute is used)
1. System route

To learn more about routing in a VNet, read [User defined routes and IP forwarding][routes]. 

If your want your ASE to operate in a forced tunnel VNet you have two choices:

1. Enable your ASE to have direct internet access
1. Change the egress endpoint for your ASE

## Enable your ASE to have direct internet access

For your ASE to work while your VNet is configured with an ExpressRoute, you can:

* Configure ExpressRoute to advertise 0.0.0.0/0. By default, it force tunnels all outbound traffic on-premises.
* Create a UDR. Apply it to the subnet that contains the ASE with an address prefix of 0.0.0.0/0 and a next hop type of Internet.

If you make these two changes, internet-destined traffic that originates from the ASE subnet isn't forced down the ExpressRoute and the ASE works.

> [!IMPORTANT]
> The routes defined in a UDR must be specific enough to take precedence over any routes advertised by the ExpressRoute configuration. The preceding example uses the broad 0.0.0.0/0 address range. It can potentially be accidentally overridden by route advertisements that use more specific address ranges.
>
> ASEs aren't supported with ExpressRoute configurations that cross-advertise routes from the public-peering path to the private-peering path. ExpressRoute configurations with public peering configured receive route advertisements from Microsoft. The advertisements contain a large set of Microsoft Azure IP address ranges. If the address ranges are cross-advertised on the private-peering path, all outbound network packets from the ASE's subnet are force tunneled to a customer's on-premises network infrastructure. This network flow is currently not supported with ASEs. One solution to this problem is to stop cross-advertising routes from the public-peering path to the private-peering path.

## Change the egress endpoint for your ASE ##

This section describes how to enable an ASE to operate in a forced tunnel configuration by changing the egress endpoint used by the ASE. This can be used to support forced tunneling situations or any other situation where the egress address must be changed.  An ASE not only has external dependencies but it also must listen for inbound traffic to manage the ASE. The ASE must be able to respond to such traffic and the replies cannot be sent back from another address as that breaks TCP.  There are thus three steps required to change the egress endpoint for the ASE.

1. Set a route table to ensure that inbound management traffic can go back out from the same IP address
1. Add your IP addresses that will be used for egress to the ASE firewall
1. Set the routes to outbound traffic from the ASE to be tunneled

![Forced tunnel network flow][1]

You can configure the ASE with a different egress address after the ASE is already up and operational or during ASE deployment.  

### Changing the egress address after the ASE is operational ###
1. Get the IP addresses you want to use as egress IPs for your ASE. If you are doing forced tunneling then this would be your NATs or gateway IPs.  If you were routing all of the ASE outbound traffic through an NVA, then the egress address would be the public IP of the NVA.
2. Set the egress addresses in your ASE configuration information. Go to resource.azure.com and navigate to: Subscription/<subscription id>/resourceGroups/<ase resource group>/providers/Microsoft.Web/hostingEnvironments/<ase name> then you will see the json that describes your ASE.  Make sure it says read/write at the top.  Click Edit   Scroll down to the bottom and change userWhitelistedIpRanges from  

       "userWhitelistedIpRanges": null 
      
  to something like the following, but using the addresses you will set as the egress address range 

      "userWhitelistedIpRanges": [
          "11.22.33.44/32".
          "55.66.77.0/24"
          ] 

   as appropriate. Then click PUT at the top. This will trigger a scale operation on your ASE and adjust the firewall.
   
3. Create or edit a route table and populate the rules to allow access to/from the management addresses that map to your ASE location.  The management addreses are here, [App Service Environment management addresses][management] 

4. Adjust the routes applied to the ASE subnet with a route table or BGP routes.  

If the ASE goes unresponsive from the portal then there is a problem with your changes.  It can be that your list of egress addresses was incomplete, the traffic was lost or the traffic was blocked.  

### Create a new ASE with a different egress address  ###

In the event that your VNet is already configured to force tunnel all the traffic, you will need to take some extra steps to create your ASE such that it will come up successfully. This means you need to enable use of another egress endpoint during the ASE creation.  To do this, you need to create the ASE with a template. The steps to do this are:

1. Get the IP addresses that you will use as the egress addresses for your ASE.
1. Pre-create the subnet to be used by the ASE. This is needed to let you set routes and also because the template requires it.  
1. Create a route table with the management IPs that map to your ASE location and assign it to your ASE
1. Follow the directions here, [Creating an ASE with a template][templatecreate], and pull down the appropriate template
1. Edit the azuredeploy.json file to include **userWhitelistedIpRanges** with your values in the form *["11.22.33.44/32", "55.66.77.0/30"]* as appropriate.

If this is configured properly then the ASE should start with no issues.  


<!--IMAGES-->
[1]: ./media/forced-tunnel-support/forced-tunnel-flow.png

<!--Links-->
[management]: ./management-addresses.md
[network]: ./network-info.md
[routes]: ../../virtual-network/virtual-networks-udr-overview.md
[template]: ./create-from-template.md
