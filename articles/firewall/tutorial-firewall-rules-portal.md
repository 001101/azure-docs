---
title: Firewall - Azure portal
description: In this tutorial , you learn how to configure application and network firewall rules 
services: firewall
author: vhorne
manager: jpconnock

ms.service: firewall
ms.topic: tutorial
ms.date: 6/25/2018
ms.author: victorh
ms.custom: mvc
#Customer intent: As an administrator, I want configure firewall rules so that I can control outbound access from resources located in a subnet.
---
# Tutorial: Configure Azure Firewall application and network rules using the Azure portal

Azure Firewall has two rule types to control outbound access:

- **Application rules**

   Allows you to configure fully qualified domain names (FQDNs) that can be accessed from a subnet. For example, you could allow access to *github.com* from your subnet.
- **Network rules**

   Allows you to configure rules containing source address, protocol, destination port, and destination address. For example, you could create a rule to allow traffic to port 53 (DNS) to the IP address of your DNS server from your subnet.

After you configure the rules, you route your network traffic from your subnet to the firewall (as a default gateway) where the network traffic is then subjected to the configured firewall rules.

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Set up a test network environment
> * Create a firewall
> * Create a default route
> * Configure application rules
> * Configure network rules
> * Test the firewall

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

[!INCLUDE [firewall-preview-notice](../../includes/firewall-preview-notice.md)]


## Set up the network environment
First, create a resource group to contain the resources needed to deploy the firewall. Then create a VNet, subnets, and test servers.

### Create a resource group
1. On the Azure portal home page, click **Resource groups**, then click **Add**.
2. For **Resource group name**, type **Test-FW-RG**.
3. For **Subscription**, select your subscription.
4. For **Resource group location**, select **East US**.
5. Click **Create**.


### Create a VNet
1. From the Azure portal home page, click **All services**.
2. Under **Networking**, click **Virtual networks**.
3. Click **Add**.
4. For **Name**, type **Test-FW-VN**.
5. For **Address space**, type **10.0.0.0/16**.

   You should use an address space that does not overlap with another virtual network in your subscription. If this one does not work for you, use one that does not overlap.
6. For **Subscription**, select your subscription.
7. For **Resource group**, select **Use existing**, and then select **Test-FW-RG**.
8. For **Location**, select **East US**.
9. Under **Subnet**, for **Name** type **FW-SN**.

   This firewall will be in this subnet.
10. For **Address range**, type **10.0.1.0/24**.
11. Use the other default settings, and then click **Create**.

### Create additional subnets

Next, create subnets for the jump server, and a subnet for the workload servers.

1. On the Azure portal home page, click **Resource groups**, then click **Test-FW-RG**.
2. Click the **Test-FW-VN** virtual network.
3. Click **Subnets**, and then click **Subnet**.
4. For **Name**, type **Workload-SN**.
5. For **Address range**, type **10.0.2.0/24**.
6. Click **OK**.

Create another subnet named **Jump-SN**, address range **10.0.3.0/24**.

### Create virtual machines

Now create the jump and workload virtual machines, and place them in the appropriate subnets.

1. From the Azure portal home page, click **All services**.
2. Under **Compute**, click **Virtual machines**.
3. Click **Add**, and click **Windows Server**,  click **Windows Server 2016 Datacenter**, and then click **Create**.

**Basics**

1. For **Name**, type **Srv-Jump**.
5. Type a username and password.
6. For **Subscription**, select your subscription.
7. For **Resource group**, select **Use existing**, and then select **Test-FW-RG**.
8. For **Location**, select **East US**.
9. Click **OK**.

**Size**

1. Choose an appropriate size for a test virtual machine running Windows Sever. For example, **B4ms** (16 GB RAM, 32 GB storage).
2. Click **Select**.

**Settings**

1. Under **Network**, for **Virtual network**, select **Test-FW-VN**.
2. For **Subnet**, select **Jump-SN**.
3. For **Select public inbound ports**, select **RDP (3389)**.
1. Leave the other default settings and click **OK**.

**Summary**

Review the summary, and then click **Create**. This will take a few minutes to complete.

Repeate this process to create another virtual machine named **Srv-Work**.

Use the information in the following table to confgure the **Settings** for the Srv-Work virtual machine. The rest of the configuration is the same as the Srv-Jump virtual machine.


|Setting  |Value  |
|---------|---------|
|**Subnet**|**Workload-SN**|
|**Public IP address**|**None**|
|**Select public inbound ports**|**No public inbound ports**|


