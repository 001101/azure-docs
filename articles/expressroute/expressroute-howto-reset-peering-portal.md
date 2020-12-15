---
title: 'Azure ExpressRoute: Reset circuit peerings by using the Azure portal'
description: Learn how to disable and enable peerings of an Azure ExpressRoute circuit by using the Azure portal.
services: expressroute
author: duongau

ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.author: duau
---

# Reset ExpressRoute circuit peerings by using the Azure portal

The first time you configure the peerings on your ExpressRoute circuit, they are enabled by default. You can enable and disable Microsoft Peering and Azure Private Peering on an ExpressRoute circuit independently.  Disable a peering to disconnect the Border Gateway Protocol (BGP) sessions for the primary and secondary connections of your ExpressRoute circuit. Re-enable a peering to reconnect the BGP sessions.

Resetting your ExpressRoute peerings might be helpful in the following scenarios:

* You are testing your disaster recovery design and implementation. For example, if you have two ExpressRoute circuits, you can disable the peerings of one circuit and force your network traffic to use the other circuit.

* You want to enable Bidirectional Forwarding Detection (BFD) on Azure Private Peering or Microsoft Peering. If your ExpressRoute circuit was created before August 1, 2018 on Azure Private Peering or before January 10, 2020 on Microsoft Peering, BFD was not enabled by default. You can enable BFD by resetting the peering.

## Sign in to the Azure portal

From a browser, navigate to the [Azure portal](https://portal.azure.com), and then sign in with your Azure account.

## Reset a peering

1. Choose the circuit that you want to change.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit-list.png" alt-text="ExpressRoute circuit list":::

1. Choose the peering configuration that you want to enable or disable.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/expressroute-circuit.png" alt-text="ExpressRoute circuit overview":::

1. Clear the **Enable Peering** check box, and then select **Save** to disable the peering configuration.

    :::image type="content" source="./media/expressroute-howto-reset-peering-portal/disable-peering.png" alt-text="Disable private peering":::

1. Select the **Enable Peering** check box, and then select **Save** to re-enable the peering configuration.

## Next steps
To troubleshoot ExpressRoute problems, see the following articles:
* [Verifying ExpressRoute connectivity](expressroute-troubleshooting-expressroute-overview.md)
* [Troubleshooting network performance](expressroute-troubleshooting-network-performance.md)
