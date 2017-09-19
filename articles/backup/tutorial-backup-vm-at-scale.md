---
title: Back up Azure virtual machines at scale | Microsoft Docs 
description: Simultaneously back up multiple virtual machines to Azure
services: service-name-with-dashes-AZURE-ONLY 
keywords: Don’t add or edit keywords without consulting your SEO champ.
author: markgalioto
ms.author: markgal
ms.date: 09/16/2017
ms.topic: article
ms.service: backup

# Optional fields. Don't forget to remove # if you need a field.
# ms.custom: can-be-multiple-comma-separated
# ms.devlang:devlang-from-white-list
# ms.suite: 
# ms.tgt_pltfrm:
# ms.reviewer:
manager: carmonm
---

# Use Azure portal to back up multiple virtual machines

When you back up data in Azure, you store that data in an Azure resource called a Recovery Services vault. The Recovery Services vault resource is available from the Settings menu of most Azure services. Having the Recovery Services vault integrated into the Settings menu makes it easy to create a new Recovery Services vault, or back up data into an existing Recovery Services vault. This tutorial shows you how to use the Recovery Services vault menu to back up multiple virtual machines at once. 
The steps in this tutorial differ from the quickstart, Back up a virtual machine using the Azure portal, because here you use the Recovery Services vault menu to configure your resources.
 
If you don't have an Azure subscription, create a [free](https://azure.microsoft.com/free/) account before you begin. If you want to create virtual machines, see the tutorial to [create a Windows virtual machine in Azure](../virtual-machines/windows/tutorial-manage-vm.md).

## Log in to the Azure portal

