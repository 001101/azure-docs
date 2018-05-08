---
title: Set up a Windows dev env for Service Fabric Mesh on Azure
description: Details all of the prerequisites required to create a Service Fabric Application to deploy to Azure Service Fabric Mesh.
services: Azure Service Fabric Mesh
keywords: 
author: thraka
ms.author: adegeo
ms.date: 05/04/2018
ms.topic: how-to
ms.service: service-fabric-mesh
manager: timlt
#Customer intent: As a developer, I need to prepare my local dev environment so that I can write applications and deploy to locally and to Azure.
---

To build and run Azure Service Fabric applications on your Windows development machine, install the Service Fabric runtime, SDK, and tools. You also need to enable execution of the Windows PowerShell scripts included in the SDK.
https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-get-started#enable-powershell-script-execution


## Supported operating system versions
The following operating system versions are supported for development:

* Windows 7
* Windows 8/Windows 8.1
* Windows Server 2012 R2
* Windows Server 2016
* Windows 10

> [!NOTE]
> Windows 7 support:
> - Windows 7 only includes Windows PowerShell 2.0 by default. Service Fabric PowerShell cmdlets requires PowerShell 3.0 or higher. You can [download Windows PowerShell 5.0][powershell5-download] from the Microsoft Download Center.

## Enable PowerShell script execution

Service Fabric uses Windows PowerShell scripts for creating a local development cluster and for deploying applications from Visual Studio. By default, Windows blocks these scripts from running. To enable them, you must modify your PowerShell execution policy. Open PowerShell as an administrator and enter the following command:

```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Force -Scope CurrentUser
```

## Visual Studio

Deploying Service Fabric Applications to Service Fabric Mesh requires Visual Studio 2017. Install version 15.6.0 or greater and enable the following workloads:

- ASP.NET and Web Development
- Azure Development

## Docker for Windows

Install the latest version of [Docker Community Edition for Windows][download-docker]. During the installation, select **Use Windows containers instead of Linux containers** when asked. You will be required to logoff and log back in. After logging back in, you may be prompted to enable Hyper-V. Enable Hyper-V which will force a restart.

## SDK and tools

Install the SDK and Visual Studio tools to create new Service Fabric Applications. Currently, you must remove any existing Service Fabric runtime and SDK from your system. Service Fabric Mesh components cannot be installed alongside the current Service Fabric components.

1. If required, remove any existing Service Fabric SDK and runtime.
2. Install the [Service Fabric Mesh Runtime][download-runtime] with the **/AcceptEULA** flag on the command line.
5. Install the [Service Fabric Mesh SDK][download-sdk].
3. Install the [Visual Studio Service Fabric Mesh tools][download-tools].
4. Open a **new** elevated PowerShell window and run  
`C:\Program Files\Microsoft SDKs\Service Fabric\ClusterSetup\DevClusterSetup.ps1 -CreateOneNodeCluster -UseMachineName`
5. (Optional) Start the local Cluster Manager tool:  
`C:\Program Files\Microsoft SDKs\Service Fabric\Tools\ServiceFabricLocalClusterManager\ServiceFabricLocalClusterManager.exe`
6. Reboot your computer.

## Next steps

Read through the [Deploy a .NET Core app to Service Fabric Mesh](service-fabric-mesh-tutorial-deploy-dotnetcore.md) tutorial.

[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[download-runtime]: http://www.a.com
[download-sdk]: http://www.a.com
[download-tools]: http://www.a.com