## Create the firewall

<!--- the following procedure is created from sceenshots, not actual UI and still needs to verified --->

[//]: # (Need steps showing how to get to the Create an Azure Firewall page)

1. On the **Create an Azure Firewall** page, use the following table to configure the firewall:
   
   |Setting  |Value  |
   |---------|---------|
   |Name     |Test-FW01|
   |Subscription     |\<your subscription\>|
   |Resource group     |**Use existing**: Test-FW-RG |
   |Location     |East US|
   |Choose a virtual network     |**Use existing**: Test-FW-VN|
   |Subnet     |FW-SN|
   |Public IP address     |**Create new**|

2. Click **Review + create** to create the firewall.


[//]: # (Remember to note the public IP for the firewall.)

## Create a default route

On the **Workload-SN** subnet, the outbound traffic default route will go through the firewall.

1. From the Azure portal home page, click **All services**.
2. Under **Networking**, click **Route tables**.
3. Click **Add**.
4. For **Name**, type **Firewall-route**.
5. For **Subscription**, select your subscription.
6. For **Resource group**, select **Use existing**, and select **Test-FW-RG**.
7. Click **Create**.
8. Click **Refresh**, and then click the **Firewall-route** route table.
9. Click **Subnets**, and then click **Associate**.
10. Click **Virtual nework**, and then select **Test-FW-VN**.
11. For **Subnet**, click **Workload-SN**.
12. Click **OK**.
13. Click **Routes**, and then click **Add**.
14. For **Route name**, type **FW-DG**.
15. For **Address prefix**, type **0.0.0.0/0**.
16. For **Next hop type**, select **Virtual appliance**.
17. For **Next hop address**, type the public IP address for the firewall thay you noted previously.
18. Click **OK**.


## Configure application rules

<!--- the following procedure is created from sceenshots, not actual UI and still needs to verified --->

1. From the **Firewalls** page, under **Settings**, click **Rules**.
2. Click **Add application rule collection**.
3. For **Name**, type **App-Coll01**.
1. For **Priority**, type **200**.
2. For **Action**, select **Allow**.

6. Under **Rules**, for **Name**, type **AllowAWS**.
7. For **Source Addresses**, type **10.0.2.0/24**.
8. For **Target FQDNS**, type **github.com**

## Configure network rules

<!--- the following procedure is created from sceenshots, not actual UI and still needs to verified --->

1. From the **Firewalls** page, under **Settings**, click **Rules**.
2. Click **Add network rule collection**.
3. For **Name**, type **Net-Coll01**.
4. For **Priority**, type **200**.
5. For **Action**, select **Allow**.

6. Under **Rules**, for **Name**, type **AllowDNS**.
8. For **Protocol**, select **DNS**.
9. For Destination address, type **209.244.0.3, 209.244.0.4**
10. For **Destination Ports**, verify it is set to **53**.

## Test the firewall

1. From the Azure portal, review the network settings for the **Srv-Work** virtual machine and note the private IP address.
2. Connect a remote desktop to **Srv-Jump** virtual machine, and from there open a remote desktop connection to the **Srv-Work** private IP address.
3. Change the primary and secondary DNS address for **SRV-Work**. From an administrator PowerShell window, run:

   `Get-DnsClientServerAddress`

   Note the Interface Index for the Ethernet interface that has a DNS server IP address configured.

4. Now change the DNS server IP address from a administrator PowerShell window:

   `Set-DnsClientServerAddress -InterfaceIndex <your index #> -ServerAddresses ("209.244.0.3","209.244.0.4")`

5. Open Internet Explorer and browse to http://github.com.
6. Click **OK**, and **Close** on the security alerts.

   You should see the GitHub home page.

7. Browse to http://www.msn.com.

   You should be blocked by the firewall.

So now you have verified that the firewall rules are working:

- You can browse to the one allowed FQDN, but not to any others.
- You can resolve DNS names using the configured exernal DNS server.

## Clean up resources

When no longer needed, delete the **Test-FW-RG** resource group to delete all firewall related resources.


## Next steps

In this tutorial, you learned how to:

> [!div class="checklist"]
> * Set up the network
> * Create a firewall
> * Configure application and network firewall rules
> * Test the firewall

Next, you can monitor the access and firewall logs.

> [!div class="nextstepaction"]
> [Diagnostics](./diagnostics.md)
