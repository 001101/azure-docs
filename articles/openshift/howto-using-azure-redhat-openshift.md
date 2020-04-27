---
title: Create an Azure Red Hat OpenShift 4.3 Cluster | Microsoft Docs
description: Create a cluster with Azure Red Hat OpenShift 4.3
author: lamek
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
keywords: aro, openshift, az aro, red hat, cli
#Customer intent: As a customer, I want to create an ARO custer using the command line.
---

# Create, access, and manage an Azure Red Hat OpenShift 4.3 Cluster

## Prerequisites

You'll need the following to create an Azure Red Hat OpenShift 4.3 cluster:

- Azure CLI version 2.0.72 or greater
  
- The `az aro` extension

- A virtual network containing two empty subnets, each with no network security group attached.  Your cluster will be deployed into these subnets.

- A cluster Azure Active Directory application (client ID and secret) and service principal, or sufficient AAD permissions for `az aro create` to create an AAD application and service principal for you automatically.

- The resource provider service principal and cluster service principal must each have the Contributor role on the cluster virtual network.  If you have the "User Access Administrator" role on the virtual network, `az aro create` will set up the role assignments for you automatically.

### Install the 'az aro' extension
The `az aro` extension allows you to create, access, and delete Azure Red Hat OpenShift clusters directly from the command line using the Azure CLI.

> [!Note] 
> The `az aro` extension is currenty in preview. It may be changed or removed in a future release.
> To opt-in for the `az aro` extension preview you need to register the `Microsoft.RedHatOpenShift` resource provider.
> 
>    ```console
>    az provider register -n Microsoft.RedHatOpenShift --wait
>    ```

1. Log in to Azure.

   ```console
   az login
   ```

2. Run the following command to install the `az aro` extension:

   ```console
   az extension add -n aro --index https://az.aroapp.io/preview
   ```

3. Verify the `aro` extension is registered.

   ```console
   az -v
   ...
   Extensions:
   aro                                0.3.0
   ...
   ```

### Get a Red Hat pull secret (optional)

A Red Hat pull secret enables your cluster to access Red Hat container registries and additional content. Using a pull secret is optional but recommended.

To get your pull secret:

1. Go to https://cloud.redhat.com/openshift/install/azure/aro-provisioned.
1. Log in to your Red Hat account, or create a new Red Hat account by using your business email; accept the terms and conditions.
1. Select **Download pull secret**.

Save the *pull-secret.txt* file somewhere safe; you will use the file each time you create a cluster.

### Create a virtual network containing two empty subnets

Follow these steps to create a virtual network containing two empty subnets.

1. Set the following variables.

   ```console
   LOCATION=eastus        #the location of your cluster
   RESOURCEGROUP="v4-$LOCATION"    #the name of the resource group where you want to create your cluster
   CLUSTER=cluster        #the name of your cluster
   ```

2. Create a resource group for your cluster.

   ```azurecli
   az group create -g "$RESOURCEGROUP" -l $LOCATION
   ```

3. Create the virtual network.

   ```azurecli
   az network vnet create \
     -g "$RESOURCEGROUP" \
     -n vnet \
     --address-prefixes 10.0.0.0/9 \
     >/dev/null
   ```

4. Add two empty subnets to your virtual network.

   ```azurecli
   for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
     az network vnet subnet create \
       -g "$RESOURCEGROUP" \
       --vnet-name vnet \
       -n "$subnet" \
       --address-prefixes 10.$((RANDOM & 127)).$((RANDOM & 255)).0/24 \
       --service-endpoints Microsoft.ContainerRegistry \
       >/dev/null
   done
   ```

5. Disable network policies for Private Link Service on your virtual network and subnets. This is a requirement for the ARO service to access and manage the cluster.

   ```azurecli
   az network vnet subnet update \
     -g "$RESOURCEGROUP" \
     --vnet-name vnet \
     -n "$CLUSTER-master" \
     --disable-private-link-service-network-policies true \
     >/dev/null
   ```

## Create a cluster

The Azure Red Hat OpenShift management resource represents the cluster and will be placed in the resource group denoted by the `-g` parameter.  Multiple Azure Red Hat OpenShift management resources can co-exist in the same resource group if you wish.

All of the Azure resources associated with the cluster itself will be placed in the resource group denoted by the `--cluster-resource-group` parameter. This resource group must not exist at creation time and will be created by the service. Only one Azure Red Hat OpenShift cluster can exist in a given cluster resource group.

For the full details on each parameter, see the [https://docs.microsoft.com/en-us/cli/azure/aro?view=azure-cli-latest#az-aro-create](`az aro create` documentation).

Run the following command to create a cluster.

```azurecli
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet vnet \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --cluster-resource-group "aro-$CLUSTER" \
  --domain "$CLUSTER" \
  --pull-secret @pull-secret.txt
```

>[!NOTE]
> It normally takes about 35 minutes to create a cluster.

## Access the cluster console

You can find the cluster console URL (of the form `https://console-openshift-console.apps.<random>.<location>.aroapp.io/`) under the Azure Red Hat OpenShift 4.3 cluster resource. Run the following command to view the resource:

```azurecli
az aro list -o table
```

You can log into the cluster using the `kubeadmin` user.  Run the following command to find the password for the `kubeadmin` user:

```dotnetcli
az aro list-credentials -g "$RESOURCEGROUP" -n "$CLUSTER"
```

## Delete a cluster

Run the following command to delete a cluster.

```azurecli
az aro delete -g "$RESOURCEGROUP" -n "$CLUSTER"

# (optional)
for subnet in "$CLUSTER-master" "$CLUSTER-worker"; do
  az network vnet subnet delete -g "$RESOURCEGROUP" --vnet-name vnet -n "$subnet"
done
```
