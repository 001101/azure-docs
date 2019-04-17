---
title: Azure Australia VPN Gateway Quickstart Guide | Microsoft Docs
description: Implementing VPN Gateway on Azure Australia Quickstart
services: azure-australia
cloud: public
documentationcenter: ''
author: grgale
manager: liki

ms.assetid: 
ms.service: azure-australia
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-australia
ms.date: 04/17/19
ms.author: grgale
---

# Azure Virtual Network Gateway: Quick Start Guide

## Introduction

### Purpose

A critical service with any public cloud is the secure connection of cloud resources and services to existing on-premises systems.  The service which provides this capability in Azure is the Azure VPN Gateway (VPN Gateway). This guide outlines the key considerations with configuring the VPN Gateway to comply with the Australian Signals Directorate’s (ASD) [Information Security Manual Controls](https://acsc.gov.au/infosec/ism/) (ISM).

A VPN Gateway is used to send encrypted traffic between an Azure virtual network (VNet) and another network.  There are 3 scenarios addressed by VPN Gateways:

- **Site-to-Site** (S2S)
- **Point-to-Site** (P2S)
- **VNet-to-VNet**

This guide will focus on S2S VPN Gateways. Diagram 1 shows an example Site-to-Site VPN gateway configuration.

![VPN Gateway with multi-site connections](images/vpngateway-multisite-connection-diagram.png)
*Diagram 1 – Azure Site-to-Site VPN Gateway*

## Key Design Considerations

There are 3 networking options to connect Azure to Australian Government customers:

- **ICON**
- **ExpressRoute**
- **Public internet**

The Australian Cyber Security Centre’s [Consumer Guide for Azure](https://servicetrust.microsoft.com/viewpage/Australia)  recommends that VPN Gateway (or equivalent PROTECTED certified third-party service) is used in conjunction with the three networking options to ensure that the connections comply with the ISM controls for encryption and integrity.

### Encryption and integrity

By default, the VPN negotiates the encryption and integrity algorithms and parameters during the connection establishment as part of the IKE handshakes.  During the IKE handshake, the configuration and order of preference will depend on whether the VPN Gateway is the initiator or the responder (NB: this is controlled via the VPN device).  The final configuration of the connection is controlled by the configuration of the VPN Device.  For details of validated VPN devices and their configuration see here: [About VPN Devices](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpn-devices)

VPN Gateways can control encryption and integrity by configuring a custom IPsec/IKE policy on the connection.

### Resource operations

VPN Gateways create a connection between Azure and non-Azure environments over the public internet.  The ISM has controls which relate to the explicit authorization of connections.  By default, it is possible to use VPN Gateways to create unauthorized tunnels into secure environments.  Therefore, it is critical that organizations use Azure Role Based Access Control (RBAC) to control who can create and modify VPN Gateways and their connections.  Azure has no “built-in” role to manage VPN Gateways therefore this will require a custom role.

Access to “Owner”, “Contributor” and “Network Contributor” roles is tightly controlled.  It is also recommended that Azure AD Privileged Identity Management is used for more granular access control.

### High availability

Azure VPN Gateways can have multiple connections (see Diagram 1) and support multiple on-premises VPN devices to the same on-premises environment.  

Azure VNets can have multiple VPN Gateways which can be deployed in independent, active-passive or active-active configurations.

It is recommended that all VPN Gateways are deployed in a [highly available configuration](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-highlyavailable): e.g. 2 on-premises VPN devices connected to 2 VPN Gateways in either active-passive or active-active  mode (See Diagram 2).

![VPN Gateway redundant connections](images/dual-redundancy.png)
*Diagram 2 – Active-active VPN Gateways and 2 VPN devices*

### Forced tunnelling

Forced tunnelling redirects or "forces" all Internet-bound traffic back to the on-premises environment via the VPN Gateway for inspection and auditing. Without forced tunnelling, Internet-bound traffic from VMs in Azure traverse the Azure network infrastructure directly out to the public internet, without the option to inspect or audit the traffic.  This is critical when organization is required to use a Secure Internet Gateway (SIG) for an environment.

## Detailed Configuration

### Service attributes

VPN Gateways for S2S connections such have following attributes:

Attribute | SHOULD | MUST
--- | --- | ---
gatewayType | | “VPN”

Attribute settings required to comply with the ISM controls for Protected are:

Attribute | SHOULD | MUST
--- | --- | ---
vpnType | | “RouteBased”
vpnClientConfiguration/vpnClientProtocols | | “IkeV2”

Azure VPN Gateways support a range of cryptographic algorithms from the IPsec and IKE protocol standards.  The default policy sets were chosen to maximize interoperability with a wide range of third-party VPN devices.  As a result, it is possible that during the IKE handshake that a non-compliant configuration is negotiated.  Therefore, it is highly recommended that [custom IPsec/IKE policy](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-ipsecikepolicy-rm-powershell) parameters  are applied to vpnClientConfiguration in VPN Gateways to ensure the connections meet the ISM controls for on-premise environment connections to Azure.  The key attributes are:

Attribute | SHOULD | MUST
--- | --- | ---
saLifeTimeSeconds | < 14400 secs | > 300 secs (min)
saDataSizeKilobytes | | > 1024 Kbytes (min)
ipsecEncryption | | AES256|GCMAES256
ipsecIntegrity | | SHA256|GCMAES256
ikeEncryption | | AES256|GCMAES256
ikeIntegrity | | SHA256|GCMAES256
dhGroup | DHGroup14\|DHGroup24\|**ECP256\|ECP384**<sup>[1](#fn1)</sup> | DHGroup2
pfsGroup | PFS2048\|PFS24\|**ECP256\|ECP384**<sup>[1](#fn1)</sup> |
|

### Related Services

in designing and configuring an Azure VPN Gateway there are a number of related services that must also exist and be configured:

Service | Action Required
--- | ---
Virtual Network | VPN Gateways are attached to a virtual network.  A VNet needs to create prior to the creation of a new VPN Gateway.
Public IP Address | S2S VPN Gateways need a public IP address to establish connectivity between the on-premises VPN device and the VPN Gateway.  A public IP address needs to create prior to creating a S2S VPN Gateway.
Subnet | A subnet of the VNet needs to be created for the VPN Gateway.

## Implementation Steps using PowerShell

### Role-Based Access Control (RBAC)

1. Create custom role (e.g. virtualNetworkGateway contributor).  Create a role to be assign to users who will be allowed to create and modify VPN Gateways. The custom role should allow the following operations:

   Microsoft.Network/virtualNetworkGateways/*  
   Microsoft.Network/connections/*  
   Microsoft.Network/localnetworkgateways/*  
   Microsoft.Network/virtualNetworks/subnets/*  
   Microsoft.Network/publicIPAddresses/*  
   Microsoft.Network/publicIPPrefixes/*  
   Microsoft.Network/routeTables/*  

2. Add custom role to users who are allowed to create and manage VPN Gateways and connections to on-premises environments.

### Create VPN Gateway <sup>[2](#fn2)</sup>

1. Create a new Public IP address
2. Create a VPN Gateway subnet
3. Create a VPN Gateway IP config
4. Create a VPN Gateway
5. Create a Local Network Gateway for the on-premises VPN device
6. Create an IPsec Policy (assuming using custom IPsec/IKE policies)
7. Create connection between VPN Gateway and Local Network Gateway using IPsec Policy

### Enforce tunnelling

If forced tunnelling is required, prior to creating the VPN Gateway:

1. Create route table and route rule(s)
2. Associate route table with the appropriate subnets

After creating the VPN Gateway:

1. Set GatewayDefaultSite to the on-premises environment on the VPN Gateway

### Example PowerShell Script

An example PowerShell Script for creating a custom IPSEC/IKE policy which complies with ISM controls for Australian PROTECTED security classification.

It assumes that the VNET, VPN Gateway and Local Gateways exist.

#### Create an IPsec/IKE policy

The following sample script creates an IPsec/IKE policy with the following algorithms and parameters:

- IKEv2: AES256, SHA256, DHGroup ECP256
- IPsec: AES256, SHA256, PFS ECP256, SA Lifetime 14400 seconds & 102400000KB

```powershell
$custompolicy = New-AzIpsecPolicy `
                    -IkeEncryption AES256 `
                    -IkeIntegrity SHA256 `
                    -DhGroup ECP256 `
                    -IpsecEncryption AES256 `
                    -IpsecIntegrity SHA256 `
                    -PfsGroup ECP256 `
                    -SALifeTimeSeconds 14400 `
                    -SADataSizeKilobytes 102400000
```

#### Create a S2S VPN connection with the custom IPsec/IKE policy

```powershell
$vpngw = Get-AzVirtualNetworkGateway `
                    -Name "<yourVPNGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"
$localgw = Get-AzLocalNetworkGateway  `
                    -Name "<yourLocalGatewayName>" `
                    -ResourceGroupName "<yourResourceGroupName>"

New-AzVirtualNetworkGatewayConnection `
                    -Name "ConnectionName" `
                    -ResourceGroupName "<yourResourceGroupName>" `
                    -VirtualNetworkGateway1 $vpngw `
                    -LocalNetworkGateway2 $localgw `
                    -Location "Australia Central" `
                    -ConnectionType IPsec `
                    -IpsecPolicies $custompolicy `
                    -SharedKey "AzureA1b2C3"
```

## Further Reading

[Azure Virtual Network Gateway Overview](https://docs.microsoft.com/en-us/azure/vpn-gateway/)  
[What is VPN Gateway?](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-about-vpngateways)  
[Create a VNet with a Site-to-Site VPN connection using PowerShell](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)  
[Create and manage a VPN gateway](https://docs.microsoft.com/en-us/azure/vpn-gateway/vpn-gateway-tutorial-create-gateway-powershell)

<a name="footnote1"><sup>1</sup></a> Use of Elliptic Curve encryption algorithms are preferred [>](#fn1ref)  
<a name="fn2"><sup>2</sup></a> Assuming VNet has previous been created [>](#fn2ref)