Log in to the [Azure portal](https://portal.azure.com/).

## Create a Recovery Services vault

The Recovery Services vault contains the backup data, and the backup policy applied to the protected virtual machines. Backing up virtual machines is a local process. You cannot back up a virtual machine from one location to a Recovery Services vault in another location. So, for each Azure location that has virtual machines to be backed up, at least one Recovery Services vault must exist in that location.

To create a Recovery Services vault:

1. On the left-hand menu, select **More services** and in the Filter dialog type **Recovery Services**. As you type, the list of resources filters. When you see Recovery Services vaults in the list, select it.

    ![Create Recovery Services Vault step 1](./media/tutorial-backup-vm-at-scale/open-rs-vault-list.png) <br/>

    The Recovery Services vaults menu appears. If there are Recovery Services vaults in the subscription, the vaults are listed.

    ![Create Recovery Services Vault step 2](./media/tutorial-backup-vm-at-scale/list-of-rs-vault.png)
2. On the **Recovery Services vaults** menu, click **Add**.

    The Recovery Services vault menu opens, prompting you for a .

    ![Create Recovery Services Vault step 3](./media/tutorial-backup-vm-at-scale/rs-vault-step-3.png)

3. Enter or choose a **Name**, **Subscription**, **Resource group**, and **Location** for the vault. 

- For complete information on Resource groups, see the [Azure Resource Manager overview](../azure-resource-manager/resource-group-overview.md).
- The Recovery Services vault must be in the same location as the virtual machines being protected. If you are unsure of the virtual machine location, close the vault creation dialog, and go to the list of virtual machines in the Azure portal. If you have virtual machines in multiple regions, create a Recovery Services vault in each region. Create the vault in the first location before going to the next location.

4. Once you fill in the information for the Recovery Services vault, at the bottom of the Recovery Services vault menu, click **Create**.

    It can take several minutes for the Recovery Services vault to be created. Monitor the status notifications in the upper right-hand area of the portal. Once your vault is created, it appears in the list of Recovery Services vaults. If after several minutes you don't see your vault, in the Recovery Services vaults menu bar click **Refresh**.

    ![Click Refresh button](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)</br>

When you create a Recovery Services vault, by default the vault has geo-redundant storage. For the purposes of this tutorial, leave the vault's storage replication option set to geo-redundant storage. If you want you can read more about [geo-redundant](../storage/common/storage-redundancy.md#geo-redundant-storage) and [locally redundant](../storage/common/storage-redundancy.md#locally-redundant-storage) storage options in the [Azure Storage replication overview](../storage/common/storage-redundancy.md).

## Select a backup goal, set policy and define items to protect

Before registering a virtual machine with a vault, run the discovery process to ensure that any new virtual machines that have been added to the subscription are identified. The process queries Azure for the list of virtual machines in the subscription, along with additional information like the cloud service name and the region. In the Azure portal, scenario refers to what you are going to put into the recovery services vault. Policy is the schedule for how often and when recovery points are taken. Policy also includes the retention range for the recovery points.

1. If you already have a Recovery Services vault open, proceed to step 2. Otherwise, on the left-hand menu, click **More services** and in the list of resources, type **Recovery Services** and click **Recovery Services vaults**.

    ![Create Recovery Services Vault step 1](./media/tutorial-backup-vm-at-scale/open-rs-vault-list.png) <br/>

    The list of Recovery Services vaults appears.

    ![View of the Recovery Services vaults list](./media/tutorial-backup-vm-at-scale/list-of-rs-vault.png)

    From the list of Recovery Services vaults, select a vault to open its dashboard.

2. On the vault dashboard menu, click **Backup** to open the Backup menu.

    ![Open Backup blade](./media/tutorial-backup-vm-at-scale/backup-button.png)

    The Backup and Backup Goal menus open.

    ![Open Scenario blade](./media/tutorial-backup-vm-at-scale/select-backup-goal-1.png)

3. On the Backup Goal menu, from the **Where is your workload running** drop-down menu, choose Azure. From the **What do you want to backup** drop-down, choose Virtual machine, then click **OK**.

    These actions register the Virtual Machine extension with the Recovery Services vault. The Backup Goal menu closes and the **Backup policy** menu opens.

    ![Open Scenario blade](./media/tutorial-backup-vm-at-scale/select-backup-goal-2.png)

4. On the Backup policy menu, click **OK** to accept the chosen backup policy.

    The details of the policy are listed under the drop-down menu. The Backup policy menu closes and the **Select virtual machines** menu opens.

    ![Select workload](./media/tutorial-backup-vm-at-scale/select-vms-to-backup.png)

5. In the **Select virtual machines** menu, select all virtual machines to associate with the specified policy and click **OK**. 

    The option of selecting multiple virtual machines allows you to scale the deployment of your backup policy.

6. In the Backup menu, click **Enable Backup** to deploy the policies to the vault and the virtual machines. Deploying the backup policy does not create the initial recovery point for the virtual machine. The backup policy executes on schedule.


## Initial backup

You have enabled backup for the Recovery Services vault, but the initial backup has not been created. It is a disaster recovery best practice to create the first backup. 

To run the initial backup job:

1. On the vault dashboard, click the number under **Backup Items**, or click the **Backup Items** tile. <br/>
  ![Settings icon](./media/backup-azure-vms-first-look-arm/rs-vault-config-vm-back-up-now-1.png)

  The **Backup Items** blade opens.

  ![Back up items](./media/backup-azure-vms-first-look-arm/back-up-items-list.png)

2. On the **Backup Items** menu, select the item.

  ![Settings icon](./media/backup-azure-vms-first-look-arm/back-up-items-list-selected.png)

  The **Backup Items** list opens. <br/>

  ![Backup job triggered](./media/backup-azure-vms-first-look-arm/backup-items-not-run.png)

3. On the **Backup Items** list, click the ellipses **...** to open the Context menu.

  ![Context menu](./media/backup-azure-vms-first-look-arm/context-menu.png)

  The Context menu appears.

  ![Context menu](./media/backup-azure-vms-first-look-arm/context-menu-small.png)

4. On the Context menu, click **Backup now**.

  ![Context menu](./media/backup-azure-vms-first-look-arm/context-menu-small-backup-now.png)

  The Backup Now menu opens.

  ![shows the Backup Now blade](./media/backup-azure-vms-first-look-arm/backup-now-blade-short.png)

5. On the Backup Now menu, click the calendar icon, use the calendar control to select the last day this recovery point is retained, and click **Backup**.

  ![set the last day the Backup Now recovery point is retained](./media/backup-azure-vms-first-look-arm/backup-now-blade-calendar.png)

  Deployment notifications let you know the backup job has been triggered, and that you can monitor the progress of the job on the Backup jobs page. Depending on the size of your VM, creating the initial backup may take a while.

6. To view or track the status of the initial backup, on the vault dashboard, on the **Backup Jobs** tile click **In progress**.

  ![Backup Jobs tile](./media/backup-azure-vms-first-look-arm/open-backup-jobs-1.png)

  The Backup Jobs menu opens.

  ![Backup Jobs tile](./media/backup-azure-vms-first-look-arm/backup-jobs-in-jobs-view-1.png)

  In the **Backup jobs** menu, you can see the status of all jobs. Check if the backup job for your VM is still in progress, or if it has finished. When a backup job is finished, the status is *Completed*.

  
## Clean up resources

Other tutorials in this collection build upon this tutorial. If you plan to continue on to work with subsequent tutorials, do not clean up the resources created in this tutorial. If you do not plan to continue, use the following steps to delete all resources created by this tutorial in the Azure portal.

1. In the list of Recovery Services vaults, select *myVault*.
2. In the myVault dashboard, select the Backup Items tile to open the Backup Items menu.
3. In the Backup Items menu, click Azure Virtual Machine to open the Backup Items menu for myVault.
  In the list, see the names of the virtual machines that are backed up to myVault.
4. Select the elipses for myVM to open the sub-menu, and select Stop backup.
5. In the Stop Backup menu, choose Delete Backup Data, and type myVM in the Backup Item dialog, then click **Stop Backup**. 
6. From the left-hand menu in the Azure portal, click **Resource groups** and then click **myResourceGroup**. 
7. On your resource group page, click **Delete**, type **myResourceGroup** in the text box, and then click **Delete**.


## Next steps

In this tutorial you used the Azure portal to:

> [!div class="checklist"]
> * Create a Recovery Services vault
> * Create a backup and retention policy
> * Back up multiple virtual machines

Continue to the next tutorial to recover Azure virtual machines using virtual machine templates. 

> [!div class="nextstepaction"]
> [Restore VMs using templates](./tutorial-backup-azure-vm.md)
