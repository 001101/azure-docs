---
 title: include file
 description: include file
 services: virtual-wan
 author: cherylmc
 ms.service: virtual-wan
 ms.topic: include
 ms.date: 10/18/2019
 ms.author: cherylmc
 ms.custom: include file
---
1. Select **Connect VPN Sites** to open the **Connect sites** page.

    ![connect](./media/virtual-wan-tutorial-connect-vpn-site-include/connect.png "connect")

   Complete the following fields:

   * Enter a Pre-shared key. Azure auto generates it for you otherwise. 
   * Select the Protocol and IPsec settings. Refer to default/custom IPSec details (put the link to the page)
   * Select the appropriate option for Propagate Default Route. An Enable option allows Virtual Hub to propagate a learnt default route to this connection. This flag enables default route propagation to a connection only if the default route is already learned by the Virtual WAN hub as a result of deploying a firewall in the hub or if another connected site has forced tunneling enabled. The default route does not originate in the Virtual WAN hub

2. Select **Connect**.
3. In a few minutes, the site will show the connection and connectivity status.

   ![status](./media/virtual-wan-tutorial-connect-vpn-site-include/status.png "status")

   **Connection Status:** This is the status of the Azure ARM resource for connection that connects the VPN Site to the Azure hub’s VPN gateway. Once this control plane operation is successful, Azure VPN gateway and the on-premises VPN device will proceed to establish connectivity.

   **Connectivity Status:** This is the actual connectivity (data path) status between Azure’s VPN gateway in the hub and VPN Site. It can show any of the following states

    * Unknown: This state is typically seen if the backend systems are working to transition to another status.
    * Connecting: Azure VPN gateway is trying to reach out to the actual on-premises VPN site.
    * Connected: Connectivity is established between Azure VPN gateway and on-premises VPN site.
    * Disconnected: This status is seen if, for any reason (on-premises or in Azure), the connection was disconnected.
4. You can additionally edit/delete the VPN Connection, delete the site  in the Azure portal, or download a branch specific configuration for details about the Azure side from the context (…) menu next to the Site. If you want to download the configuration for all connected sites in your hub, select **Download VPN Config** on the top menu.