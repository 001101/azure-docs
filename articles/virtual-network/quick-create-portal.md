---
title: Create a virtual network - Azure portal | Microsoft Docs
description: Quickly learn to create a virtual network using the Azure portal.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager

ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
---

# Create a virtual network using the Azure portal

In this article, you learn how to create a virtual network. You can deploy several types of Azure resources into a virtual network and allow them to communicate with each other privately, and with the Internet. After creating the virtual network, you deploy two virtual machines into the virtual network so they can communicate privately with each other, and with the Internet.

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Log in to Azure 

Log in to the Azure portal at http://portal.azure.com.

## Create a virtual network

1. Click **+ New** on the upper left-hand corner of the Azure portal.

2. Select **Networking**, and then select **Virtual network**.

3. As shown in the following picture, enter *myVirtualNetwork* for **Name**, *myResourceGroup* for **Resource group**, select a **Location** and your **Subscription**, accept the remaining defaults, and then click **Create**. 

    ![Enter basic information about your virtual network](./media/quick-create-portal/virtual-network.png)

    All Azure resources exist in a [subscription](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription), a [resource group](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group), and a location (or [region](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region)). The **Address space** is specified in CIDR notation. A virtual network contains zero or more subnets. The default **Address range** of 10.0.0.0/24 uses the entire address range of the virtual network, so another subnet cannot be created within the virtual network using the default address space and ranges. The specified address range includes the IP addresses 10.0.0.0-10.0.0.254. Only 10.0.0.4-10.0.0.254 are available however, because Azure reserves the first four addresses (0-3) and the last address in each subnet. The available IP addresses are assigned to resources deployed within a virtual network.

## Create virtual machines

Create two virtual machines in the virtual network so you can validate and understand how communication between virtual machines in a virtual network works in a later step.

1. Click the **New** button found on the upper left-hand corner of the Azure portal.

2. Select **Compute**, and then select **Windows Server 2016 Datacenter**.

3. Enter virtual machine information shown in the picture that follows. The **User name** and **Password** you enter are used to log in to the virtual machine in a later step. The password must be at least 12 characters long and meet the [defined complexity requirements](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm). Select your **Subscription**, choose to use the existing *myResourceGroup* resource group, and ensure that the **Location** selected is the same location you created the virtual network in. When complete, click **OK**.

    ![Enter basic information about a virtual machine](./media/quick-create-portal/virtual-machine-basics.png)

4. Select a size for the virtual machine and then click **Select**. To see more sizes, select **View all** or change the **Supported disk type** filter. The sizes that appear for you may be different than the following example. 

    ![Select a size for a virtual machine](./media/quick-create-portal/virtual-machine-size.png)

5. Under **Settings**, *myVirtualNetwork* should already be selected for **Virtual network**, but if it's not, click **Virtual network**, select *myVirtualNetwork*. Leave *default* selected for **Subnet**, and then click **OK**.

    ![Select a virtual network](./media/quick-create-portal/virtual-machine-network-settings.png)

6. On the **Summary** page, click **Create** to start the virtual machine deployment.

7. After creation, the virtual machine is pinned to the Azure portal dashboard and the virtual machine summary automatically opens. Click **Networking**.

    ![Virtual machine networking information](./media/quick-create-portal/virtual-machine-networking.png)

    You see that the **Private IP** address is *10.0.0.4*. In step 5, under **Settings**, you selected the *myVirtualNetwork* virtual network and accepted the subnet named *default* for **Subnet**. When you [created the virtual network](#create-a-virtual-network), you accepted the default value of 10.0.0.0/24 for the subnet **Address range**. Azure's DHCP server assigns the first available address for the subnet you select to the virtual machine. Since Azure reserves the first four addresses (0-3) of each subnet, 10.0.0.4 is the first available IP address available for the subnet.

    The **Public IP** address assigned is different than the address assigned to your virtual machine. A virtual machine isn't required to have a public IP address, but Azure assigns a public, Internet routable IP address to each virtual machine, by default. The public IP address is assigned to the virtual machine from a [pool of addresses assigned to each Azure region](https://www.microsoft.com/download/details.aspx?id=41653). A virtual machine must have a public IP address assigned to it for you be able to connect to the virtual machine from the Internet. While Azure knows which public IP address is assigned to a virtual machine, the operating system running in a virtual machine has no awareness of any public IP address assigned to it.

8. Complete steps 1-7 again, with the following changes:
    
    - In step 3, name the virtual machine *myVm2*.
    - In step 5, click **Public IP address**, then click **None**, because it isn't necessary to connect to this virtual machine from the Internet. Regardless of whether a public IP address is assigned to a virtual machine, the virtual machine can still communicate outbound to the Internet.

    After the virtual machine is created, click **Networking**, as you did in step 7. You see the **Private IP** address is *10.0.0.5*. Since Azure previously assigned the first usable address of *10.0.0.4* in the subnet to the *myVm1* virtual machine, it assigned *10.0.0.5* to the *myVm2* virtual machine, because it was the next available address in the subnet.

## Connect to a virtual machine

1. Remotely connect to the *myVm1* virtual machine. At the top of the Azure portal, enter *myVm1*. When **myVm1** appears in the search results, click it. Click the **Connect** button.

    ![Virtual machine overview](./media/quick-create-portal/virtual-machine-overview.png)


2. After clicking the **Connect** button, an .rdp file is downloaded to your computer.  
3. Open the rdp file. If prompted, click **Connect**. Enter the user name and password you specified when creating the virtual machine, then click **OK**. You may receive a certificate warning during the sign-in process. Click **Yes** or **Continue** to proceed with the connection.

The connection succeeds because a public IP address is assigned to *myVm1*. You cannot connect to *myVm2* from the Internet because *myVm2* does not have a public IP address assigned to it.

## Validate communication

To validate communication with *myVm2*, enter the following command from a command prompt on the *myVm1* virtual machine, provide the credentials you used when you created the virtual machine and complete the connection:

```
mstsc myVm2
```

The remote desktop connection is successful because both virtual machines have private IP addresses assigned from the *default* subnet. You are able to connect to *myVm2* by hostname because Azure automatically provides DNS name resolution for all hosts within a virtual network. 

If you attempt to ping *myVm2* from *myVm1*, ping fails because ping is not allowed through the Windows firewall, by default.

To allow ping to a Windows virtual machine, enter the following command from the Windows virtual machine you want to allow ping to:

```
netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"
```

To confirm outbound communication to the Internet, enter the following command:

```
ping bing.com
```

You receive four replies from bing.com.

## Clean up resources

When no longer needed, delete the resource group, and all its contents. To do so, select the resource group for the virtual machine and click **Delete**.

## Next steps

In this quickstart, you deployed a default virtual network with one subnet and two virtual machines. To learn how to create a custom virtual network with multiple subnets and perform basic management tasks, continue to the tutorial for creating a custom virtual network and managing it.


> [!div class="nextstepaction"]
> [Create a custom virtual network and manage it](./tutorial-create-manage-virtual-network-portal.md)
