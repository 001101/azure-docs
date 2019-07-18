---
title: Create an FSLogix profile container in Windows Virtual Desktop  - Azure
description: How to create an FSLogix profile container in Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr

ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: helohr
---
# Create an FSLogix profile container in Windows Virtual Desktop

Windows Virtual Desktop is in [publicly preview](https://aka.ms/wvdpreview) and we wanted to share this step by step guide on how you can get FSLogix profile
containers on [Azure NetApp Files](https://azure.microsoft.com/en-us/services/netapp/).

We are going to assume that you already have a set of VMs that are part of a Windows Virtual Desktop environment. Information on how to get that available at the official Windows Virtual Desktop documentation [here](https://docs.microsoft.com/en-us/azure/virtual-desktop/tenant-setup-azure-active-directory) or on the blog post in Tech Community [here](https://techcommunity.microsoft.com/t5/Windows-IT-Pro-Blog/Getting-started-with-Windows-Virtual-Desktop/ba-p/391054).

The full documentation for configuring Azure NetApp Files are available [here](https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes). In this article we have modified added steps particular to Windows Virtual Desktop.

This article will not cover best practices for securing access to the Azure NetApp Files share.

## Prerequisites 

-   Windows Virtual Desktop set up and configured
-   [Subscription is enabled for Azure NetApp Files](https://docs.microsoft.com/en-us/azure/azure-netapp-files/azure-netapp-files-register)

## Set up Azure NetApp Files and create an NFS volume

1. Sign in to the [Azure portal](https://microsoft-my.sharepoint.com/personal/stgeorgi_microsoft_com/Documents/RDS%20work/wvd%20and%20fslogix/portal.azure.com). Make sure your account has contributor or administrator permissions.

2. Select the **Azure Cloud Shell** icon to open it.

![](media/42d96dccad38b43b71e915c4300aa8ca.png)

3. Once Azure Cloud Shell is open, select **PowerShell**.

![](media/b60565d35de4bcee50820769836d1ee8.png)

4. If this is your first time using Azure Cloud Shell, create a storage account in the same subscription you keep your Azure NetApp Files and Windows Virtual Desktop.

<!--Ask for clarification for step 4-->

![The storage age with a red arrow pointing at the create storage button.](media/0058ca9f19a387c86319f51d5b4b9562.png)

5. Once Azure Cloud Shell loads, run the following two commands.

```shell
az account set --subscription <subscriptionID>
```

```shell
az provider register --namespace Microsoft.NetApp --wait
```

![](media/3be21e3963ae3ebd7ae051b3d9fa727a.png)

1. In the left side of the window, select **All services**. Enter **Azure NetApp Files** into the search box that appears at the top of the menu.

![](media/6546f1ab07c5c0ef0e04b68b5f426472.png)

7. Select **Azure NetApp Files** in the search results, then select **Create**.

8. Select the **Add** button.
9. When the **New NetApp account** blade opens, enter the following values:

  - For **Name**, enter your NetApp account name.
  - For **Subscription**, enter the subscription for the storage account you set up in step 4.
  - For **Resource group**, enter either an existing resource group you want to use or create a new one.
  - For **Location**, enter the region for your NetApp account. This region must be the same region as your session host VMs.

   >[!NOTE]
   >Currently Azure NetApp Files does not support mounting of a volume across regions.

10. When you're finished, select **Create** to create your NetApp account.

![](media/55dca1c987220ce419042bd1dfb88b56.png)

![](media/516368c855c833539e9b0ee2c8868207.png)

10. In the **Azure NetApp Files** menu, select your new account to create a capacity pool.

![](media/9d20bba46a64e1c78fda4e25e1ddaa3f.png)

![](media/65551cd022276fe801a2bf656e069e4c.png)

![](media/187f244eed643633e1867c7c2e71c06e.png)

11. When the **New capacity pool** blade opens, enter the following values:

  - For **Name**, enter a name for the new capacity pool.
  - For **Service level**, select your desired value from the drop-down menu. We recommend **Premium** for most environments.
  - For **Size (TiB)**, enter the capacity pool size that best fits your needs. The minimum size is 4 TiB.

![](media/d5bb782f55d1e1b2ab49e22910e19a32.png)

12. When you're finished, select **OK**.

13. Next, select **Active Directory connections**, then select the **Join** button to open the **Join Active Directory** page.

![](media/03f68f9f13b185c0521d694b1e9e225e.png)

14. Enter the following values in the **Join Active Directory** page:

  - For **Primary DNS**, enter the IP address of the DNS server in your environment that can resolve the domain name.
  - For **Domain**, enter your fully qualified domain name (FQDN).
  - For **SMB Server (Computer Account) Prefix**, enter the string you want to append to the computer account name.
  - For **Username**, enter the name of the account with permissions to perform domain join.
  - For **Password**, enter the account's password.

  >[!NOTE]
  >It's best practice to confirm that the computer account specified in **Join Active Directory** configuration wizard has appeared in your domain controller under **Computers.**

![](media/53e367f2c72d5902bbfa82a1a7dd77ea.png)

1.  Next, you'll need to create a new volume. Select **Volumes**, then select **Add volume**.

![](media/39f0c687e1cf89fb2448c0470e745418.png)

![](media/3aa9d3d9cd06f12c925e7d3d3dcd9c34.png)

1.  When the **Create a volume** blade opens, enter the following values:

**Volume name** – name for the new volume

**Capacity pool** – capacity pool under which this volume will be create. Use the one we just created.

**Quota (GiB)** – size of volume applicable for your environment.

**Virtual network** – select an existing virtual network that has connectivity to the domain controller.

**Subnet** – create a new subnet. Please note that this subnet will be delegated to Azure NetApp Files.

![](media/b1235bd64cfa165cabfa37a315fcb810.png)

17. Select **Next: Protocol \>\>** this will open tab for configuring access parameters.

18. Select **SMB** as Protocol type. Under **Configuration** specify

**Active Directory –** same as the one we connect in previous step. Please note there is a limitation of one Active Directory per subscription,

**Share name** – name of share that will be used by session host pool and users.

![](media/e36a9f45702b4047dbc3f167f7562313.png)

19. Select **Review + create** at the bottom of the page. This will open the validation page. Once validation has passed select **Create.**

![](media/32c86e33f19308de496233240fd3c784.png)

20. At this point a deployment for the new volume will start. Once deployment has completed the Azure NetApp Files share is ready for use.

![](media/aeeb1c1d87f00537df93756ab74d181f.png)

21. Select **Go to resource** and the **Mount path** will be visible.

![](media/3538527bfdd0d8a76b2ec3ce74f10890.png)

### Configure FSLogix on session host VMs

This section is based on information provided [here](https://docs.microsoft.com/en-us/azure/virtual-desktop/create-host-pools-user-profile).

1. While still remoted in session host VM download and install FSLogix agent from this [link](https://go.microsoft.com/fwlink/?linkid=2084562).

2. Unzip the downloaded file.

3.  to **x64\\Releases** and execute **FSLogixAppsSetup.exe.**

![](media/fdb02247f8a528be7f7d63a550f8a10e.png)

4. Once the installer starts. Select **I agree to the license terms and conditions.** If applicable provide a new key.

5. Select **Install**.

![](media/bc8bad805085ef1875a20b0d845f99a4.png)

6. Navigate to **C:\\Program Files\\FSLogix\\Apps** to confirm the agent installed.

![](media/32f561f6e923afc899019cc9657732f9.png)

7. From the start menu, run **RegEdit** as an administrator.

![](media/d7e6314d663907e4227b1fdc0e62ec0e.png)

8. Navigate to **Computer\\HKEY_LOCAL_MACHINE\\software\\FSLogix**.

![](media/07dcb4497a96f1b8c111a88071ecf34d.png)

9. Create a key named **Profiles**.

![](media/56af5129fe452d55356d6c1817c29f07.png)

10. Create value **Enabled** of type **DWORD** with value of **1.**

![](media/bf055352db16c62a97abf335c45861ee.png)

11. Create value **VHDLocations** of type **Multi-String** with value of the URI for the Azure Files share (\\\\anf-SMB-3863.gt1107.onmicrosoft.com\\anf-Vol).

![](media/a420934cc18803242679cb8de863d767.png)

### Assign users to session host

1. Navigate to PowerShell ISE and login to Windows Virtual Desktop.

![Machine generated alternative text: All Apps Best match Documents Email Web More -5 Windows P App Apps PowerShell C 0 Run as administrator Open file location Pin to Start Pin to taskbar Windows PowerShell Windows PowerShell (x86) Windows PowerShell ISE (x86) Microsoft Azure Active Directory Module for Windows PowerShell Search the web p powershell - see web results Settings (3) Websites (1) p powershell Feedback Windows PowerShell ISE App Open Run as administrator Open file location Pin to Start Pin to taskbar ](media/d553c0b391cee5a1b99c6a2bd415915e.png)

2. Run the following cmdlets:

>   Import-Module Microsoft.RdInfra.RdPowershell

>   (Optional) Install-Module Microsoft.RdInfra.RdPowershell

>   \$brokerurl = "https://rdbroker.wvd.microsoft.com"

>   Add-RdsAccount -DeploymentUrl \$brokerurl

3. When prompted for credentials enter the same user that was granted Tenant Creator role or RDS Owner/RDS Contributor on the Windows Virtual Desktop Tenant.

4. Run the following cmdlets to assign user to remote desktop group

>   \$tenant = "\<your-wvd-tenant\>"

>   \$pool1 = "\<wvd-pool\>"

>   \$appgroup = "Desktop Application Group"

>   \$user1 = "\<user-principal\>"

>   Add-RdsAppGroupUser \$tenant \$pool1 \$appgroup \$user1

>   (Sample: \$pool1 = "airlift2020"

>   \$tenant = "airlift2020"

>   \$appgroup = "Desktop Application Group"

>   \$user1 = "debra.berger\@airlift2020outlook.onmicrosoft.com"

>   Add-RdsAppGroupUser \$tenant \$pool1 \$appgroup \$user1)

### Verify profile user connectivity and access to Azure NetApp File share

1. Open a browser of choice.

2. Navigate to <https://aka.ms/wvdweb>.

3. Sign in with user that has been assigned to the remote desktop group (<debra.berger@airlift2020outlook.onmicrosoft.com>).

![](media/3bc26bb1d5ec0888ec613afd92eafeb9.png)

4. Once user session has been established navigate back to the Azure portal with an administrative account.

5. Open **Azure NetApp Files**, select **Azure NetApp Files** account, and the corresponding volume

![](media/225c51fda15dbbd937616e1f1c302b1e.png)

![](media/85f68f2d847e5238700214d5f33c976a.png)

6. Confirm that space for the FSLogix profile container has been consumed.

![](media/3a10923cb3b8ae93381c88daf1965a0f.png)

7. RDP directly to any VM part of the host pool and open the **File Explorer.** Then navigate to the **Mount path**
(\\\\anf-SMB-3863.gt1107.onmicrosoft.com\\anf-VOL).

Inside that path there will be a **Folder** named \<user SID\>-\<username\>.

![](media/9b7470e05e517e9bd60c8d6e4d0b08d0.png)

Under the folder there will be a profile vhd.

![](media/a2462ee25312ddfd310b625673360c4e.png)