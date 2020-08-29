---
title: 'Use Azure Firewall to inspect traffic destined to a private endpoint'
titleSuffix: Azure Private Link
description: Learn how you can inspect traffic destined to a private endpoint using Azure Firewall.
services: private-link
author: jocortems
ms.service: private-link
ms.topic: how-to
ms.date: 09/02/2020
ms.author: allensu

---
# Use Azure Firewall to inspect traffic destined to a private endpoint

Azure Private Endpoint is the fundamental building block for Azure Private Link. Private endpoints enable Azure resources deployed in a virtual network to communicate privately with private link resources.

Private endpoints allow resources access to the private link service deployed in a virtual network. Access to the private endpoint through virtual network peering and on-premises network connections extend the connectivity.

You may need to inspect or block traffic from clients to the services exposed via private endpoints. Complete this inspection by using [Azure Firewall](../firewall/overview.md) or a third-party network virtual appliance.

There are some limitations to implementation:

* Network security groups (NSGs) don't apply to private endpoints
* User-defined routes (UDR) don't apply to Private Endpoints
* A single route table can be attached to a subnet
* A route table supports up to 400 routes

Azure Firewall filters traffic using either:

* [FQDN in network rules](../firewall/fqdn-filtering-network-rules.md) for TCP and UDP protocols
* [FQDN in application rules](../firewall/features.md#application-fqdn-filtering-rules) for HTTP, HTTPS, and MSSQL. 

Most of the services exposed over private endpoints use HTTPS. The use of application rules over network rules is recommended when using Azure SQL.

> [!NOTE]
> SQL FQDN filtering is supported in [proxy-mode](../azure-sql/database/connectivity-architecture.md#connection-policy) only (port 1433). **Proxy** mode can result in more latency compared to *redirect*. If you want to continue using redirect mode, which is the default for clients connecting within Azure, you can filter access using FQDN in firewall network rules.

## Scenario 1: Hub and spoke architecture - Dedicated virtual network for private endpoints

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/hubandspoke.png" alt-text="Dedicated Virtual Network for Private Endpoints" border="true":::

This scenario is the most expandable architecture to connect privately to multiple Azure services using private endpoints. A route pointing to the network address space where the private endpoints are deployed is created. This configuration reduces administrative overhead and prevents running into the limit of 400 routes.

Connections from a client virtual network to the Azure Firewall in a hub virtual network will incur charges if the virtual networks are peered.

For more information on charges related to connections with peered virtual networks, see the FAQ section of the [pricing](https://azure.microsoft.com/pricing/details/private-link/) page.

>[!NOTE]
> This scenario can be implemented using any third party NVA or Azure Firewall network rules instead of application rules.

## Scenario 2: Hub and spoke architecture - Shared virtual network for private endpoints and virtual machines

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/sharedspoke.png" alt-text="Private Endpoints and Virtual Machines in same Virtual Network" border="true":::

This scenario is implemented when:

* It's not possible to have a dedicated virtual network for the private endpoints

* When only a few services are exposed in the virtual network using private endpoints

The virtual machines will have /32 system routes pointing to each private endpoint. One route per private endpoint is configured to route traffic through Azure Firewall. 

The administrative overhead of maintaining the route table increases as services are exposed in the virtual network. The possibility of hitting the route limit also increases.

Depending on your overall architecture, it's possible to run into the 400 routes limit. It's recommended to use scenario 1 whenever possible.

Connections from a client virtual network to the Azure Firewall in a hub virtual network will incur charges if the virtual networks are peered.

For more information on charges related to connections with peered virtual networks, see the FAQ section of the [pricing](https://azure.microsoft.com/pricing/details/private-link/) page.

>[!NOTE]
> This scenario can be implemented using any third party NVA or Azure Firewall network rules instead of application rules.

## Scenario 3: Single virtual network

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/singlevnet.png" alt-text="Single Virtual Network" border="true":::

This scenario is implemented when all services have been deployed into a single virtual network. This scenario is implemented when a migration to a hub and spoke architecture isn't possible. The same considerations as in scenario 2 apply. In this scenario, there aren't virtual network peering charges.

>[!NOTE]
> If you want to implement this scenario using a third party NVA or Azure Firewall, network rules instead of application rules is required to SNAT traffic destined to the private endpoints. Otherwise communication between the virtual machines and private endpoints will fail.

## Scenario 4: On-premises traffic to private endpoints

:::image type="content" source="./media/inspect-traffic-using-azure-firewall/onprem.png" alt-text="On-premises traffic to private endpoints" border="true":::

This architecture can be implemented if you have configured connectivity with your on-premises network using either: 

* [ExpressRoute](..\expressroute\expressroute-introduction.md)
* [Site to Site VPN](..\vpn-gateway\vpn-gateway-howto-site-to-site-resource-manager-portal.md) 

If your security requirements require client traffic to services exposed via private endpoints to be routed through a security appliance, deploy this scenario.

The same considerations as in scenario 2 above apply. In this scenario, there are no virtual network peering charges. For more information about how to configure your DNS servers to allow on-premises workloads to access private endpoints, see [On-Premises workloads using a DNS forwarder](./private-endpoint-dns.md#on-premises-workloads-using-a-dns-forwarder).

>[!NOTE]
> If you want to implement this scenario using a third party NVA or Azure Firewall, network rules instead of application rules is required to SNAT traffic destined to the private endpoints. Otherwise communication between the virtual machines and private endpoints will fail.

## Sign in to Azure

Sign in to the Azure portal at https://portal.azure.com.

## Create a VM

In this section, you'll create a virtual network and subnet to host the VM used to access your private link resource. An Azure SQL database is used as the example service.

## Virtual networks and parameters

Create three virtual networks and their corresponding subnets to:

* Host the VM that is used to access your private link resource.
* Contain the Azure Firewall used to restrict communication between the VM and the private endpoint.
* The private endpoint.

Replace the following parameters in the steps with the information below:

### Virtual machine network
| Parameter                   | Value                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroup |
| **\<virtual-network-name>** | myVMVNet          |
| **\<region-name>**          | South Central US      |
| **\<IPv4-address-space>**   | 10.1.0.0/16          |
| **\<subnet-name>**          | VMSubnet      |
| **\<subnet-address-range>** | 10.1.0.0/24          |

### Azure Firewall network
| Parameter                   | Value                 |
|-----------------------------|----------------------|
| **\<virtual-network-name>** | myAzFwVNet          |
| **\<region-name>**          | South Central US      |
| **\<IPv4-address-space>**   | 10.0.0.0/16          |
| **\<subnet-name>**          | AzureFirewallSubnet        |
| **\<subnet-address-range>** | 10.0.0.0/24          |

### Private endpoint network
| Parameter                   | Value                 |
|-----------------------------|----------------------|
| **\<private-endpoint-virtual-network-name>** | myPEVNet         |
| **\<region-name>**          | South Central US      |
| **\<IPv4-address-space>**   | 10.2.0.0/16          |
| **\<subnet-name>**          | PrivateEndpointSubnet    |        |
| **\<subnet-address-range>** | 10.2.0.0/24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

1. Repeat steps 1 to 9 to create the virtual networks for hosting the Azure Firewall and Private endpoint resources.

### Create virtual machine

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Compute** > **Virtual machine**.

1. In **Create a virtual machine - Basics**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | **PROJECT DETAILS** | |
    | Subscription | Select your subscription. |
    | Resource group | Select **myResourceGroup**. You created this resource group in the previous section.  |
    | **INSTANCE DETAILS** |  |
    | Virtual machine name | Enter **myVm**. |
    | Region | Select **(US) South Central US**. |
    | Availability options | Leave the default **No infrastructure redundancy required**. |
    | Image | Select **Ubuntu Server 18.04 LTS - Gen1**. |
    | Size | Select **Standard_B2s**. |
    | **ADMINISTRATOR ACCOUNT** |  |
    | Authentication type | Select **Password**. |
    | Username | Enter a username of your choosing. |
    | Password | Enter a password of your choosing. The password must be at least 12 characters long and meet the [defined complexity requirements](../virtual-machines/linux/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    | Confirm Password | Reenter password. |
    | **INBOUND PORT RULES** |  |
    | Public inbound ports | Select **None**. |
    |||

1. Select **Next: Disks**.

1. In **Create a virtual machine - Disks**, leave the defaults and select **Next: Networking**.

1. In **Create a virtual machine - Networking**, select this information:

    | Setting | Value |
    | ------- | ----- |
    | Virtual network | Select **myVMVNet**.  |
    | Subnet | Select **VMSubnet (10.1.0.0/24)**.|
    | Public IP | Leave the default **(new) myVm-ip**. |
    | Public inbound ports | Select **Allow selected ports**. |
    | Select inbound ports | Select **SSH**.|
    ||

1. Select **Review + create**. You're taken to the **Review + create** page where Azure validates your configuration.

1. When you see the **Validation passed** message, select **Create**.

## Deploy the Firewall

1. On the Azure portal menu or from the **Home** page, select **Create a resource**.

1. Type **firewall** in the search box and press **Enter**.

1. Select **Firewall** and then select **Create**.

1. On the **Create a Firewall** page, use the following table to configure the firewall:

    | Setting | Value |
    | ------- | ----- |
    | **PROJECT DETAILS** | |
    | Subscription | Select your subscription. |
    | Resource group | Select **myResourceGroup**.  |
    | **INSTANCE DETAILS** |  |
    | Name | Enter **myAzureFirewall**. |
    | Region | Select **South Central US**. |
    | Availability zone | Leave the default **None**. |
    | Choose a virtual network    |    Select **Use Existing**.    |
    | Virtual network    |    Select **myAzFwVNet**.    |
    | Public IP address    |    Select **Add new** and in Name enter **myFirewall-ip**.    |
    | Forced tunneling    | Leave the default **Disabled**.    |
    |||
1. Select **Review + create**. You're taken to the **Review + create** page where Azure validates your configuration.

1. When you see the **Validation passed** message, select **Create**.

## Create your private endpoint

In this section, you create a private SQL Database using a private endpoint.

1. On the upper-left side of the screen in the Azure portal, select **Create a resource** > **Databases** > **SQL Database**.

1. In **Create SQL Database - Basics**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | **PROJECT DETAILS** | |
    | Subscription | Select your subscription. |
    | Resource group | Select **myResourceGroup**. You created this resource group in the previous section.|
    | **DATABASE DETAILS** |  |
    | Database name  | Enter **mydatabase**.  |
    | Server | Select **Create new** and enter the information below.    |
    | Server name | Enter **mydbserver**. If this name is taken, enter a unique name.   |
    | Server admin login | Enter a name of your choosing. |
    | Password    |    Enter a password of your choosing.    |
    | Confirm Password | Reenter password    |
    | Location    | Select **(US) South Central US**.    |
    | Want to use SQL elastic pool    | Leave the default **No**. |
    | Compute + storage | Leave the default **General Purpose Gen5, 2 vCores, 32 GB Storage**. |
    |||
  
1. Select **Next: Networking**.

1. In **Create SQL Database - Networking**, connectivity method, select **Private Endpoint**.

1. In **Create SQL Database - Networking**, select **Add Private Endpoint**.

1. In **Create Private Endpoint**, enter or select this information:

    | Setting | Value |
    | ------- | ----- |
    | **PROJECT DETAILS** | |
    | Subscription | Select your subscription. |
    | Resource group | Select **myResourceGroup**. You created this resource group in the previous section.|
    |Location|Select **(US) South Central US**.|
    |Name|Enter **sqlPrivateEndpoint**.  |
    | **NETWORKING** |  |
    | Virtual network  | Select **myPEVNet** from resource group **myResourceGroup**. |
    | Subnet | Select **PrivateEndpointSubnet**. |
    | **PRIVATE DNS INTEGRATION**|  |
    | Integrate with private DNS zone  | Leave the default **Yes**. |
    | Private DNS zone  | Leave the default **(New) privatelink.database.windows.net**. |
    |||

1. Select **OK**.

1. Select **Review + create**. You're taken to the **Review + create** page where Azure validates your configuration.

1. When you see the **Validation passed** message, select **Create**.

## Connect the virtual networks using virtual network peering

In this section, we'll connect virtual networks **myVMVNet** and **myPEVNet** to **myAzFwVNet** using peering. There won't be direct connectivity between **myVMVNet** and **myPEVNet**.

1. In the portal's search bar, enter **myAzFwVNet**.

1. Select **Peerings** under **Settings** menu and select the **Add** button.

1. In **Add Peering** enter or select the following information:

    | Setting | Value |
    | ------- | ----- |
    | Name of the peering from myAzFwVNet to remote virtual network | Enter **myAzFwVNet-to-myVMVNet**. |
    | **PEER DETAILS** |  |
    | Virtual network deployment model  | Leave the default **Resource Manager**.  |
    | I know my resource ID | Leave unchecked.    |
    | Subscription | Select your subscription.    |
    | Virtual network | Select **myVMVNet**. |
    | Name of the peering from remote virtual network to myAzFwVNet    |    Enter **myVMVNet-to-myAzFwVNet**.    |
    | **CONFIGURATION** | |
    | **Configure virtual network access settings** | |
    | Allow virtual network access from myAzFwVNet to remote virtual network | Leave the default **Enabled**.    |
    | Allow virtual network access from remote virtual network to myAzFwVNet    | Leave the default **Enabled**.    |
    | **Configure forwarded traffic settings** | |
    | Allow forwarded traffic from remote virtual network to myAzFwVNet    | Select **Enabled**. |
    | Allow forwarded traffic from myAzFwVNet to remote virtual network | Select **Enabled**. |
    | **Configure gateway transit settings** | |
    | Allow gateway transit | Leave unchecked |
    |||

1. Select **OK**.

1. Repeat steps 2-4 to create a virtual network peering between virtual networks **myAzFwVNet** and **myPEVNet**.

## Link the virtual networks to the private DNS zone

In this section, we'll link virtual networks **myVMVNet** and **myAzFwVNet** to the **privatelink.database.windows.net** private DNS zone. This zone was created when we created the private endpoint. 

The link is required for the VM and firewall to resolve the FQDN of database to its private endpoint address. Virtual network **myPEVNet** was automatically linked when the private endpoint was created.

>[!NOTE]
>If you don't link the VM and firewall virtual networks to the private DNS zone, both the VM and firewall will still be able to resolve the SQL Server FQDN. They will resolve to its public IP address.

1. In the portal's search bar, enter **privatelink.database**.

1. Select **Virtual network links** under **Settings** menu and select the **Add** button.

1. In **Add virtual network link** enter or select the following information:

    | Setting | Value |
    | ------- | ----- |
    | Link name | Enter **Link-to-myVMVNet**. |
    | **VIRTUAL NETWORK DETAILS** |  |
    | I know the resource ID of virtual network  | Leave unchecked.  |
    | Subscription | Select your subscription.    |
    | Virtual network | Select **myVMVNet**. |
    | **CONFIGURATION** | |
    | Enable auto registration | Leave unchecked.    |
    |||

1. Select **OK**.

1. Repeat steps 2-4 to create a virtual network link between **privatelink.database.windows.net** private DNS zone and virtual network **myAzFwVNet**.

## Configure an application rule with SQL FQDN in Azure Firewall

In this section, configure an application rule to allow communication between **myVM** and the private endpoint for SQL Server **mydbserver.database.windows.net**. 

This rule allows communication through the firewall that we created in the previous steps.

1. In the portal's search bar, enter **myAzureFirewall**.

1. Select **Rules** under **Settings** menu.

1. Select **Application rule collection** and select **Add application rule collection**.

1. In **Add application rule collection** enter or select the following information:

    | Setting | Value |
    | ------- | ----- |
    | Name | Enter **SQLPrivateEndpoint**. |
    | Priority | Enter **100**. |
    | Action | Enter **Allow**. |
    | **RULES** |  |
    | **FQDN tags** | |
    | Name  | Leave blank.  |
    | Source type | Leave the default **IP address**.    |
    | Source | Leave blank. |
    | FQDN tags | Leave the default **0 selected**. |
    | **Target FQDNs** | |
    | Name | Enter **SQLPrivateEndpoint**.    |
    | Source type | Leave the default **IP address**. |
    | Source | Enter **10.1.0.0/16**. |
    | Protocol: Port | Enter **Mssql: 1433**. |
    | Target FQDNs | Enter **mydbserver.database.windows.net**. |
    |||

1. Select **Save**.

## Route traffic between the virtual machine and private endpoint through Azure Firewall

We didn't create a virtual network peering directly between virtual networks **myVMVNet** and **myPEVNet**. The virtual machine **myVM** doesn't have a route to the private endpoint we created. 

In this section, we'll create a route table with a custom route. 

The route sends traffic from the **myVM** subnet to the address space of virtual network **myPEVNet**, through the Azure Firewall.

1. On the Azure portal menu or from the **Home** page, select **Create a resource**.

1. Type **route table** in the search box and press **Enter**.

1. Select **Route table** and then select **Create**.

1. On the **Create Route table** page, use the following table to configure the route table:

    | Setting | Value |
    | ------- | ----- |
    | **PROJECT DETAILS** | |
    | Subscription | Select your subscription. |
    | Resource group | Select **myResourceGroup**.  |
    | **INSTANCE DETAILS** |  |
    | Region | Select **South Central US**. |
    | Name | Enter **VMsubnet-to-AzFW**. |
    | Propagate gateway routes | Select **No**. |
    |||

1. Select **Review + create**. You're taken to the **Review + create** page where Azure validates your configuration.

1. When you see the **Validation passed** message, select **Create**.

1. Once the deployment completes select **Go to resource**.

1. Select **Routes** from the **Settings** menu and select **Add**.

1. On the **Add route** page, use the following table to configure the route:

    | Setting | Value |
    | ------- | ----- |
    | Route name | Enter **to-privateendpoint**. |
    | Address prefix | Enter **10.2.0.0/16**.  |
    | Next hop type | Select **Virtual appliance**. |
    | Next hop address | Enter **10.0.0.4**. |
    |||

1. Select **OK**.

1. Select **Subnets** from the **Settings** menu and select **Associate**.

1. On the **Associate subnet** page, use the following table to associate the route table with **VMSubnet**:

    | Setting | Value |
    | ------- | ----- |
    | Virtual network | Select **myVMVNet**. |
    | Subnet | Select **VMSubnet**.  |
    |||

1. Select **OK**.

## Connect to the virtual machine from your client computer

Connect to the VM **myVm** from the internet as follows:

1. In the portal's search bar, enter **myVm**.

1. Select the **Connect** button. After selecting the **Connect** button, **Connect to virtual machine** opens.

1. Select **SSH** and copy the example command in section **4. Run the example command below to connect to your VM**.

1. If you're using Windows 10, run the command using PowerShell. For other Windows client versions, use an SSH client like [Putty](https://www.putty.org/)

1. Enter the password you defined when creating **myVm**

## Access SQL Server privately from the VM

In this section, you'll connect privately to the SQL Database using the private endpoint.

1. Enter `nslookup mydbserver.database.windows.net`
    
    You'll receive a message similar to below:

    ```bash
    Server:         127.0.0.53
    Address:        127.0.0.53#53

    Non-authoritative answer:
    mypdbserver.database.windows.net       canonical name = mydbserver.privatelink.database.windows.net.
    Name:   mypedbserver.privatelink.database.windows.net
    Address: 10.2.0.4
    ```

1. Install [SQL Server and SQL Server command-line tools](https://docs.microsoft.com/sql/linux/quickstart-install-connect-ubuntu?view=sql-server-ver15).

1. Run the following command to connect to the SQL Server. Use the server admin and password you defined when you created the SQL Server in the previous steps.

    ```bash
    sqlcmd -S mypdbserver.database.windows.net -U '<ServerAdmin>' -P '<YourPassword>'
    ```

1. Validate that [Azure Firewall logs](..\Firewall\log-analytics-samples.md) show the traffic is allowed.

1. (Optionally) [Create a new database](https://docs.microsoft.com/sql/linux/quickstart-install-connect-ubuntu?view=sql-server-ver15).

1. Close the connection to **myVM** by typing `exit`.

## Clean up resources

When you're done using the resources, delete the resource group and all of the resources it contains:

1. Enter **myResourceGroup** in the **Search** box at the top of the portal and select **myResourceGroup** from the search results.

1. Select **Delete resource group**.

1. Enter **myResourceGroup** for **TYPE THE RESOURCE GROUP NAME** and select **Delete**.

## Next steps

In this article, you explored different scenarios that you can use to restrict traffic between a virtual machine and a private endpoint using Azure Firewall. 

You connected to the VM and securely communicated to the database through Azure Firewall using private link.

To learn more about private endpoint, see [What is Azure Private Endpoint?](private-endpoint-overview.md).
