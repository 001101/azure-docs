---
title: Tutorial - Create an Azure Red Hat OpenShift 4 cluster
description: Learn how to create a Microsoft Azure Red Hat OpenShift cluster using the Azure CLI
author: suvetriv
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
#Customer intent: As a developer, I want learn how to create an Azure Red Hat OpenShift cluster, scale it, and then clean up resources so that I am not charged for what I'm not using.
---

# Tutorial: Create an Azure Red Hat OpenShift 4 cluster

In part one of the series, you'll learn how to:



If you choose to install and use the CLI locally, this tutorial requires that you are running the Azure CLI version 2.0.75 or later. Run `az --version` to find the version. If you need to install or upgrade, see [Install Azure CLI][azure-cli-install].

## Before you begin

### Install the 'az aro' extension
The `az aro` extension allows you to create, access, and delete Azure Red Hat OpenShift clusters directly from the command line using the Azure CLI.

Run the following command to install the `az aro` extension.

```azurecli-interactive
az extension add -n aro --index https://az.aroapp.io/preview
```

If you already have the extension installed, you can update by running the following command.

```azurecli-interactive
az extension update -n aro --index https://az.aroapp.io/preview
```

### Register the preview resource provider

Next, you need to register the `Microsoft.RedHatOpenShift` resource provider in your subscription.

```azurecli-interactive
az provider register -n Microsoft.RedHatOpenShift --wait
```

Verify the extension is registered.

```azurecli-interactive
az -v
```

  You should get an output similar to the below.

```output
...
Extensions:
aro                                0.1.0
...
```

### Obtain a Red Hat pull secret (optional)

A Red Hat pull secret enables your cluster to access Red Hat container registries along with additional content. This step is optional but recommended.

Obtain your pull secret by navigating to https://cloud.redhat.com/openshift/install/azure/aro-provisioned and clicking *Download pull secret*.

You will need to log in to your Red Hat account or create a new Red Hat account with your business email and accept the terms and conditions.

Keep the saved `pull-secret.txt` file somewhere safe - it will be used in each cluster creation.

### Create a virtual network containing two empty subnets

Next, you will create a virtual network containing two empty subnets.

1. **Set the following variables.**

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   ```

1. **Create a resource group**

    An Azure resource group is a logical group in which Azure resources are deployed and managed. When you create a resource group, you are asked to specify a location. This location is where resource group metadata is stored, it is also where your resources run in Azure if you don't specify another region during resource creation. Create a resource group using the [az group create][az-group-create] command.

    The following example creates a resource group named *aro-rg* in the *eastus* location.

    ```azurecli-interactive
    az group create --name aro-rg --location eastus
    ```

    The following example output shows the resource group created successfully:

    ```json
    {
    "id": "/subscriptions/<guid>/resourceGroups/aro-rg",
    "location": "eastus",
    "managedBy": null,
    "name": "aro-rg",
    "properties": {
        "provisioningState": "Succeeded"
    },
    "tags": null
    }
    ```

3. **Create a virtual network.**

    Azure Red Hat OpenShift clusters running OpenShift 4 require a virtual network with two empty subnets, for the master and worker nodes.

    Create a new virtual network in the same resource group you created earlier, for example in *aro-rg*.

    ```azurecli-interactive
    az network vnet create \
    --resource-group aro-rg \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/8
    ```

    The following example output shows the virtual network created successfully:

    ```json
    {
    "newVNet": {
        "addressSpace": {
        "addressPrefixes": [
            "10.0.0.0/8"
        ]
        },
        "id": "/subscriptions/<guid>/resourceGroups/aro-rg/providers/Microsoft.Network/virtualNetworks/aro-vnet",
        "location": "eastus",
        "name": "aro-vnet",
        "provisioningState": "Succeeded",
        "resourceGroup": "aro-rg",
        "type": "Microsoft.Network/virtualNetworks"
    }
    }
    ```

4. **Add an empty subnet for the master nodes.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group aro-rg \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

5. **Add an empty subnet for the worker nodes.**

    ```azurecli-interactive
    az network vnet subnet create \
    --resource-group aro-rg \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.1.0.0/20 \
    --service-endpoints Microsoft.ContainerRegistry
    ```

6. **[Disable subnet private endpoint policies](https://docs.microsoft.com/azure/private-link/disable-private-link-service-network-policy) on the master subnet.** This is required to be able to connect and manage the cluster.

    ```azurecli-interactive
    az network vnet subnet update \
    --name master-subnet \
    --resource-group aro-rg \
    --vnet-name aro-vnet \
    --disable-private-link-service-network-policies true
    ```

## Create the cluster

Run the following command to create a cluster. Optionally, you can pass a pull secret which enables your cluster to access Red Hat container registries along with additional content. Access your pull secret by navigating to the [Red Hat OpenShift Cluster Manager](https://cloud.redhat.com/openshift/install/azure/installer-provisioned) and clicking **Copy Pull Secret**.

```azurecli-interactive
az aro create \
  --resource-group aro-rg \
  --name aro-cluster \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret 'Pull secret from https://cloud.redhat.com/openshift/install/azure/installer-provisioned/' # [OPTIONAL] 
```

>[!NOTE]
> It normally takes about 35 minutes to create a cluster.

>[!IMPORTANT]
> If you choose to specify a custom domain, for example **foo.example.com**, the OpenShift console will be available at a URL such as `https://console-openshift-console.apps.foo.example.com`, instead of the built-in domain `https://console-openshift-console.apps.<random>.<location>.aroapp.io`.
>
> By default, OpenShift uses self-signed certificates for all of the routes created on `*.apps.<random>.<location>.aroapp.io`.  If you choose to use custom DNS after connecting to the cluster, you will need to follow the OpenShift documentation to [configure a custom CA for your ingress controller](https://docs.openshift.com/container-platform/4.3/authentication/certificates/replacing-default-ingress-certificate.html) and a [custom CA for your API server](https://docs.openshift.com/container-platform/4.3/authentication/certificates/api-server.html).
>
> You can follow this article to [create testing certificates to use with your custom domain](../configure/custom-ca.md).
<!-- 
## Next steps

In this article, an Azure Red Hat OpenShift cluster running OpenShift 4 was deployed. You learned how to:

> [!div class="checklist"]
> * Setup the prerequisites and create the required virtual network and subnets
> * Deploy a cluster
> * Connect to the cluster using the `kubeadmin` user -->

Advance to the next article to learn how to connect to the cluster. 
<!-- configure the cluster for authentication using Azure Active Directory. -->

<!-- > [!div class="nextstepaction"]
> [Configure authentication with Azure Active Directory using the command line](configure-azure-ad-cli.md)

> [!div class="nextstepaction"]
> [Configure authentication with Azure Active Directory using the Azure portal and OpenShift web console](configure-azure-ad-cli.md)
 -->

## Next steps

In this part of the tutorial, you learned how to:
> [!div class="checklist"]
> * Setup the prerequisites and create the required virtual network and subnets
> * Deploy a cluster

Advance to the next tutorial:
> [!div class="nextstepaction"]
> [Connect to an Azure Red Hat OpenShift cluster](tutorial-connect-cluster.md)
