---
title: Migrate VMs from AWS to Azure with Azure Site Recovery | Microsoft Docs
description: This article describes how to migrate VMs running in Amazon Web Services (AWS) to Azure, using Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: carmonm
editor: ''

ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 09/16/2017
ms.author: raynew

---
# Migrate Amazon Web Services (AWS) VMs to Azure

This tutorial shows you how to migrate Amazon Web Services (AWS) virtual machines (VMs), to Azure using Site Recovery. In this tutorial, you learn how to:

> [!div class="checklist"]
> * Prepare Azure resources
> * Create a Recovery Services vault for Site Recovery 
> * Deploy a configuration server in AWS
> * Enable replication for VMs
> * Run a disaster recovery drill to make sure everything's working
> * Run a one-time failover to Azure

## Overview

You migrate a VM by enabling replication for it, and running a failover from AWS to Azure. 


## Prerequisites

Here's what you need to do for this tutorial.

- You need to have completed the [Prepare Azure](tutorial-prepare-azure.md) tutorial before starting this tutorial. You need to make sure you have a subscription, virtual network, and storage account prepared..
- You need one or more VMs that you want to migrate. These EC2 instance should be running the 64-bit version of Windows Server 2008 R2 SP1 or later, Windows Server 2012, Windows Server 2012 R2 or Red Hat Enterprise Linux 6.7 (HVM virtualized instances only). The server must have only Citrix PV or AWS PV drivers. Instances running RedHat PV drivers aren't supported.
- Check the [Site Recovery support matrix](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) to make sure that your EC2 instances meet Azure requirements for disk size, VM name etc.
- You need a separate EC2 instance that you can use as the Site Recovery configuration server. This instance must be running Windows Server 2012 R2.    



## Prepare the Ec2 instances for migration

The Mobility service must be installed on each AWS instance that you want to replicate. Site Recovery installs this service automatically when you enable replication for the VM. To install automatically, you need an account that Site Recovery can use to access the VM.

- You can use a domain or local account.
- For Windows VMs, if you're using a local account, disable Remote User Access control. To do this, open a cmd prompt on the VM and type:
	```
	REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.
	```
- For Linux, the account should be root on the source Linux server.

## Create a Recovery Services vault

