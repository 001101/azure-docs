---
title: Azure Quickstart - Process event streams using Azure CLI | Microsoft Docs
description: Quickly learn to process event streams using Azure CLI
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
ms.date: 02/26/2018
ms.author: sethm

---

# Process event streams using Azure CLI

Azure Event Hubs is a highly scalable data streaming platform and ingestion service capable of receiving and processing millions of events per second. This quickstart shows how to send and receive events to and from an event hub, after using Azure CLI to create an Event Hubs namespace and an event hub within that namespace.

If you do not have an Azure subscription, create a [free account][] before you begin.

## Launch Azure Cloud Shell

Azure Cloud Shell is a free Bash shell that you can run directly from within the Azure portal. It has the Azure CLI preinstalled and configured to use with your account. Click the **Cloud Shell** button in the upper right corner of the Azure portal.

![][1]

This option launches an interactive shell that you can use to run the steps in this article.

![][2]

## Install CLI locally

If you choose to install and use the CLI locally, this article requires that you run the latest version of Azure CLI (2.0.14 or later). To find the version, run the `cli az –version` command. If you need to install or upgrade, see [Install Azure CLI 2.0][].

1. Once CLI is installed, open a command prompt and issue the following command:

   ```azurecli-interactive
   az extension add --name eventhubs
   ```

2. Run the following command to log in to Azure:

   ```azurecli-interactive
   az login
   ```
   This command displays the following text:

   ```Output
   To sign in, use a web browser to open the page https://aka.ms/devicelogin and enter the code ######## to authenticate.
   ```

3. Open the https://aka.ms/devicelogin link in the browser and enter the code to authenticate your Azure login.

## Create a resource group

A resource group is a logical collection of Azure resources. All resources are deployed and managed in a resource group. Create a new resource group with [az group create][] command.

The following example creates a resource group named **eventhubsResourceGroup** in the **East US** region:

```azurecli-interactive
az group create --name eventhubsResourceGroup --location eastus
```

## Create an Event Hubs namespace

An Event Hubs namespace provides a unique scoping container, referenced by its [fully qualified domain name][], in which you create one or more event hubs. The following example creates a namespace in your resource group. Replace `<namespace_name>` with a unique name for your namespace:

```azurecli-interactive
az eventhubs namespace create --name <namespace_name> -l eastus2
```

### Get namespace credentials

To obtain the connection string, which contains the credentials you need to connect to the event hub, open the [Azure portal](https://portal.azure.com). 

1. In the portal list of namespaces, click the newly created namespace.

2. Click **Shared access policies**, and then click **RootManageSharedAccessKey**.
    
3. Click the **Copy** button to copy the **RootManageSharedAccessKey** connection string to the clipboard. Save this connection string in a temporary location, such as Notepad, to use later.
 
## Create an event hub

To create an event hub, specify the namespace under which you want it created. The following example shows how to create an event hub:

```azurecli-interactive
az eventhubs entity create --name <eventhub_name> -l eastus2
```

## Create a storage account for the Event Processor Host

The Event Processor Host is an intelligent agent that simplifies receiving events from Event Hubs by managing persistent checkpoints and parallel receives. For checkpointing, the Event Processor Host requires a storage account. The following example shows how to create a storage account and how to get its keys for access:

```azurecli-interactive
# Create a general purpose standard storage account
az storage account create --name <storage_account_name> --resource-group eventhubsResourceGroup --location eastus2 --sku Standard_RAGRS --encryption blob 

# List the storage account access keys
az storage account keys list --resource-group eventhubsResourceGroup --account-name <storage_account_name>
```

## Download and run the samples

The next step is to download the sample code that sends events to an event hub, and receives those events using the Event Processor Host. Download the [Basic](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/src/main/java/com/microsoft/azure/eventhubs/samples/Basic) sample from GitHub, or clone or download the [azure-event-hubs repo](https://github.com/Azure/azure-event-hubs).

### Send

1. In the Send.java file, replace the `----EventHubsNamespaceName-----` value with the Event Hubs namespace you obtained in the "Create an Event Hubs namespace" section of this article.
2. Replace `----EventHubName-----` with the name of the event hub you created within that namespace.
3. Replace `-----SharedAccessSignatureKeyName-----` with the name of the Primary Key value you obtained previously. Unless you created a new policy, the default is **RootManageSharedAccessKey**.
4. Replace `---SharedAccessSignatureKey----` with the value of the SAS key in the previous step.   

### Receive

1. In the ReceiveByDateTime.java file, replace the `----EventHubsNamespaceName-----` value with the Event Hubs namespace you obtained in the "Create an Event Hubs namespace" section of this article. 
2. Replace `----EventHubName-----` with the name of the event hub you created within that namespace.
3. Replace `-----SharedAccessSignatureKeyName-----` with the name of the Primary Key value you obtained previously. Unless you created a new policy, the default is **RootManageSharedAccessKey**.
4. Replace `---SharedAccessSignatureKey----` with the value of the SAS key in the previous step.

### Run the apps

First, run the **Send** application and observe an event being sent. Then, run the **ReceiveByDateTime** app, and observe the event being received into the Event Processor Host.

## Clean up deployment

Run the following command to remove the resource group, namespace, storage account, and all related resources:

```azurecli-interactive
az group delete --name eventhubsResourceGroup
```

## Next steps

In this article, you created the Event Hubs namespace and other resources required to send and receive events from your event hub. To learn more, continue with the following articles.

* [Send events to your event hub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java)
* [Receive events from your event hub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java)

[1]: ./media/event-hubs-quickstart-namespace-cli/cli1.png
[2]: ./media/event-hubs-quickstart-namespace-cli/cli2.png

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install Azure CLI 2.0]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
