---
title: Troubleshoot Azure Backup Server | Microsoft Docs
description: Troubleshoot installation, registration of Azure Backup Server, and backup and restore of application workloads.
services: backup
documentationcenter: ''
author: pvrk
manager: shreeshd
editor: ''

ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/24/2017
ms.author: pullabhk;markgal;

---

# Troubleshoot Azure Backup Server

You can troubleshoot errors that you encounter while using Azure Backup Server with the information that's listed in the following table.

## Invalid vault credentials provided 

Follow these [troubleshooting steps](https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues) to resolve this issue.

## The agent operation failed because of a communication error with the DPM agent coordinator service on Server. 

Follow these [troubleshooting steps](https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#registration-and-agent-related-issues) to resolve this issue.

## Setup could not update registry metadata

Follow these [troubleshooting steps](https://docs.microsoft.com/en-us/azure/backup/backup-azure-mabs-troubleshoot#installation-issues) to resolve this issue.


## Installation issues

| Operation | Error details | Workaround |
|-----------|---------------|------------|
|Installation | Setup could not update registry metadata. This update failure could lead to overusage of storage consumption. To avoid this,  update the ReFS Trimming registry entry. | Adjust the registry key, SYSTEM\CurrentControlSet\Control\FileSystem\RefsEnableInlineTrim. Set the value Dword to 1. |
|Installation | Setup could not update registry metadata. This update failure could lead to overusage of storage consumption. To avoid this,  update the Volume SnapOptimization registry entry. | Create the registry key **SOFTWARE\Microsoft Data Protection Manager\Configuration\VolSnapOptimization\WriteIds** with an empty string value. |

## Registration and agent-related issues
| Operation | Error details | Workaround |
| --- | --- | --- |
| Registering to a vault | Invalid vault credentials provided. The file is either corrupted or does not have the latest credentials associated with the recovery service. | To fix this error: <ol><li> Download the latest credentials file from the vault and try again. <br>(OR)</li> <li> If the previous action didn't work, try downloading the credentials to a different local directory or create a new vault. <br>(OR)</li> <li> Try updating the date and time settings as described in [this blog](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/). <br>(OR)</li> <li> Check to see if c:\windows\temp has more than 65000 files. Move stale files to another location or delete the items in the Temp folder. <br>(OR)</li> <li> Check the status of certificates. <br> a. Open **Manage Computer Certificates** (in the Control Panel). <br> b. Expand the **Personal** node and its child node **Certificates**.<br> c.  Remove the certificate **Windows Azure Tools**. <br> d. Retry the registration in the Azure Backup client. <br> (OR) </li> <li> Check to see if any group policy is in place. </li></ol> |
| Pushing agent(s) to protected servers | The agent operation failed because of a communication error with the DPM Agent Coordinator service on \<ServerName>. | **If the recommended action that's shown in the product doesn't work**: <ol><li> If you are attaching a computer from an untrusted domain, follow [these steps](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx). <br> (OR) </li><li> If you are attaching a computer from a trusted domain, troubleshoot using the steps outlined in [this blog](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/). <br>(OR)</li><li> Try disabling antivirus as a troubleshooting step. If it resolves the issue, modify the antivirus settings as suggested in [this article](https://technet.microsoft.com/library/hh757911.aspx).</li></ol> |
| Pushing agent(s) to protected servers | The credentials that are specified for the server are invalid. | **If the recommended action that's shown in the product doesn't work**: <br> Try to install the protection agent manually on the production server as specified in [this article](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual).|
| Azure Backup Agent was unable to connect to the Azure Backup service (ID: 100050) | The Azure Backup Agent was unable to connect to the Azure Backup service. | **If the recommended action that's shown in the product doesn't work**: <br>1. Run following command from elevated prompt: **psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe**. This opens the Internet Explorer window. <br/> 2. Go to **Tools** > **Internet Options** > **Connections** > **LAN settings**. <br/> 3. Verify the proxy settings for the system account. Set Proxy IP and port. <br/> 4. Close Internet Explorer.|
| Azure Backup Agent installation failed | The Microsoft Azure Recovery Services installation failed. All changes made by the Microsoft Azure Recovery Services installation to the system were rolled back. (ID: 4024) | Manually install Azure Agent.


## Configuring protection group
| Operation | Error details | Workaround |
| --- | --- | --- |
| Configuring protection groups | DPM could not enumerate the application component on the protected computer (protected computer name). | Select **Refresh** on the configure protection group UI screen at the relevant datasource/component level. |
| Configuring protection groups | Unable to configure protection | If the protected server is a SQL server, verify that the sysadmin role permissions have been provided to the system account (NTAuthority\System) on the protected computer as described in [this article](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx).
| Configuring protection groups | There is insufficient free space in the storage pool for this protection group. | The disks that are added to the storage pool [should not contain a partition](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Delete any existing volumes on the disks.Then add them to the storage pool.|
| Policy change |The backup policy could not be modified. Error: The current operation failed due to an internal service error [0x29834]. Please retry the operation after some time has passed. If the issue persists, contact Microsoft support. |**Cause:**<br/>This error occurs when security settings are enabled, and you try to reduce retention range below the minimum values specified previously, and you are on an unsupported  version (below MAB version 2.0.9052 and Azure Backup server update 1). <br/>**Recommended Action:**<br/> Set the retention period above the minimum retention period specified (seven days for daily, four weeks for weekly, three weeks for monthly or one year for yearly) to proceed with policy-related udpates. Optionally, another preferred approach is to update the backup agent and Azure Backup Server to leverage all the security updates. |

## Backup
| Operation | Error details | Workaround |
| --- | --- | --- |
| Backup | Replica is inconsistent | Verify that the automatic consistency check option in the Protection Group wizard is turned on. For more information about the causes of replica inconsistency and relevant suggestions, see [Replica is inconsistent](https://technet.microsoft.com/library/cc161593.aspx) on Microsoft TechNet.<br> <ol><li> In case of System State/BMR backup, verify that Windows Server Backup is installed on the protected server.</li><li> Check for space-related issues in the DPM storage pool on the DPM/MABS server, and allocate storage as required.</li><li> Check the state of the Volume shadow copy service on the protected server. If it is in a disabled state, set it to start manually. Start the service on the server. Then go back to the DPM/MABS console and start the sync with the consistency check job.</li></ol>|
| Backup | An unexpected error occurred while the job was running, The device is not ready. | **If the recommended action shown in the product doesn't work**, <br> <ol><li>Set the Shadow Copy Storage space to unlimited on the items in the protection group, and then run the consistency check.<br></li> (OR) <li>Try deleting the existing protection group and creating multiple new ones. Each new protection group should have an individual item in it.</li></ol> |
| Backup | If you are backing up only system state, verify that there is enough free space on the protected computer to store the system state backup. | <ol><li>Verify that the WSB on the protected machine is installed.</li><li>Verify that there is enough space on the protected computer for the system state. The easiest way to do this is to go to the protected computer, open WSB, click through the selections, and then select BMR. The UI then tells you how much space is required for this. Open **WSB** > **Local backup** > **Backup schedule** > **Select Backup Configuration** > **Full server** (size is displayed). Use this size for verification.</li></ol>
| Backup | Online recovery point creation failed | If the error message says "Windows Azure Backup Agent was unable to create a snapshot of the selected volume,"  try increasing the space in replica and recovery point volume.
| Backup | Online recovery point creation failed | If the error message says "The Windows Azure Backup Agent cannot connect to the OBEngine service," verify that the OBEngine exists in the list of running services on the computer. If the OBEngine service is not running, use the "net start OBEngine" command to start the OBEngine service.
| Backup | Online recovery point creation failed | If the error message says "The encryption passphrase for this server is not set. Please configure an encryption passphrase," try configuring an encryption passphrase. If it fails, take the following steps: <br> <ol><li>Verify that the scratch location exists. The location that's mentioned in the registry HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config, with the name **ScratchLocation** should exist.</li><li> If the scratch location exists, try re-registering using the old passphrase. *Whenever you configure an encryption passphrase,  save it in a secure location.*</li><ol>
| Backup | Backup failure for BMR | If THE BMR size is huge, retry after moving some application files to the OS drive. |
| Backup | Re-protecting VMWare VM on a new MABS server does not show as available to add. | VMWare properties are pointed at an old, retired MABS server. To resolve this issue:<br><ol><li>In VCenter (SC-VMM equivalent), go to the **Summary** tab, and then to **Custom Attributes**.</li>  <li>Delete the old MABS server name from the **DPMServer** value.</li>  <li>Go back to the new MABS server and modify the PG.  After you select the **Refresh** button, the VM appears with  with a check box as available to add to protection.</li></ol> |
| Backup | Error while accessing files/shared folders | Try modifying the antivirus settings as suggested in [Run antivirus software on the DPM server](https://technet.microsoft.com/library/hh757911.aspx) on TechNet.|
| Backup | Online recovery point creation jobs for VMware VM fails. DPM encountered an error from VMware while trying to get ChangeTracking information. ErrorCode - FileFaultFault (ID 33621 ) |  <ol><li> Reset the ctk on VMWare for the affected VMs.</li> <li>Check that independent disk is not in place on VMWare.</li> <li>Stop protection for the affected VMs and re-protect with the **Refresh** button. </li><li>Run a CC for the affected VMs.</li></ol>|


## Change passphrase
| Operation | Error details | Workaround |
| --- | --- | --- |
| Change passphrase |The security PIN that was entered is incorrect. Provide the correct security PIN to complete this operation. |**Cause:**<br/> This error occurs when you enter an invalid or expired security PIN while performing a critical operation (like changing a passphrase). <br/>**Recommended Action:**<br/> To complete the operation, you must enter a valid Security PIN. To get the PIN, sign in to the Azure portal and navigate to the Recovery Services vault, Then go to **Settings** > **Properties** > **Generate Security PIN**. Use this PIN to change the passphrase. |
| Change passphrase |Operation failed. ID: 120002 |**Cause:**<br/>This error occurs when security settings are enabled, or when you try to change the passphrase and you are on an  unsupported version.<br/>**Recommended Action:**<br/> To change the passphrase, you must first update the backup agent to the minimum version, which is 2.0.9052. You also need to udpate Azure Backup server to the minimum of update 1, and then enter a valid Security PIN. To get the PIN, sign into the Azure portal and go to the Recovery Services vault. Then go to **Settings** > **Properties** > **Generate Security PIN**. Use this PIN to change the passphrase. |


## Configure email notifications
| Operation | Error details | Workaround |
| --- | --- | --- |
| Setting up email notifications using an Office 365 account | getting Error ID: 2013| **Cause:**<br/> Trying to use Office 365 account <br/> **Recommended action:**<ol><li> 1. The first thing to ensure is that “Allow Anonymous Relay on a Receive Connector” for your DPM server is set up on Exchange. For more information about how to configure this, see [Allow Anonymous Relay on a Receive Connector](http://technet.microsoft.com/en-us/library/bb232021.aspx) on Microsoft TechNet.</li> <li> If you can't use an internal SMTP relay and need to set up by using your Office 365 server, you can set up IIS to be a relay. You need to configure the DPM server to be [able to relay the SMTP to O365 using IIS].(https://technet.microsoft.com/en-us/library/aa995718(v=exchg.65).aspx) </li><li>[!IMPORTANT] On step 3->g->ii, be sure to use user@domain.com format and NOT domain\user <br/> Point DPM to use the local servername as SMTP server, port 587 and then the user email that the emails should come from.<li> The username and password on the DPM SMTP setup page should be a domain account in the domain DPM is on. </li>
>[!NOTE]
>When changing the SMTP server address, make the change to new settings, close the settings box and then reopen to be sure it reflects the new value.  Simply changing and testing may not always take the new settings so testing this way is best practice.

At any time during this process, you can clear these settings out by closing DPM console and editing the following registry keys:<br/> HKLM\SOFTWARE\Microsoft\Microsoft Data Protection Manager\Notification\ <br/> Delete SMTPPassword and SMTPUserName keys. <br/> 

You can add them back in the UI when you launch it again.
