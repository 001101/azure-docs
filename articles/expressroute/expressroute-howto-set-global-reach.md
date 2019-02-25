---
title: 'Configure Global Reach - ExpressRoute: Azure | Microsoft Docs'
description: This article helps you link ExpressRoute circuits together to make a private network between your on-premises networks and enable Global Reach.
services: expressroute
author: mialdrid

ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: mialdrid
ms.custom: seodec18

---

# Configure ExpressRoute Global Reach (preview)
This article helps you configure ExpressRoute Global Reach using PowerShell. For more information, see [ExpressRouteRoute Global Reach](expressroute-global-reach.md).

> [!IMPORTANT]
> This public preview is provided without a service-level agreement and should not be used for production workloads. Certain features might not be supported, might have constrained capabilities, or might not be available in all Azure locations. For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
> 
 
## Before you begin

Before you start configuration, confirm the following:

* That you've installed the latest version of Azure PowerShell if you want to run PowerShell locally.
* That you understand ExpressRoute circuit provisioning [workflows](expressroute-workflows.md).
* That your ExpressRoute circuits are in a provisioned state.
* That Azure private peering is configured on your ExpressRoute circuits.

### Working with Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### 1. Sign in to your Azure account
To start the configuration, sign in to your Azure account and select the subscription that you want to use. 

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### 2. Identify your ExpressRoute circuits for configuration
You can enable ExpressRoute Global Reach between any two ExpressRoute circuits as long as they're located in the supported countries and were created at different peering locations. If your subscription owns both circuits, you can choose either circuit to run the configuration in the following sections.

If the two circuits are in different Azure subscriptions, you need authorization from one Azure subscription. Then you pass in the authorization key when you run the configuration command in the other Azure subscription.

## Enable connectivity between your on-premises networks

### ExpressRoute circuits in the same Azure subscription

1. Use the following commands to get circuit 1 and circuit 2. The two circuits are in the same subscription.

  ```azurepowershell-interactive
  $ckt_1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
  $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
  ```
2. Run the following command against circuit 1, and pass in the private peering ID of circuit 2. When running the command, note the following:

  * The private peering ID looks similar to the following example: 

  ```
  /subscriptions/{your_subscription_id}/resourceGroups/{your_resource_group}/providers/Microsoft.Network/expressRouteCircuits/{your_circuit_name}/peerings/AzurePrivatePeering
  ```
  * *-AddressPrefix* must be a /29 IPv4 subnet, for example, "10.0.0.0/29". We use IP addresses in this subnet to establish connectivity between the two ExpressRoute circuits. You shouldn’t use the addresses in this subnet in your Azure virtual networks, or in your on-premises network.

  ```azurepowershell-interactive
  Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering $ckt_2.Peerings[0].Id -AddressPrefix '__.__.__.__/29'
  ```
3. Save the configuration on circuit 1 as follows:

  ```azurepowershell-interactive
  Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
  ```

When the previous operation completes, you will have connectivity between your on-premises networks on both sides through your two ExpressRoute circuits.

### ExpressRoute circuits in different Azure subscriptions

If the two circuits are not in the same Azure subscription, you need authorization. In the following configuration, authorization is generated in the circuit 2 subscription, and the authorization key is passed to circuit 1.

1. Generate an authorization key.

  ```azurepowershell-interactive
  $ckt_2 = Get-AzExpressRouteCircuit -Name "Your_circuit_2_name" -ResourceGroupName "Your_resource_group"
  Add-AzExpressRouteCircuitAuthorization -ExpressRouteCircuit $ckt_2 -Name "Name_for_auth_key"
  Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_2
  ```

  Make a note of the private peering ID of circuit 2, as well as the authorization key.
2. Run the following command against circuit 1. Pass in the private peering ID of circuit 2 and the authorization key.

  ```azurepowershell-interactive
  Add-AzExpressRouteCircuitConnectionConfig -Name 'Your_connection_name' -ExpressRouteCircuit $ckt_1 -PeerExpressRouteCircuitPeering "circuit_2_private_peering_id" -AddressPrefix '__.__.__.__/29' -AuthorizationKey '########-####-####-####-############'
  ```
3. Save the configuration on circuit 1.

  ```azurepowershell-interactive
  Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
  ```

When the previous operation completes, you will have connectivity between your on-premises networks on both sides through your two ExpressRoute circuits.

## Get and verify the configuration

Use the following command to verify the configuration on the circuit where the configuration was made (for example, circuit 1 in the previous example).
```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
```

If you simply run *$ckt1* in PowerShell, you see *CircuitConnectionStatus* in the output. It tells you whether the connectivity is established, "Connected", or "Disconnected". 

## Disable connectivity between your on-premises networks

To disable connectivity, run the commands against the circuit where the configuration was made (for example, circuit 1 in the previous example).

```azurepowershell-interactive
$ckt1 = Get-AzExpressRouteCircuit -Name "Your_circuit_1_name" -ResourceGroupName "Your_resource_group"
Remove-AzExpressRouteCircuitConnectionConfig -Name "Your_connection_name" -ExpressRouteCircuit $ckt_1
Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt_1
```

You can run the Get operation to verify the status.

After the previous operation is complete, you no longer have connectivity between your on-premises network through your ExpressRoute circuits.

## Next steps
1. [Learn more about ExpressRoute Global Reach](expressroute-global-reach.md)
2. [Verify ExpressRoute connectivity](expressroute-troubleshooting-expressroute-overview.md)
3. [Link an ExpressRoute circuit to an Azure virtual network](expressroute-howto-linkvnet-arm.md)