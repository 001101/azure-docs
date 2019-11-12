---
title: Configure Windows Virtual Desktop personal desktop host pool assignment type - Azure
description: How to configure the assignment type for a Windows Virtual Desktop personal desktop host pool.
services: virtual-desktop
author: HeidiLohr

ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
---
# Configure the personal desktop host pool assignment type

Configuring the personal desktop host pool assignment type for a host pool allows you to adjust the Windows Virtual Desktop environment to better suit your needs.

>[!NOTE]
> This does not apply to a pooled host pool because users would not have 1:1 mapping to a session host within the host pool.

## Configure automatic assignment

Automatic assignment is the default assignment type for new personal desktop host pools. With automatic assignment, you must first assign the user to the personal desktop host pool to display the desktop in their feed, but do not need to assign the user to a specific session host. Once assigned, the user can launch the personal desktop in their feed and claim a session host, if one is available. 

First, [download and import the Windows Virtual Desktop PowerShell module](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) to use in your PowerShell session if you haven't already. After that, run the following cmdlet to sign in to your account:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

To configure a host pool to automatically assign users to VMs, run the following PowerShell cmdlet:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Automatic
```

To assign a user to the personal desktop host pool, run the following Powershell cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

## Configure direct assignment

With direct assignment, you must assign the user to both the personal desktop host pool and a specific session host before a user will be able to view and successfully connect to their personal desktop. If the user is only assigned to the host pool but not a specific session host, the user will see an error indicating there are no resources available.

To configure a host pool to require direct assignment of users to session hosts, run the following PowerShell cmdlet:

```powershell
Set-RdsHostPool <tenantname> <hostpoolname> -AssignmentType Direct
```

To assign a user to the personal desktop host pool, run the following PowerShell cmdlet:
```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

To assign a user to a specific session host, run the following PowerShell cmdlet:
```powershell
Set-RdsSessionHost <tenantname> <hostpoolname> -Name <sessionhostname> -AssignedUser <userupn>
```