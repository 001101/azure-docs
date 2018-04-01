---
title: Azure Quickstart - process event streams using PowerShell | Microsoft Docs
description: Quickly learn to process event streams using PowerShell
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''

ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: sethm

---

# Process events using PowerShell and .NET Standard

Azure Event Hubs is a highly scalable data streaming platform and ingestion service capable of receiving and processing millions of events per second. This quickstart shows how to use PowerShell to deploy resources, and how to use sample clients to ingest and process events into Event Hubs. 

If you do not have an Azure subscription, start by creating a [free account][].

## Prerequisites

To complete this tutorial, make sure you have installed:

1. [Visual Studio 2017 Update 3 (version 15.3, 26730.01)](http://www.visualstudio.com/vs) or later.
2. [NET Core SDK](https://www.microsoft.com/net/download/windows), version 2.0 or later.

This article requires that you are running the latest version of Azure PowerShell. If you need to install or upgrade, see [Install and Configure Azure PowerShell][].

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## Log on to Azure

Once PowerShell is installed, perform the following steps to install the Event Hubs PowerShell module, and to log on to Azure:

1. Issue the following command to install the Event Hubs PowerShell module:

   ```azurepowershell-interactive
   Install-Module AzureRM.EventHub
   ```

2. Run the following command to log on to Azure:

   ```azurepowershell-interactive
   Login-AzureRmAccount
   ```

3. Change to the current subscription or see the currently active subscription. Replace `MyAzureSub` with the name of the Azure subscription you want to use:

   ```azurepowershell
   Select-AzureRmSubscription -SubscriptionName "MyAzureSub"
   Get-AzureRmContext
   ```

## Provision resources

### Create a resource group

A resource group is a logical collection of Azure resources. All resources are deployed, managed into a resource group.
The following example creates a resource group named eventhubsResourceGroup in the East US region

```powershell
New-AzureRmResourceGroup –Name eventhubsResourceGroup –Location east
```

### Create an Event Hubs namespace

An Event Hubs namespace provides a unique fully qualified domain name in which you can create your event hub. The following example creates a namespace in your resource group. Replace <namespace_name> with a unique name for your namespace.


```powershell
New-AzureRmEventHubNamespace -ResourceGroupName eventhubsResourceGroup  -NamespaceName <namespace_name> -Location eastus
```


### Create an event hub
To create an event hub, specify the namespace under which you want it created. The following example shows how to create an event hub


```powershell
  New-AzureRmEventHub -ResourceGroupName eventhubsResourceGroup   -NamespaceName <eventhubs_namespace_name> -EventHubName <eventhub_name> -Location eastus 
```

### Create a storage account for Event Processor Host

Event Processor Host is an intelligent agent that simplifies receiving events from Event Hubs by managing persistent checkpoints and parallel receives. For check pointing, Event Processor Host requires a storage account. The following example shows how to create a storage account and how to get its keys for access.


```powershell
# create a standard general-purpose storage account 
New-AzureRmStorageAccount -ResourceGroupName eventhubsResourceGroup   
  -Name <storage_account_name>
  -Location eastus
  -SkuName Standard_LRS 

# retrieve the storage account key for accessing it
Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroup -Name $storageAccountName).Value[0]

```

### Get the connection string
Obtain the connection string required to connect to your Event Hubs for ingesting and processing the events.

```powershell
Get-AzureRmEventHubKey -ResourceGroupName eventhubsResourceGroup -NamespaceName <namespace_name> -EventHubName <eventhub_name> -Name RootManageSharedAccessKey
```

## Stream into Event Hubs

You can now start streaming into your Event Hubs. The samples can be downloaded or Git cloned from the [Event Hubs repo](https://github.com/Azure/azure-event-hubs)

### Ingest events

1. Download the [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) from GitHub, or clone the [azure-event-hubs repo](https://github.com/Azure/azure-event-hubs).

2. Navigate to the \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleSender folder.

3. Load SampleSender.sln file from your downloaded location in Visual Studio.

4. Add [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) Nuget package to the project.

5. In Program.cs, replace the following place holders with the resource names and connection strings you have obtained while provisioning the resources:

```netcore-cli
private const string EhConnectionString = "Event Hubs connection string";
private const string EhEntityPath = "Event Hub name";

```
6. Build and run the sample. You can see the events being ingested into your event hub.
![][3]

### Receive and process events

 1. Download the [SampleEphReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) from GitHub, or clone the [azure-event-hubs repo](https://github.com/Azure/azure-event-hubs).

2. Navigate to the \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleEphReceiver folder.

3. Load the SampleEphReceiver.sln solution file into Visual Studio.

4. Add the [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) and [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) Nuget packages to the project.

5. In Program.cs, replace the following constants with the corresponding values for the Event Hubs connection string, event hub name, storage account container name, storage account name and storage account key:

```netcore-cli

private const string EventHubConnectionString = "Event Hubs connection string";
private const string EventHubName = "Event Hub name";
private const string StorageContainerName = "Storage account container name";
private const string StorageAccountName = "Storage account name";
private const string StorageAccountKey = "Storage account key";

```
6. Build and run the sample. You can see the events being received on your sample application
![][4]

You can view the incoming and outgoing events count in the portal for the Event Hubs namespace as shown below:
![][5]

## Clean up deployment

Run the following command to remove the resource group, namespace, storage account, and all related resources. Replace `myResourceGroup` with the name of the resource group you created:

```azurepowershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## Next steps

In this article, you created the Event Hubs namespace and other resources required to send and receive events from an event hub. To learn more, continue with the following articles:

* [Event Hubs PowerShell script sample](https://github.com/Azure/azure-event-hubs/samples/DotNet/Quickstart_PSsample1.ps1)
* [Send events to your event hub](event-hubs-dotnet-standard-getstarted-send.md)
* [Receive events from your event hub](event-hubs-dotnet-standard-getstarted-receive-eph.md)

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[New-AzureRmResourceGroup]: https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png