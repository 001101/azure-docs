---
title: include file
description: include file
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
---

Once Peering resource is deployed successfully, you can view it by following the steps below.

1. Go to **Resource groups** and click on the resource group you selected while creating Peering resource. You may use the *Filter* field if you have too many resource groups.

    > [!div class="mx-imgBorder"]
    > ![Peering resource group](../media/Peering_Direct_get_resourceGroup.png)

1. Click on the Peering resource you created.

    > [!div class="mx-imgBorder"]
    > ![Peering resource view](../media/Peering_Direct_get_open.png)

1. The **Overview** page shows high-level information. Observe the info highlighted below.

    > [!div class="mx-imgBorder"]
    > ![Peering resource view](../media/Peering_Exchange_get_overview.png)

1. On the left, click on **ASN information** to view information submitted while creating PeerAsn

    > [!div class="mx-imgBorder"]
    > ![Peering resource view](../media/Peering_Direct_get_asnInfo.png)

1. On the left, click on **Connections**. Observe on the top a summary of peering connections between your ASN and Microsoft, across different facilities within the metro. You may also arrive at the connections summary from **Overview** page, by clicking on **Connections** in the center pane as highlighted above.

    > [!div class="mx-imgBorder"]
    > ![Peering resource view](../media/Peering_Exchange_get_connectionsSummary.png)

    * **Connection State** corresponds to the state of the peering connection setup. The states displayed in this field follow the state diagram shown in [Exchange Peering Walkthrough](../internet-peering-workflows-exchange.md)
    * **IPv4 Session State** and **IPv6 Session State** correspond to the IPv4 and IPv6 BGP session states respectively.  
    * When you select a row on the top, the ***Connection*** section on the bottom shows details for each connection. You can click on arrow marks to expand the sub-sections ***Configuration***, ***IPv4 address*** and ***IPv6 address***

    > [!div class="mx-imgBorder"]
    > ![Peering resource view](../media/Peering_Exchange_get_connectionsIpv4.png)
