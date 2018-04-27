---
title: Monitor network connections - tutorial - Azure portal | Microsoft Docs
description: Learn how to monitor network connectivity with Azure Network Watcher using the Azure portal.
services: network-watcher
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 

ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload:  infrastructure-services
ms.date: 04/27/2018
ms.author: jdial
ms.custom: mvc
---

# Tutorial: Monitor network connections using the Azure portal

In this tutorial, you deploy two virtual machines (VM), and then use the *connection monitor* capability of Network Watcher to monitor communication from one VM to the other VM. Connection monitor provides monitoring between source and destination IP address and port. It also provides you a hop-by-hop topology, and identifies configuration issues impacting your connection.

## Sign in to Azure 

Sign in to the [Azure portal](https://portal.azure.com).

## Create VMs

### Create the first VM

1. Select **+ Create a resource** found on the upper, left corner of the Azure portal.
2. Select **Compute**, and then select an operating system. In this tutorial, **Windows Server 2016 Datacenter** is used.
3. Enter, or select, the following information, accept the defaults for the remaining settings, and then select **OK**:

    |Setting|Value|
    |---|---|
    |Name|myVm1|
    |User name| Enter a user name of your choosing.|
    |Password| Enter a password of your choosing. The password must be at least 12 characters long and meet the [defined complexity requirements](../virtual-machines/windows/faq.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subscription| Select your subscription.|
    |Resource group| Select **Create new** and enter **myResourceGroup**.|
    |Location| Select **East US**|

4. Select a size for the VM and then select **Select**.
5. Under **Settings**, select **Extensions**. Select **Add extension**, and select **Network Watcher Agent for Windows**, as shown in the following picture:

    ![Network Watcher agent extension](./media/connection-monitor/nw-agent-extension.png)

6. Under **Network Watcher Agent for Windows**, select **Create**, under **Install extension** select **OK**, and then under **Extensions**, select **OK**.
7. Accept the defaults for the remaining **Settings** and select **OK**.
8. Under **Create** of the **Summary**, select **Create** to start VM deployment.

### Create the second VM

Complete the steps in [Create the first VM](#create-the-first-vm) again, with the following changes:

|Step|Setting|Value|
|---|---|---|
| 1 | Select **Ubuntu Server 17.10 VM**|                                                                         |
| 3 | Name                              | myVm2                                                                   |
| 3 | Authentication type               | Paste your SSH public key or select **Password**, and enter a password. |
| 3 | Resource group                    | Select **Use existing** and select **myResourceGroup**.                 |
| 6 | Extensions                        | **Network Agent for Linux**                                             |

The VM takes a few minutes to deploy. Wait for the VM to finish deploying before continuing with the remaining steps.

## Create a connection monitor

Create a connection monitor to monitor communication over TCP port 22 from *myVm1* to *myVm2*.

1. On the left side of the portal, select **All services**.
2. Start typing *network watcher* in the **Filter** box. When **Network Watcher** appears in the search results, select it.
3. Under **MONITORING**, select **Connection monitor**.
4. Select **+ Add**.
5. Enter or select the information for the connection you want to monitor, and then select **Add**. In the example shown in the following picture, the connection monitored is from the *myVm1* VM to the *myVm2* VM over port 22:

    | Setting                  | Value               |
    | ---------                | ---------           |
    | Name                     | myVm1-myVm2(22) |
    | Source                   |                     |
    | Virtual machine          | myVm1               |
    | Destination              |                     |
    | Select a virtual machine |                     |
    | Virtual machine          | myVm2               |
    | Port                     | 22                  |

    ![Add Connection Monitor](./media/connection-monitor/add-connection-monitor.png)

## View a connection monitor

1. Complete steps 1-3 in [Create a connection monitor](#create-a-connection-monitor) to view connection monitoring. You see a list of existing connection monitors, as shown in the following picture:

    ![Connection monitors](./media/connection-monitor/connection-monitors.png)

2. Select the monitor with the name **myVm1-myVm2(22)**, to see details for the monitor, as shown in the following picture:

    ![Monitor details](./media/connection-monitor/vm-monitor.png)

    Note the following information:

    | Item                     | Value                      | Details                                                     |
    | ---------                | ---------                  |--------                                                     |
    | Status                   | Reachable                  | This lets you know whether the endpoint is reachable or not.|
    | AVG. ROUND-TRIP          | This lets you know the round-trip time to make the connection, in milliseconds. Connection monitor probes the connection every 60 seconds, so you can monitor latency over time.                                                |
    | Hops                     | Connection monitor lets you know the hops between the two endpoints. In this example, the connection is between two VMs in the same virtual network, so there is only one hop, to the 10.0.0.5 IP address. If any existing system or custom routes routed traffic between the VMs through a VPN gateway, or network virtual appliance, for example, additional hops are listed.                                                                                                                         |
    | Status (in the table)    | The green check marks for each endpoint lets you know that each endpoint is healthy.   ||

# Create a network security group rule

By default, Azure allows communication over all ports between VMs in the same virtual network. Over time, you, or someone in your organization, might override Azure's default rules, inadvertently causing a communication failure. Complete the following steps which will cause a failure in connection monitoring:

1. In the search box at the top of the portal, enter *myResourceGroup*. When the **myResourceGroup** resource group appears in the search results, select it.
2. Select the **myVm2-nsg** network security group.
3. Select **Inbound security rules**, and then select **Add**, as shown in the following picture:

    ![Inbound security rules](./media/connection-monitor/inbound-security-rules.png)

4. The default rule that allows communication between all VMs in a virtual network is the rule named **AllowVnetInBound**. Create a rule with a higher priority (lower number) than the **AllowVnetInBound** rule that denies inbound communication over port 22. Select, or enter, the following information, accept the remaining defaults, and then select **OK**:

    |Setting|Value|
    |---|---|
    |Destination port ranges| 22 |
    |Name| DenyAllPort22

5. Since connection monitor probes at 60 second intervals, wait a few minutes and then complete steps 1 and 2 in [View a connection monitor](#view-a-connection-monitor) again. The results are different now, as shown in the following picture:

    ![Monitor details fault](./media/connection-monitor/monitor-details-fault.png)

## Next steps

- Learn how to automate packet captures with VM alerts by [Creating an alert-triggered packet capture](network-watcher-alert-triggered-packet-capture.md).
- Determine if certain traffic is allowed in or out of your VM by using [IP flow verify](diagnose-vm-network-traffic-filtering-problem.md).