---
title: Manage app groups for Windows Virtual Desktop - Azure
description: Describes how to set up Windows Virtual Desktop tenants in Azure Active Directory.
services: virtual-desktop
author: Heidilohr

ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 02/20/2019
ms.author: helohr
---
# Tutorial: Manage app groups for Windows Virtual Desktop (Preview)

The default app group created for a new host pool also publishes the full desktop. In addition, you can create one or more RemoteApp application groups for the host pool. Follow this tutorial to create a RemoteApp app group and publish individual Start menu apps.

In this tutorial, learn how to:

> [!div class="checklist"]
> * Create a RemoteApp group.
> * Grant access to RemoteApps.

You need the Windows Virtual Desktop PowerShell module to follow the instructions in this article. Install the Windows Virtual Desktop PowerShell module from the PowerShell Gallery by running this cmdlet:

```powershell
PS C:\> Install-Module WindowsVirtualDesktop
```

## Create a RemoteApp group

1. Run the following PowerShell cmdlet to create a new empty RemoteApp group.

 ```powershell
 New-RdsAppGroup <tenantname> <hostpoolname> <appgroupname> -ResourceType “RemoteApp”
 ```

2. (Optional) To verify the application group was created, you can run the following cmdlet to see a list of all application groups for the host pool.

 ```powershell
 Get-RdsAppGroup <tenantname> <hostpoolname>
 ```

3. Run the following cmdlet to get a list of start menu apps on the host pool's virtual machine image. Write down the values for **FilePath**, **IconPath**, **IconIndex**, and other important information for the application you want to publish.

 ```powershell
 Get-RdsHostPoolAvailableApp <tenantname> <hostpoolname>
 ```

4. Run the following cmdlet to publish a new RemoteApp to the application group created in step 1.
   
 ```powershell
 New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> <remoteappname> -Filepath <filepath>  -IconPath <iconpath> -IconIndex <iconindex>
 ```

5. (Optional) Run the following cmdlet to install the application based on appalias. appalias becomes visible when you run the output from step 3.

 ```powershell
 New-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname> <remoteappname> -AppAlias <appalias>
 ```

6. To verify that the app was published, run the following cmdlet.

 ```powershell
 Get-RdsRemoteApp <tenantname> <hostpoolname> <appgroupname>
 ```

7. Repeat steps 1–5 for each application you wish to publish for this app group.
8. Run the following cmdlet to grant users access to the RemoteApps in the app group.

 ```powershell
 Add-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname> -UserPrincipalNames <userupn>
 ```

## Next steps

Once you've created your app groups, you should set up a file share for your users. To learn how to convert a virtual machine into a file share, see the tutorial for how to set up a user profile in Windows Virtual Desktop.

> [!div class="nextstepaction"]
> [Set up user profiles for a host pool](create-host-pools-user-profile-disk.md)