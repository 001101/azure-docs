---
title: DO NOT INDEX.
description: Deploying the Azure File Sync agent. A common text block, shared between migration docs.
author: fauhse
ms.service: storage
ms.topic: migration
ms.date: 2/20/2020
ms.author: fauhse
ms.subservice: files
---

Install the Azure File Sync agent on your Windows Server.
The deployment guide (link below) illustrates that you need to turn off IE enhanced security, such that you can authenticate to Azure without any issues.

Also open PowerShell and install the required PowerShell modules with the following commands. Make sure to install the full module and the NuGet provider when prompted:

```powershell
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.StorageSync
```

If you have any issues reaching the internet from your server, now is the time to solve them. Azure File Sync uses any available network connection to the internet.
Requiring a proxy server to reach the internet is also supported. You can either configure a machine-wide proxy now, or specify a proxy that just file sync will use, during agent installation.

If that means, you need to open up your firewalls for this server, then that might be an acceptable approach to you. At the end of the server installation, after completed server registration, there will be a network connectivity report showing you the exact endpoint URLs in Azure, that file sync needs to communicate with for the region you've selected. The report also tells you the reason why communication is needed. You can use the report to then lock down the firewalls around this server, to specific URLs.

You can also follow a more conservative approach, in which you do not open the firewalls wide, but instead limit the server to communicate to higher-level DNS name spaces - there is more documentation and details available in the [Azure File Sync proxy and firewall settings](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy) article. Since all this describes outbound firewall rules, that limits your exposure either way. Follow your own networking best practices.

At the end of the server *installation* wizard, a server *registration* wizard will pop up.
Register the server to your storage sync service Azure resource from earlier.

These steps are described in more detail in the deployment guide, including the above PowerShell modules you should install first:
[Azure File Sync agent install](../storage-sync-files-deployment-guide.md)

The latest agent should be used and can be downloaded from Microsoft Download Center:
[Azure File Sync - agent](https://aka.ms/AFS/agent "Azure File Sync agent download")

After a successful installation and server registration, you can check that you have successfully completed this step: Navigate to the storage sync service resource in the Azure portal, then follow the left-hand menu to “Registered servers”. You will see your server listed there right away.
