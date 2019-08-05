---
title: Azure Site Recovery troubleshooting for Azure-to-Azure network connectivity issues and errors| Microsoft Docs
description: Troubleshooting errors and issues when replicating Azure virtual machines for disaster recovery
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/05/2019
ms.author: asgang

---
# Troubleshoot Azure-to-Azure VM network connectivity issues

This article describes the common issues related to network connectivity in Azure Site Recovery when replicating and recovering Azure virtual machines from one region to another region and explains how to troubleshoot them. For more information about networking requirements, see the [connectivity requirements for replicating Azure VMs](azure-to-azure-about-networking.md).

## Outbound connectivity for Site Recovery URLs or IP ranges (error code 151037 or 151072)

For Site Recovery replication to work, outbound connectivity to specific URLs or IP ranges is required from the VM. If your VM is behind a firewall or uses network security group (NSG) rules to control outbound connectivity, you might face one of these issues.

### <a name="issue-1-failed-to-register-azure-virtual-machine-with-site-recovery-151195-br"></a>Issue 1: Failed to register Azure virtual machine with Site Recovery (151195) </br>
- **Possible cause** </br>
  - Connection cannot be established to Site Recovery endpoints due to DNS resolution failure.
  - This is more frequently seen during re-protection when you have failed over the virtual machine but the DNS server is not reachable from the DR region.

- **Resolution**
   - If you're using custom DNS then make sure that the DNS server is accessible from the Disaster Recovery region. To check if you have a custom DNS go to the VM> Disaster Recovery network> DNS servers. Try accessing the DNS server from the virtual machine. If it is not accessible then make it accessible by either failing over the DNS server or creating the line of site between DR network and DNS.

    ![com-error](./media/azure-to-azure-troubleshoot-errors/custom_dns.png)


### Issue 2: Site Recovery configuration failed (151196)
- **Possible cause** </br>
  - Connection cannot be established to Office 365 authentication and identity IP4 endpoints.

- **Resolution**
  - Azure Site Recovery required access to Office 365 IPs ranges for authentication.
    If you are using Azure Network security group (NSG) rules/firewall proxy to control outbound network connectivity on the VM, ensure you allow communication to O365 IPranges. Create a [Azure Active Directory (AAD) service tag](../virtual-network/security-overview.md#service-tags) based NSG rule for allowing access to all IP addresses corresponding to AAD
      - If new addresses are added to the Azure Active Directory (AAD) in the future, you need to create new NSG rules.

> [!NOTE]
> If the virtual machines are behind **Standard** internal load balancer then it would not have access to O365 IPs i.e login.microsoftonline.com by default. Either change it to **Basic** internal load balancer type or  create out bound access as mentioned in the [article](https://aka.ms/lboutboundrulescli).

### Issue 3: Site Recovery configuration failed (151197)
- **Possible cause** </br>
  - Connection cannot be established to Azure Site Recovery service endpoints.

- **Resolution**
  - Azure Site Recovery required access to [Site Recovery IP ranges](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges) depending on the region. Make sure that required ip ranges are accessible from the virtual machine.


### Issue 4: A2A replication failed when the network traffic goes through on-premises proxy server (151072)
- **Possible cause** </br>
  - The custom proxy settings are invalid and Azure Site Recovery Mobility Service agent did not auto-detect the proxy settings from IE


- **Resolution**
  1. Mobility Service agent detects the proxy settings from IE on Windows and /etc/environment on Linux.
  2. If you prefer to set proxy only for Azure Site Recovery Mobility Service, then you can provide the proxy details in ProxyInfo.conf located at:</br>
     - ``/usr/local/InMage/config/`` on ***Linux***
     - ``C:\ProgramData\Microsoft Azure Site Recovery\Config`` on ***Windows***
  3. The ProxyInfo.conf should have the proxy settings in the following INI format.</br>
                *[proxy]*</br>
                *Address=http://1.2.3.4*</br>
                *Port=567*</br>
  4. Azure Site Recovery Mobility Service agent supports only ***un-authenticated proxies***.


### Fix the problem
To allow [the required URLs](azure-to-azure-about-networking.md#outbound-connectivity-for-urls) or the [required IP ranges](azure-to-azure-about-networking.md#outbound-connectivity-for-ip-address-ranges), follow the steps in the [networking guidance document](site-recovery-azure-to-azure-networking-guidance.md).


## Next steps
[Replicate Azure virtual machines](site-recovery-replicate-azure-to-azure.md)
