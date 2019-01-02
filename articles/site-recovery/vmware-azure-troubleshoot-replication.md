---
title: Troubleshoot replication issues for disaster recovery of VMware VMs and physical servers to Azure by using Azure Site Recovery | Microsoft Docs
description: This article provides troubleshooting information for common replication issues during disaster recovery of VMware VMs and physical servers to Azure by using Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill

---
# Troubleshoot replication issues for VMware VMs and physical servers

You might see a specific error message when you protect your VMware virtual machines or physical servers by using Azure Site Recovery. This article describes some common issues you might encounter when you replicate on-premises VMware VMs and physical servers to Azure by using [Site Recovery](site-recovery-overview.md).

## Initial replication issues

In many cases, initial replication failures that we encounter at support are caused by connectivity issues between the source server to the process server or by the process server to Azure. In most cases, you can troubleshoot these issues by completing the steps in the following sections.

### Verify the source machine

1.    At the command line on the source server machine, use Telnet to ping the process server by using the HTTPS port (the default port is 9443) as shown below. The command checks for network connectivity issues or firewall port blocking issues.

	`telnet <PS IP address> <port>`

      > [!NOTE]
      > Use Telnet to test connectivity. Don’t use `ping`. If Telnet isn't installed, complete the steps listed in [Install Telnet client](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx).

      If you can't to connect to the process server, allow inbound port 9443 on the process server. Then, check to see if the problem still exits. In some cases, the process server was behind a DMZ. This might cause this issue to occur.

*     Check the status of the service `InMage Scout VX Agent – Sentinel/OutpostStart`. If the service isn't running, check to see if the problem still exists.   

### Verify the process server

1.    **Check whether the process server is actively pushing data to Azure**

      On the process server machine, open Task Manager (press Ctrl-Shift-Esc). Go to the **Performance** tab, and then select the **Open Resource Monitor** link. From Resource Manager, go to Network tab. Check if cbengine.exe in ‘Processes with Network Activity’ is actively sending large volume (in Mbs) of data.

      ![Enable replication](./media/vmware-azure-troubleshoot-replication/cbengine.png)

      If not, follow the steps listed below:

1.    **Check whether the process server can connect to Azure Blob storage**

      Select and check **cbengine.exe** to view **TCP Connections**. Check to see if there is connectivity from the process server to the Azure Blog storage URL.

      ![Enable replication](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

      If there's no connectivity from the process server to the Azure Blog storage URL, in Control Panel, select **Services**. Check to see if the following services are up and running:

      * cxprocessserver
      * InMage Scout VX Agent – Sentinel/Outpost
      * Microsoft Azure Recovery Services Agent
      * Microsoft Azure Site Recovery Service
      * tmansvc
      
      Start or restart any service that isn't running. Check to see if the problem still occurs.

1.    **Check whether the process server can connect to the Azure Public IP address by using port 443**

      In %programfiles%\Microsoft Azure Recovery Services Agent\Temp, open the latest CBEngineCurr.errlog. Search for **443** and **connection attempt failed**.

      ![Enable replication](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

      If there are issues, at the command line in the process server, use Telnet to ping your Azure Public IP address (masked in the preceding image). You can find your Azure Public IP address in the CBEngineCurr.currLog by using port 443.

      `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

      If you can't connect, check whether the access issue is due to firewall or proxy settings as described in the next step.

1.    **Check whether the IP address-based firewall on the process server blocks access**

      If you use IP address-based firewall rules on the server, download the complete list of [Microsoft Azure datacenter IP ranges](https://www.microsoft.com/download/details.aspx?id=41653). Add the IP address ranges to your firewall configuration to ensure that they allow communication to Azure (and the HTTPS port, 443). Allow IP address ranges for the Azure region of your subscription and for the West US Azure region (used for access control and identity management).

1.    **Check whether a URL-based firewall on the process server blocks access**

      If you use a URL-based firewall rule on the server, ensure that you add the following URLs to the firewall configuration.

      [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

1.    **Check whether proxy settings on the process server block access**

      If you use a proxy server, ensure that the proxy server name is resolved by the DNS server. To check the value that you provided when you set up the configuration server, go to the registry key **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.

      Next, ensure that the same settings are used by the Azure Site Recovery agent to send data: Search for **Microsoft Azure Backup**. Open **Microsoft Azure Backup**, and then select **Action** > **Change Properties**. On the **Proxy Configuration** tab, you should see the proxy address, which should be same as the proxy address that's shown in the registry settings. If not, change it to the same address.

1.    **Check whether the throttle bandwidth is constrained on the process server**

      Increase the bandwidth, and then check whether the problem still occurs.

## The source machine to be protected through Site Recovery isn't listed in the Azure portal

When you try to choose the source machine to enable replication by using Azure Site Recovery, the machine might not be available for you to continue for the following reasons:

* If two virtual machines under the vCenter have the same instance UUID, the first virtual machine discovered by the configuration server is shown in the Azure portal. To resolve this issue, ensure that no two virtual machines have the same instance UUID.
* Ensure that you added the correct vCenter credentials when you set up of the configuration server by using the OVF template or unified setup. To verify the credentials that were added during setup, see [Modify credentials for automatic discovery](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* If the permissions provided to access vCenter don't have the required permissions, failure to discover virtual machines might occur. Ensure that the permissions described in [Prepare an account for automatic discovery](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) are added to the vCenter user account.
* If the virtual machine is already protected through Site Recovery, the virtual machine isn't available for protection. Ensure that the virtual machine you're looking for in the portal isn't already protected by any other user or under a different subscription.

## Protected virtual machines aren't available in the portal

Virtual machines that are replicated under Site Recovery aren't available in the Azure portal if there are duplicate entries in the system. To learn how to delete stale entries and resolve the issue, see [Azure Site Recovery VMware-to-Azure: How to cleanup duplicate/stale entries](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## Next steps

If you need more help, post your question in the [Azure Site Recovery forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). We have an active community, and one of our engineers will be able to assist you.
