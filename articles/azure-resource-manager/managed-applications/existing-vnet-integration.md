---
title: Deploy to existing virtual network
description: Describes how to enable users of your managed application to select an existing virtual network. The virtual network can be outside of the managed application.
author: tfitzmac

ms.topic: conceptual
ms.date: 05/10/2020
ms.author: tomfitz

---
# Use existing virtual network with Azure Managed Applications

This article shows you how to define an Azure managed application that integrates with an existing virtual network from the consumer's deployed resources. The managed application lets the consumer decide whether to create a new virtual network or use an existing one. The existing virtual network can be outside of the managed resource group.

## Main template

The following example template deploys a virtual machine and its associated resources. First, let's look at the whole template.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json":::

Notice that the virtual network is [conditionally deployed](../templates/conditional-resource-deployment.md). The consumer passes in a parameter value that indicates whether to use a new or existing virtual network. If the consumer selects a new virtual network, the resource is created. Otherwise, the resource is skipped during deployment.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="111-132" highlight="112":::

Next, let's look at the variables. Notice that **vnetId** contains resource IDs for both new and existing virtual networks. The resource ID for the existing virtual network includes the name of the resource group that contains the virtual network. The **subnetId** is constructed based on the option the consumer has selected.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="98-109" highlight="103-107":::

The network interface is set to the subnet ID variable.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/mainTemplate.json" range="142-163" highlight="157":::

## UI definition

Now, let's look at the **createUiDefinition.json** file.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json":::

In the outputs, notice the value that indicates whether the consumer selected a new or existing virtual network. There's also a managed identity value. This identity is used to access the existing virtual network. The output value for the managed identity must be named **managedIdentity**.

:::code language="json" source="~/resourcemanager-templates/managed-app-existing-vnet/createUiDefinition.json" range="136-148" highlight="141,147":::

