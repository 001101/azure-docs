---
title: Troubleshooting Docker client errors on Windows by using Visual Studio | Microsoft Docs
description: Troubleshoot problems you encounter when using Visual Studio to create and deploy web apps to Docker on Windows by using Visual Studio 2017.
services: azure-container-service
documentationcenter: na
author: devinb
manager: douge
editor: ''

ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 10/13/2017
ms.author: devinb

---

# Troubleshoot Visual Studio 2017 development with Docker

When you're working with Visual Studio Tools for Docker, you may encounter issues while building or debugging your application. Below are some common trouble shooting steps.

## Volume sharing is not enabled. Enable volume sharing in the Docker CE for Windows settings  (Linux containers only)

To resolve this issue:

1. Right-click **Docker for Windows** in the notification area, and then select **Settings**.
1. Select **Shared Drives** and share the system drive along with the drive where the project resides.

![shared drives](./docker/media/shareddrives.png)

## Unable to start debugging

One reason could be related to having stale debugging components in your userprofile directory.  Try removing the following directories so that the latest debugging components are downloaded.

- %userprofile%\vsdbg
- %userprofile%\onecoremsvsmon

## Errors specific to networking when debugging your application

Follow the link below to obtain a script which will refresh the network related components on your host machine.

`https://github.com/MicrosoftDocs/Virtualization-Documentation/tree/master/windows-server-container-tools/CleanupContainerHostNetworking`

## Microsoft/DockerTools GitHub Repo

For any other issues you encountere, please see

 `https://github.com/microsoft/dockertools/issues`