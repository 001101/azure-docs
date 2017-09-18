---
title: Run a disaster recovery drill for on-premises machines to Azure with Azure Site Recovery | Microsoft Docs
description: Learn about running disaster recovery drill from on-premises to Azure, with Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
editor: ''

ms.assetid: ddd17921-68f4-41c7-ba4c-b767d36f1733
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/18/2017
ms.author: raynew

---
# Run a disaster recovery drill to Azure

he [Azure Site Recovery](site-recovery-overview.md) service contributes to your business continuity and disaster recovery (BCDR) strategy by keeping your business apps up and running available during planned and unplanned outages. Site Recovery manages and orchestrates disaster recovery of on-premises machines and Azure virtual machines (VMs), including replication, failover, and recovery.

This tutorial shows you how to run a disaster recovery drill for on-premises machines to Azure, using a test failover. A drill validates your replication strategy without data loss or downtime, and doesn't affect your production environment. In this tutorial, you learn how to:

> [!div class="checklist"]
> * Set up an isolated network for the test failover
> * Prepare to connect to Azure VMs after failover 
> * Run a test failover for a single machine



## Create a network for test failover

1. Create a test network that's isolated from your production network (the network specified in the **Compute and Network** settings for a VM). By default, an Azure virtual network is isolated when it's created.
2.  Specify the following settings for the network:
    - The same number of subnets as your production network, and the same subnet names.
    - The same IP address range as the production network.

>[!NOTE]
>When running a test failover, Site Recovery tries to create test VMs in a subnet with the same name and address as the VM's production network.   


## Prepare to connect to Azure VMs after failover

To connect to Azure VMs using RDP after failover, do the following:

### Azure VMs running Windows

1. To access over the internet, enable RDP on the on-premises VM before failover. Make sure that TCP, and UDP rules are added for the **Public** profile, and that RDP is allowed in **Windows Firewall** > **Allowed Apps** for all profiles.
2. To access over site-to-site VPN, enable RDP on the on-premises machine. RDP should be allowed in the **Windows Firewall** -> **Allowed apps and features** for **Domain and Private** networks. Check that the operating system's SAN policy is set to **OnlineAll**. [Learn more](https://support.microsoft.com/kb/3031135). There should be no Windows updates pending on the VM when you trigger a failover. If there are, you won't be able to log in to the virtual machine until the update completes. 
3. On the Windows Azure VM after failover, check **Boot diagnostics** to view a screenshot of the VM. If you can't connect, check that the VM is running and review these [troubleshooting tips](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


### Azure VMs running Linux

1. On the on-premises machine before failover, check that the Secure Shell service is set to start automatically on system boot. Check that firewall rules allow an SSH connection.
2. On the Azure VM after failover, allow incoming connections to the SSH port for the network security group rules on the failed over VM, and for the Azure subnet to which it's connected.  [Add a public IP address](site-recovery-monitoring-and-troubleshooting.md#adding-a-public-ip-on-a-resource-manager-virtual-machine) for the VM. You can check **Boot diagnostics** to view a screenshot of the VM.


## Verify VM properties

Before you run a test failover, verify the VM properties, and make sure that the VM complies with [Azure requirements](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

1. In **Protected Items**, click **Replicated Items** > VM.
2. In the **Replicated item** pane, there's a summary of VM information, health status, and the latest available recovery points. Click **Properties** to view more details.
3. In **Compute and Network**, you can modify the Azure name, resource group, target size, [availability set](../virtual-machines/windows/tutorial-availability-sets.md), and managed disk settings.
4. You can view and modify network settings, including the network/subnet in which the Azure VM will be located after failover, and the IP address that will be assigned to it.
5. In **Disks**, you can see information about the operating system and data disks on the VM.



## Run a test failover for a single VM

When you run a test failover, the following happens:

1. A prerequisites check runs to make sure all of the conditions required for failover are in place.
2. Failover processes the data, so that an Azure VM can be created. If select the latest recovery point, a recovery point is created from the data.
3. An Azure VM is created using the data processed in the previous step.

Run the test failover as follows:


1. In **Settings** > **Replicated Items**, click the VM > **+Test Failover**.
2. Select a recovery point to use for the failover:
    - **Latest processed** : Fails the VM over to the latest recovery point that was processed by Site Recovery. The time stamp is shown. With this option, no time is spent processing data, so it provides a low RTO (recovery time objective).
    - **Latest app-consistent**: This option fails over all VMs to the latest app-consistent recovery point. The time stamp is shown.
    - **Custom**: Select any recovery point.

3. In **Test Failover**, select the target Azure network to which Azure VMs will be connected after failover occurs.
4. Click **OK** to begin the failover. You can track progress by clicking on the VM to open its properties. Or you can click the **Test Failover** job in vault name > **Settings** > **Jobs** > **Site Recovery jobs**.
5. After the failover finishes, the replica Azure VM appears in the Azure portal > **Virtual Machines**. Check that the VM is the appropriate size, that it's connected to the right network, and that it's running.
6. If you prepared to connect after failover, you should now be able to connect to the Azure VM.
7. To delete Azure VMs created during the test failover, click **Cleanup test failover** on the recovery plan. In **Notes**, record and save any observations associated with the test failover. 

In some scenarios, failover requires additional processing that takes around eight to ten minutes to complete. You might notice longer test failover times for physical servers, VMware Linux machines, VMware VMs that don't have the DHCP service enables, and VMware VMs that don't have the following boot drivers: storvsc, vmbus, storflt, intelide, atapi.


## Connect to VMs in Azure

If you [prepared to connect to Azure after failover](#prepare-to-connect-to-azure-vms-after-failover), you can connect to the [Windows](../virtual-machines/windows/connect-logon.md) or [Linux](../virtual-machines/linux/quick-create-portal.md#connect-to-virtual-machine) machine.



## Next steps
Run a failover and failback for on-premises [VMware VMs](tutorial-vmware-to-azure-failover-failback.md).
