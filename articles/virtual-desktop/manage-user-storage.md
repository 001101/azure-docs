---
title: Manage user storage in Windows Virtual Desktop - Azure
description: How to manage file shares for virtual machines in Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr

ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: helohr
---
# Tutorial: Manage user storage in Windows Virtual Desktop (Preview)

After creating the host pools and managing their apps, you'll need to manage user storage in the Windows Virtual Desktop environment. You can manage user storage by setting up virtual machines (VMs) as file shares.

In this tutorial, learn how to:

> [!div class="checklist"]
> * Create a VM that will act as a file share.
> * Prepare the VM to act as a file share for the User Profile Disk.
> * Enable the host pool to use the file share.

You need the Windows Virtual Desktop PowerShell module to follow the instructions in this article. Install the Windows Virtual Desktop PowerShell module from the PowerShell Gallery by running this cmdlet:

```powershell
PS C:\> Install-Module WindowsVirtualDesktop
```

## Create a new VM that will act as a file share

To create a new VM that will act as a file share:

1. Create a new security group in Active Directory. Keep in mind that Azure Active Directory doesn’t support groups for VMs.  
2. Create a new resource group that will host the VM.
3. Select **Virtual Machines** in the left navigation pane.  
4. On the **Virtual machine** blade, select **+Add**. Choose any “Windows Server” VM, then select **Create**.
5. On the Create virtual machine flow under the Basic blade, enter the required information.
    1. **Name**: the VM name.
    2. **Admin username**: create an admin name you'll remember.
    3. **Admin password**: create a password that meets Azure VM password complexity standards.
    4. **Resource group**: enter the name of the resource group you created in step 1.
6. Follow the rest of the instructions in the workflow to complete the VM setup. The VM will take some time to deploy, so be patient.

## Prepare the VM to act as a file share for the User Profile Disk

To prepare the VM to act as a file share for the User Profile Disk:

1. Join the VMs to the domain.  
2. Add the VMs to the security group you created in [Create a new VM that will act as a file share](manage-user-storage.md#create-a-new-vm-that-will-act-as-a-file-share).
3. Select the **User Profile Disk VM**, then select **Connect** at the top of the VM blade.
4. Add the VM to the security group you created in [Create a new VM that will act as a file share](manage-user-storage.md#create-a-new-vm-that-will-act-as-a-file-share). Keep in mind that once you add session host VMs to the group, you'll need to restart them so the security group counts them as part of it.
5. Connect to the created VM.
6. Copy SetupUVHD.ps1 to the User Profile Disk VM.  
7. Run PowerShell as a local admin.
8. Navigate to the folder you copied SetupUVHD.ps1 to and run the following cmdlet with these parameters:
    1. *PathOfShare*: the full path to drive and folder where the User Profile Disk will be stored.
    2. *NameOfShare*: the name of the folder where the User Profile Disk will be stored.
    3. *SizeInGB*: the User Profile Disk’s file size.
    4. **DomainName\SecurityGroupFromStep1**: the name of the security group where all session host VMs are added.
        
        ```powershell
        .\SetupUVHD.ps1 -UvhdSharePath "<PathOfShare> " -ShareName "NameOfShare" -MaxGB <SizeInGB> -DomainGroupForSessionHosts "<DomainName>\<SecurityGroupFromStep1>"
        ```

9. After running the cmdlet, “Setup Completed Successfully” should appear in the PowerShell window.

## Enable the host pool to use the file share

To enable the host pool to use the file share you created:

1. Open PowerShell as an admin (this requires setting the context to the Windows Virtual Desktop service).
2. Run the following cmdlet to set context to the Remote Desktop Services tenant deployment.

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -DiskPath <\\FileServer\PathOfShare> -EnableUserProfileDisk
```

## Next steps

Now that you have everything set up, it's time to connect to the Windows Virtual Desktop client. To learn how to do this, see the following articles about how to the Windows Virtual Desktop client.

> [!div class="nextstepaction"]
> [Connect to the Remote Desktop client on Windows 7 and Windows 10](connect-windows-7-and-10.md)
> [Connect to Microsoft Remote Desktop on macOS](connect-macos.md)
> [Connect to the Windows Virtual Desktop web client](connect-web.md)