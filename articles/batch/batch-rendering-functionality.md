---
title: Batch rendering capabilities
description: Specific rendering capabilities in Azure Batch
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: ???
---

# Azure Batch rendering capabilities

In the main, standard Azure Batch capabilities are used to run rendering workloads and applications, but there are some areas where specific support has been added.

For an overview of Batch concepts, including pools, jobs, and tasks see [this article](https://docs.microsoft.com/azure/batch/batch-api-basics).

## Batch Pools

### Rendering application installation

An Azure Marketplace rendering VM image can simply be specified in the pool configuration if only one or more of the pre-installed applications need to be used.

* There is a Windows 2016 image and a CentOS image.
* For an example pool configuration, see the rendering tutorial.

The other standard mechanisms are available if alternative or additional applications are required on the pool VMs:

* A custom image based on a standard Marketplace image or a rendering Marketplace image
  * Using this option, you can configure your VM with the exact applications and specific versions that you require. For more information see [Use a custom image to create a pool of virtual machines](https://docs.microsoft.com/azure/batch/batch-custom-images). Note that Autodesk and Chaos Group have modified Arnold and V-Ray respectively to validate against an Azure Batch licensing service. You will need to ensure you have the versions of these applications with this support, otherwise the pay-per-use licensing won't work. This license validation isn't required for Maya or 3ds Max as the current published versions don't require a license server when running headless (in batch/command-line mode). Please contact Azure support if you're not sure how to proceed with this option.
* Application packages
* Resource files

### Pay-for-use licensing for pre-installed applications

The applications that will be used and have a licensing fee need to be specified in the pool configuration.

* The ‘applicationLicenses’ property needs to be specified when [creating a pool](https://docs.microsoft.com/rest/api/batchservice/pool/add#request-body).  The following values can be specified in the array of strings - "vray", "arnold", "3dsmax", "maya".
* When one or more applications are specified, then the cost of those applications will be charged in addition to the cost of the VMs.  Application prices are listed on the [Azure Batch pricing page](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

The Azure Portal and Batch Explorer display licensing UI that allow applications to be selected and show the application prices.

If an attempt is made to execute an application, but the application hasn’t been specified in the ‘applicationLicenses’ property of the pool configuration, then the application execution will fail with a licensing error and non-zero exit code.

### Environment variables for pre-installed applications

To be able to create the command line for rendering tasks, the installation location of the rendering application executables must be specified.  System environment variables have been created on the Azure Marketplace VM images which can be used instead of having to specify actual paths.  These environment variables are in addition to the [standard Batch environment variables](https://docs.microsoft.com/azure/batch/batch-compute-node-environment-variables) created for each task.


|Application|Application Executable|Environment Variable|
|---------|---------|---------|
|Autodesk 3ds Max 2018|3dsmaxcmdio.exe|3DSMAX_2018_EXEC|
|Autodesk 3ds Max 2019|3dsmaxcmdio.exe|3DSMAX_2019_EXEC|
|Autodesk Maya 2017|render.exe|MAYA_2017_EXEC|
|Autodesk Maya 2018|render.exe|MAYA_2018_EXEC|
|Chaos Group V-Ray Standalone|vray.exe|VRAY_3.60.4_EXEC|
Arnold 2017 command line|kick.exe|ARNOLD_2017_EXEC|
|Arnold 2018 command line|kick.exe|ARNOLD_2018_EXEC|
|Blender|blender.exe|BLENDER_2018_EXEC|

### Azure VM families

As with other workloads, rendering application system requirements vary and performance requirements vary for jobs and projects.  A large variety of VM families are available in Azure depending on your requirements – lowest cost, best price/performance, best performance, etc.
Some rendering applications, such as Arnold, are CPU-based; others such as V-Ray and Blender Cycles can use CPU and/or GPU.
For a description of available VM families and VM sizes [see VM types and sizes](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).

### Low-Priority VMs

As with other workloads, low-priority VMs can be utilized in Batch pools for rendering.  Low-priority VMs perform the same as regular dedicated VMs but utilize surplus Azure capacity and are available for a large discount.  The tradeoff for using low-priority VMs is that those VMs may not be available to be allocated or may be preempted at any time, depending on available capacity. For this reason, low-priority VMs are not going to be suitable for all rendering jobs; e.g. if images take many hours to render then it is likely that having the rendering of those images interrupted and restarted due to VMs being preempted, would not be acceptable.

See [Use low-priority VMs with Batch](https://docs.microsoft.com/azure/batch/batch-low-pri-vms) for more information about the characteristics of low-priority VMs and the various ways to configure them using Batch.

## Jobs and Tasks

No rendering-specific support is present for jobs and tasks.  The main configuration item is the task command line, which needs to reference the required application.
When the Azure Marketplace VM images are used, then the best practice is to use the environment variables to specify the path and application executable.