1. Sign in to the [Azure portal](https://portal.azure.com) 
2. In the left navigation, click **More services** and search for and select **Recovery Services vaults**.
2. In the Recovery Services vaults page, click **+ Add** in the upper left of the page.
3. In **Name**, type *myVault*. 
4. In subscription, select the appropriate one.
4. In Resource Group, select **New** and type *myVaultRG*. 
5. In Location, select *West US*. 
5. To quickly access the vault from the dashboard, select **Pin to dashboard**.
7. When you are done, click **Create**.

## Create the configuration server

Use one of the EC2 instances to create a configuration server and register it with your recovery vault.

1. Configure the proxy on the VM so that it can access the [Service URLs](site-recovery-support-matrix-to-azure.md#service-urls).

2. Download the [Microsoft Azure Site Recovery Unified Setup](http://aka.ms/unifiedinstaller_wus) program. You can download it to your local machine and then copy it over to the VM.

3. In the Azure portal, go to All resources > myVault > Properties and click the **Downlaod** button to download the **Backup Credentials**. Copy the downloaded file over the to VM to use as the **Site Recovery Registration Key**.

4. On the VM, right-click installer you downloaded for the **Microsoft Azure Site Recovery Unified Setup** and select **Run as administrator**. In the setup wizard provide the following values for each page:

	1. In **Before You Begin**, select **Install the configuration server and process server** and then click **Next**.
	2. In **Third Party Software Licesnse**, select **I accept the third party license agreement.** and then click **Next**.
	3. In **Registration**, click browse and navigate to where you put the **Backup Credentials** file and then click **Next**.
	4. In **Internet Settings**, select **Connect to Azure Site Recovery without a proxy server.** and then click **Next**.
	5. In the **Prerequisites Check** page, it will run checks for several items. When it is complete, click **Next**. 
	6. In **MySQL Configuration**, provide the required passwords and then click **Next**.
	7. In **Environment Details**, select **No** and then click **Next**.
	8. In **Install Location**, click **Next** to accept the default.
	9. In **Network Selection**, click **Next** to accept the default.
	10. In **Summary** click **Install**.
	11. **Installation Progress** will show you information about where you are in the installation process. When it is complete, click **Finish**. You may need to OK a reboot for the installation to complete. You might also get a pop-up about a passphrase, copy it to your clipbloard and save it somewhere safe.
    
5. On the VM, run **cspsconfigtool.exe** to create one or more management accounts on the configuration server. Make sure the management accounts have administrator permissions on the EC2 instances that you want to migrate.




## Prepare the infrastructure

1. On the page for your vault, select **Site Recovery** from the **Getting Started** section.
2. Click **Prepare Infrastructure**.

### 1 Protection goal

Select the following values on the **Protection Goal** page:

- **Where are your machines located?** = **On-premises**.
- **Where do you want to replicate your machines?** = select **To Azure**.
- **Are your machines virtualized?** = select "Not virtualized / Other**.

When you are done, click **OK** to move to the next section.

### 2 Source Prepare

On the **Prepare source** page, choose the configuration server that you created earlier from the drop-down and then click **OK**. 

### 3 Target Prepare 

In this section you will be entering in information about the the subscription you used and the resources you created when you went through the [Prepare Azure](tutorial-prepare-azure.md) tutorial before you started this tutorial.

1. In **Subscription**, select the Azure subscription that you used for the [Prepare Azure](tutorial-prepare-azure.md) tutorial.
2. Select **Resource Manager** as the deployment model.
3. Site Recovery checks that you have one or more compatible Azure storage accounts and networks. These should be the resources you created when you went through the [Prepare Azure](tutorial-prepare-azure.md) tutorial.
4. When you are done, click **OK**.



### 4 Replication settings Prepare

You need to create a replication policy, before you can enable replication

1. Click **+ Replicate and Associate**.
2. In **Name**, type **myReplicationPolicy**.
3. Leave the rest of the default settings and click **OK** to create the policy. The new policy is automatically associated with the configuration server. 


Enable replication for each VM you want to migrate.

- Site Recovery will install the Mobility service when replication is enabled.
- When you enable replication for a VM, it can take 15 minutes or longer for changes to take effect and appear in the portal.

1. Click **Replicate application** > **Source**.
2. In **Source**, select the configuration server.
3. In **Machine type**, select **Physical machines**.
4. Select the process server (the configuration server). Then click **OK**.
5. In **Target**, select the subscription and the resource group in which you want to create the Azure VMs after failover. Choose the deployment model that you want to use in Azure (classic or resource management).
6. Select the Azure storage account you want to use for replicating data. 
7. Select the Azure network and subnet to which Azure VMs will connect, when they're created after failover.
8. Select **Configure now for selected machines**, to apply the network setting to all machines you select for protection. Select **Configure later** to select the Azure network per machine. 
9. In **Physical Machines**, and click **+Physical machine**. Specify the name and IP address. Select the operating system of the machine you want to replicate. It takes a few minutes for the servers to be discovered and listed. 
    - The IP address must belong to the EC2 instance you're migrating.
    - The EC2 instance must be acessible on this IP address from the configuration server.
1. In **Properties** > **Configure properties**, select the account that will be used by the process server to automatically install the Mobility service on the machine.
2. In **Replication settings** > **Configure replication settings**, verify that the correct replication policy is selected. 
3. Click **Enable Replication**. You can track progress of the **Enable Protection** job in **Settings** > **Jobs** > **Site Recovery Jobs**. After the **Finalize Protection** job runs the machine is ready for failover.
-->
### 5 Deployment planning Select

In **Have you completed deployment planning?**, select **I will do it later** from the drop-down and then click **OK**.

When you are all done with all 5 sections of **Prepare infrastructure**, click **OK**.
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

## Run a test failover

Run a [test failover](tutorial-dr-drill-azure.md) to make sure everything's working as expected.


## Migrate to Azure

Run a fail over for EC2 instances. 

R1. In **Settings** > **Replicated items** click the AWS instances > **Failover**.
2. In **Failover** select a **Recovery Point** to fail over to. Select the latest recovery point.
3. Select **Shut down machine before beginning failover** if you want Site Recovery to attempt to do a shutdown of source virtual machines before triggering the failover. Failover continues even if shutdown fails. You can follow the failover progress on the **Jobs** page.
4. Check that the VM appears in **Replicated items**. 
5. Right-click each VM > **Complete Migration**. This finishes the migration process, stops replication for the AWS VM, and stops Site Recovery billing for the VM.

    ![Complete migration](./media/tutorial-migrate-aws-to-azure/complete-migration.png)

> [!WARNING]
> **Don't cancel a failover in progress**: Before failover is started, VM replication is stopped. If you cancel a failover in progress, failover stops, but the VM won't replicate again.  


    

## Next steps

[Learn about](site-recovery-azure-to-azure-after-migration.md) replicating Azure VMs to another region after migration to Azure.
