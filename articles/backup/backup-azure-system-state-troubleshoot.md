---
title: Troubleshoot System State Backup with Azure Backup
description: Troubleshoot issues in System State Backup.
services: backup
author: srinathvasireddy
manager: sivan
keywords: how to backup; backup system state
ms.service: backup
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: srinathvasireddy
---

# Troubleshoot System State Backup

This article describes solutions for issues that you might encounter while using System State Backup.

## Pre-requisite

1. Ensure Windows Server Backup is installed and enabled in the server.

   To check the installation status, run the below PowerShell command:

   ```
    PS C:\> Get-WindowsFeature Windows-Server-Backup

   ```
    If the output displays the **Install State** as **available** then it means Windows Server backup feature is available for the installation but not installed on the server. However if Windows Server Backup is not installed then use one of the below methods to install it.

    **Method 1: Install Windows Server Backup using PowerShell**

      To install Windows Server Backup using PowerShell, run the below command:

      ```
      PS C:\> Install-WindowsFeature -Name Windows-Server-Backup
      ```

      This will install Windows Server Backup feature on your Windows Server.

    **Method 2: Install Windows Server Backup using Server Manager**

      To install Windows Server Backup using Server Manager, perform the below:

      1. Go to **Sever Manger** and click **Add roles and features**. The Add roles and features wizard appears.

          ![Dashboard](./media/backup-azure-system-state-troubleshoot/server_management.jpg)

      2. Select **Installation Type** and click **Next**.

          ![Installation Type](./media/backup-azure-system-state-troubleshoot/Installation_type.jpg)

      3. Select a server from the server pool and click **Next**. In the Server Role leave the default selection and click **Next**.
      4. Select **Windows Server Backup** in **Features** tab and click **Next**.

          ![features](./media/backup-azure-system-state-troubleshoot/features.png)

      5. In the **Confirmation** tab, click **Install** to start the installation process.
      6. In the **Results** tab it will display the Windows Server Backup feature is successfully installed on your Windows Server.

          ![result](./media/backup-azure-system-state-troubleshoot/results.jpg)


2. **System Volume information permission**: Ensure that the Local SYSTEM has full control on **System Volume Information** folder located in the volume where windows is installed. Usually this is **C:\System Volume Information**. Windows Server backup can fail if the above permissions are not set correctly

3. **Dependent services**: Ensure the below services are in running state:

**Service Name** | **Startup Type**
--- | ---
Remote Procedure Call(RPC) | Automatic
COM+ Event System(EventSystem) | Automatic
System Event Notification Service(SENS) | Automatic
Volume Shadow Copy(VSS) | Manual
Microsoft Software Shadow Copy Provider(SWPRV) | Manual

4. Validate Windows Server Backup status:

  * Ensure WSB PowerShell is running

    -	Run `Get-WBJob` from an elevated PowerShell and make sure it does not return the following error:

    > [!WARNING]
    > Get-WBJob: The term 'Get-WBJob' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.

      -	If it fails with this error then re-install the Windows Server Backup feature on the server machine as mentioned in the step 1 prerequisites.

  * Ensure WSB backup is working properly, by running the below command from elevated command prompt:

      ` wbadmin start systemstatebackup -backuptarget:X: -quiet `

      > [!NOTE]
      >Replace X with the drive letter of the volume where you want to store the system state backup image.

      - Periodically check the status of the job by running the below command from elevated PowerShell:

          `Get-WBJob`

        -  Once backup job is completed check the final status of the job by running the below command

          `Get-WBJob -Previous 1`

        If the job fails it indicates a WSB issue which would result in MARS agent System State Backups failure.

## Common Errors

### VSS Writer timeout error

**Symptom**:

  -	MARS agent fails with error message: “WSB job failed with VSS errors. Check VSS event logs to resolve the failure”
  -	Following error log is present in VSS Application event logs: “A VSS writer has rejected an event with error 0x800423f2, the writer's timeout expired between the Freeze and Thaw events.”

**Cause 1**: VSS writer is unable to complete in time due to lack of CPU and memory resources on the machine<br/>
**Resolution**: Wait for CPU/memory to be freed up on system or abort processes taking too much memory/CPU and try the operation again.

**Cause 2**: Another backup software is already using the VSS writer, as a result snapshot operation could not complete for this backup<br/>
**Resolution**: Wait for the ongoing backup to complete and try the operation at a later point when no backups are running on the machine.

### Insufficient disk space to grow shadow copies

**Symptom**:

  -	MARS agent fails with error message: “Backup failed as the shadow copy volume could not grow due to insufficient disk space on volumes containing system files”
  -	Following error/warning log is present in volsnap System event logs: “There was insufficient disk space on volume C: to grow the shadow copy storage for shadow copies of C:.  As a result of this failure all shadow copies of volume C: are at risk of being deleted.”

  **Resolution**: Free up space in the highlighted volume in the event log so that there is sufficient space for shadow copies to grow while backup is in progress


### EFI partition locked

**Symptom**:

  -	MARS agent fails with error message: “System state backup failed as the EFI system partition is locked. This can be due to system partition access by a third party security or backup software.”

  **Resolution**:
  -	If a third party security software like Kaspersky, AVASTA so on is running, please disable it and try backup again.
  -	If a third party backup software is running, then wait for it to finish and then try backup again.


## Next steps

- For more information about Windows system state in Resource Manager deployment, see [Back up Windows Server System State](backup-azure-system-state.md)
-
