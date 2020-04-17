---
# Mandatory fields.
title: Create an Azure Digital Twins instance
titleSuffix: Azure Digital Twins
description: See how to set up an instance of the Azure Digital Twins service.
author: cschormann
ms.author: cschorm # Microsoft employees only
ms.date: 3/17/2020
ms.topic: how-to
ms.service: digital-twins

# Optional fields. Don't forget to remove # if you need a field.
# ms.custom: can-be-multiple-comma-separated
# ms.reviewer: MSFT-alias-of-reviewer
# manager: MSFT-alias-of-manager-or-PM-counterpart
---

# Set up an Azure Digital Twins instance

If you just got access to the preview for the new version of Azure Digital Twins, you may be wondering where to begin.

This how-to will walk you through the basic steps to create and set up a new Azure Digital Twins instance.

In order to complete these steps, you should first have:

* Access to a subscription that is approved for access to Azure Digital Twins
* Access to the [Azure CLI extensions for Digital Twins preview](https://github.com/Azure/azure-digital-twins/tree/private-preview/CLI)

## Prepare

Install the Azure CLI extensions for Azure Digital Twins. During public preview, this is done by running `az extension add --name azure-iot`.

[!INCLUDE [iot-hub-cli-version-info.md](../../includes/iot-hub-cli-version-info.md)]

## Create an Azure Digital Twins instance

To create an Azure Digital Twins instance, open a command prompt or PowerShell window.

First, run this command and complete the associated prompts to log into your Azure account for this session:
```azurecli
az login
```

Next, set your working subscription to the approved subscription that has access to Azure Digital Twins:
```azurecli
az account set -s <your-approved-subscription-ID>
```

For simplicity, you can set a default location for all resources that will be created in this walk-through:
```azurecli
az configure --defaults location="West Central US"
```

If you have never created an Azure Digital Twins instance before in your approved subscription, you will need to register the Azure Digital Twins resource provider. This step only has to be done once per subscription.

```azurecli
 az provider register --namespace 'Microsoft.DigitalTwins'
```

Before you can create an Azure Digital Twins instance, you will need an existing resource group. If you don't have one in your subscription, you can create one with:
```azurecli
az group create -n <your-resource-group-name>
```

Now, you can create your Azure Digital Twins instance with the following command:

```azurecli
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> -g <your-resource-group-name>
```

The output of this command displays information about your newly created instance. Take note of the "hostname" value, as you will need this value later.

## Assign a role to the instance

Before you can use the newly created Azure Digital Twins instance, there is one more step: setting up access control for the instance. 

Every identity (users or service principals) that you want to give access to the Azure Digital Twins instance must have an assigned role for that instance. Azure Digital Twins currently has two built-in roles: "Owner" and "Reader". You can also create your own custom roles via the access control (IAM) pane in the Azure Digital Twins page in the Azure portal.  

To assign a role to a service principal, use this Azure Digital Twins CLI command:

```azurecli
 az dt rbac assign-role -n <your-instance-name> --role owner -g <your-resource-group-name> --assignee <service-principal-name>
```

> [!TIP] 
> The service principal name may not be your actual login name for Azure. If you don't know the principal name, you can find it with this command:
>
> ```azurecli
> az ad user show --displayName <login-name>
> ```

You now have an Azure Digital Twins instance ready to go.

## Next steps

Start learning about the key elements of your Azure Digital Twins instance:
* [Create digital twins and the twin graph](concepts-twins-graph.md)
* [Create a twin model](concepts-models.md)

Or, see how to set up Azure Digital Twins to ingest data from IoT Hub:
* [Ingest telemetry from IoT Hub](how-to-ingest-iot-hub-data.md)