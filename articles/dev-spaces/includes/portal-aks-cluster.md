---
title: "include file"
description: "include file"
ms.custom: "include file"
services: azure-dev-spaces
ms.service: "azure-dev-spaces"
ms.component: "azds-kubernetes"
author: "ghogen"
ms.author: "ghogen"
ms.date: "05/11/2018"
ms.topic: "include"
manager: "douge"
---

## Sign in to the Azure portal

Sign in to the Azure portal at http://portal.azure.com.

## Create a Kubernetes cluster enabled for Azure Dev Spaces

1.  Choose **Create a resource** > search for **Kubernetes** > select **Kubernetes Service** > **Create**.

    Complete the following steps under each heading of the create AKS cluster form.

    - **PROJECT DETAILS**:  select an Azure subscription and a new or existing Azure resource group.
    - **CLUSTER DETAILS**: enter a name, region, version, and DNS name prefix for the AKS cluster.
    - **AUTHENTICATION**: create a new service principal or use an existing one. When using an existing SPN, you need to provide the SPN client ID and secret.
    - **SCALE**: select a VM size for the AKS nodes. The VM size **cannot** be changed once an AKS cluster has been deployed. Also, select the number of nodes to deploy into the cluster. Node count **can** be adjusted after the cluster has been deployed.

   Make sure to choose Kubernetes version 1.9.6 or later.

   ![Kubernetes configuration settings](../media/common/Kubernetes-Create-Cluster-2.PNG)

   Select **Next: Networking** when complete.

1. Make sure that Http Application Routing is enabled.

   ![Enable Http Application Routing](../media/common/Kubernetes-Create-Cluster-3.PNG)

    > [!IMPORTANT]
    > You must be sure to enable Http Application Routing when you create your AKS cluster. It is not possible to change this setting later.

1. Select **Review + create** and then **Create** when complete.
