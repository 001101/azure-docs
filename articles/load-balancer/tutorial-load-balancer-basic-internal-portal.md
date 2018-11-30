---
title: "Tutorial: Create a Basic internal load balancer by using the Azure portal | Microsoft Docs"
description: This tutorial shows you how to create an internal Basic load balancer by using the Azure portal.
services: load-balancer
documentationcenter: na
author: KumudD 
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internal traffic to virtual machines within a specific zone in a region. 

ms.assetid: aa9d26ca-3d8a-4a99-83b7-c410dd20b9d0
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2018
ms.author: kumud
ms.custom: mvc
---

# Tutorial: Balance internal traffic load with a Basic load balancer in the Azure portal

Load balancing provides a higher level of availability and scale by spreading incoming requests across virtual machines (VMs). You can use the Azure portal to create a Basic load balancer and balance internal traffic among VMs. This tutorial shows you how to create and configure an internal load balancer, back-end servers, and network resources at the Basic pricing tier.

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin. 

If you prefer, you can do these steps using the [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) or [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) instead of the portal.

To do the steps uaing this tutorial, sign in to the Azure portal at [https://portal.azure.com](https://portal.azure.com).

### Create a virtual network

1. On the upper-left side of the portal, select **Create a resource** > **Networking** > **Virtual network**.
   
1. In the **Create virtual network** pane, type or select these values:
   
   - **Name**: Type *MyVNet*.
   - **ResourceGroup**: Select **Create new**, then enter *MyResourceGroupLB*, and select **OK**. 
   - **Subnet** > **Name**: Type *MyBackendSubnet*.
   
1. Select **Create**.

   ![Create a virtual network](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

## Create a Basic load balancer

Create a Basic internal load balancer by using the portal. The name and IP address you create are automatically configured as the load balancer's front end.

1. On the upper-left side of the portal, select **Create a resource** > **Networking** > **Load Balancer**.
   
1. In the **Create load balancer** pane, type or select these values:
   
   - **Name**: Type *MyLoadBalancer*.
   - **Type**: Select **Internal**. 
   - **SKU**: Select **Basic**.
   - **Virtual network**: Select **Choose a virtual network**, and then select **MyVNet**.
   - **Subnet**: Select **Choose a subnet**, and then select **MyBackendSubnet**.
   - **IP address assignment**: Select **Static** if not selected.
   - **Private IP address**: Type an address that is in the address space of your virtual network and subnet, for example *10.3.0.7*.
   - **ResourceGroup**: Drop down **Select existing** and select **MyResourceGroupLB**. 
   
1. Select **Create**.
   
![Create a load balancer](./media/tutorial-load-balancer-basic-internal-portal/1-load-balancer.png)

## Create back-end server and test VMs

In your virtual network, create two VMs to use for the back-end pool of your Basic load balancer, and a third VM to use for testing the load balancer. 

### Create virtual machines

1. On the upper-left side of the portal, select **Create a resource** > **Compute** > **Windows Server 2016 Datacenter**. 
   
1. In **Create a virtual machine**, type or select the following values in the **Basics** tab:
   - **Subscription** > **Resource Group**: Drop down and select **MyResourceGroupLB**.
   - **Instance Details** > **Virtual machine name**: Type *MyVM1*.
   - **Instance Details** > **Availability Options**: 
     1. Drop down and select **Availability set**. 
     2. Select **Create new**, type *MyAvailabilitySet*, and select **OK**.
   - **Administrator Account** > **Username**: Type *azureuser*.
   - **Administrator Account** > **Password**: Type *Azure1234567*. 
     Retype the password in the **Confirm password** field.
   
1. Select the **Networking** tab, or select **Next: Disks**, then **Next: Networking**. 
   
   Make sure the following are selected:
   - **Virtual network**: **MyVNet**
   - **Subnet**: **MyBackendSubnet**
   
   >[!NOTE]
   >By default, the VM already has the **RDP** (Remote Desktop) port open to allow remote desktop access. 
   
1. Select the **Management** tab, or select **Next** > **Management**. Under **Monitoring**, set **Boot diagnostics** to **Off**.
   
1. Select **Review + create**.
   
1. Review the settings, and then select **Create**. 

1. Follow the steps to create a second VM named *MyVM2*, with all the other settings the same as MyVM1. 

1. Follow the steps again to create a third VM named *MyTestVM*. 

## Create Basic Load Balancer resources

In this section, you  configure load balancer settings for a backend address pool and a health probe, and specify load balancer rules.

### Create a back-end address pool

To distribute traffic to the VMs, the load balancer uses a back-end address pool. The back-end address pool contains the IP addresses of the virtual network interfaces (NICs) that are connected to the load balancer. 

**To create a back-end address pool that includes VM1 and VM2:**

1. Select **All resources** on the left menu, and then select **MyLoadBalancer** from the resource list.
   
1. Under **Settings**, select **Backend pools**, and then select **Add**.
   
1. On the **Add a backend pool** page, type or select the following values:
   
   - **Name**: Type *MyBackendPool*.
   - **Associated to**: Drop down and select **Availability set**.
   - **Availability set**: Select **MyAvailabilitySet**.
   
1. Select **Add a target network IP configuration**. 
   1. Add each virtual machine (**MyVM1** and **MyVM2**) that you created to the back-end pool.
   2. After you add each machine, drop down and select its **Network IP configuration**. 
   
   >[!NOTE]
   >Do not add **MyTestVM** to the pool. 
   
1. Select **OK**.
   
   ![Add the back-end address pool](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)
   
1. On the **Backend pools** page, expand **MyBackendPool** and make sure both **VM1** and **VM2** are listed.

### Create a health probe

To allow the load balancer to monitor VM status, you use a health probe. The health probe dynamically adds or removes VMs from the load balancer rotation based on their response to health checks. 

**To create a health probe to monitor the health of the VMs:**

1. Select **All resources** on the left menu, and then select **MyLoadBalancer** from the resource list.
   
1. Under **Settings**, select **Health probes**, and then select **Add**.
   
1. On the **Add a health probe** page, type or select the following values:
   
   - **Name**: Type *MyHealthProbe*.
   - **Protocol**: Drop down and select **HTTP**. 
   - **Port**: Type *80*. 
   - **Path**: Accept */* for the default URI. You can replace this value with any other URI. 
   - **Interval**: Type *15*. Interval is the number of seconds between probe attempts.
   - **Unhealthy threshold**: Type *2*. This value is the number of consecutive probe failures that occur before a VM is considered unhealthy.
   
1. Select **OK**.
   
   ![Add a probe](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### Create a load balancer rule

A load balancer rule defines how traffic is distributed to the VMs. The rule defines the front-end IP configuration for incoming traffic, the back-end IP pool to receive the traffic, and the required source and destination ports. 

The load balancer rule named **MyLoadBalancerRule** listens to port 80 in the front-end **LoadBalancerFrontEnd**. The rule sends network traffic to the back-end address pool **MyBackEndPool**, also on port 80. 

**To create the load balancer rule:**

1. Select **All resources** on the left menu, and then select **MyLoadBalancer** from the resource list.
   
1. Under **Settings**, select **Load balancing rules**, and then select **Add**.
   
1. On the **Add load balancing rule** page, type or select the following values:
   
   - **Name**: Type *MyLoadBalancerRule*.
   - **Frontend IP address:** Type *LoadBalancerFrontEnd* if not present.
   - **Protocol**: Select **TCP**.
   - **Port**: Type *80*.
   - **Backend port**: Type *80*.
   - **Backend pool**: Select **MyBackendPool**.
   - **Health probe**: Select **MyHealthProbe**. 
   
1. Select **OK**.
   
  ![Add a load balancer rule](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## Test the load balancer

Install Internet Information Services (IIS) on the back-end servers, then test the load balancer from MyTestVM by using the private IP address. Each VM will serve a different version of the default IIS web page, so you can see the load balancer distribute requests between the two VMs.

In the portal, on the **Overview** page for **MyLoadBalancer**, find its IP address under **Private IP Address**. Hover over the address and select the **Copy** icon to copy it. In this example, it is **10.3.0.7**. 

### Connect to the VMs with RDP

First, connect to all three VMs with Remote Desktop (RDP). 

**To remote desktop (RDP) into the VMs:**

1. In the portal, select **All resources** on the left menu. From the resource list, select **MyVM1** (or **MyVM2** or **MyTestVM**) in the **MyResourceGroupLB** resource group.
   
1. On the **Overview** page, select **Connect**, and then select **Download RDP file**. 
   
1. Open the RDP file you downloaded, and select **Connect**.
   
1. On the Windows Security screen, select **More choices** and then **Use a different account**. 
   
   Enter username *azureuser* and password *Azure1234567*, and select **OK**.
   
1. Respond **Yes** to any certificate prompt. 
   
   The VM desktop opens in a new window. 

### Install IIS on the back-end VMs

You can use the **Add Roles and Features Wizard**, or PowerShell, to install and configure IIS. 

**To install IIS by using the Add Roles and Features Wizard:**

1. On MyVM1, if **Server Manager** is not already open on the server desktop, browse to **Windows Administrative Tools** > **Server Manager**.
   
1. In **Server Manager**, select **Add roles and features**.
   
   ![Adding server manager role](./media/load-balancer-get-started-internet-portal/servermanager.png)
   
1. In the **Add Roles and Features Wizard**:
   1. On the **Select installation type** page, select **Role-based or feature-based installation**.
   1. On the **Select destination server** page, select **MyVM1**.
   1. On the **Select server role** page, select **Web Server (IIS)**. 
   1. At the prompt to install required tools, select **Add Features**. 
   1. Accept the defaults, and select **Install**. 
   1. When the features are finished installing, select **Close**. 
   
1. Close the RDP connection with MyVM1 by selecting **Disconnect**. Do not shut down the VM.

**To install IIS and update the default web page with PowerShell:**

1. On MyVM2, launch **Windows PowerShell** from the **Start** menu, and run the following commands to install IIS and replace the default IIS web page:
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. Close the RDP connection with MyVM2 by selecting **Disconnect**. Do not shut down the VM.

### Test the load balancer

1. On MyTestVM, open **Internet Explorer**, and respond **OK** to any configuration prompts. 
   
1. Paste or type the load balancer's private IP address (*10.3.0.7*) into the address bar of the browser. 
   
   Either the IIS web server default page, or the message **Hello World from MyVM2**, appears in the browser.
   
1. Refresh the browser to see the load balancer distribute traffic across VMs. You may also need to clear your browser cache between attempts.

   Sometimes the IIS default page appears, and other times the **Hello World** page appears, as the load balancer distributes the requests to each back-end VM. 

   ![IIS default page](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png)  ![New IIS page](./media/tutorial-load-balancer-basic-internal-portal/9-1-load-balancer-test.png)
   
## Clean up resources

To delete the load balancer and all related resources when you no longer need them, open the **MyResourceGroupLB** resource group and select **Delete resource group**.

## Next steps

In this tutorial, you created a Basic-tier internal load balancer. You created and configured network resources, back-end servers, a health probe, and rules to use with the load balancer. You installed IIS on the back-end VMs and used a test VM to test the load balancer private IP address in the browser. 

Next, learn how to load balance VMs across availability zones.

> [!div class="nextstepaction"]
> [Load balance VMs across availability zones](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
