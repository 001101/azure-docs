---
title: 'Create an Azure private link service using Azure CLI'
description: Learn how to create an Azure private link service using Azure CLI
services: virtual-network
author: KumudD
# Customer intent: As someone with a basic network background, but is new to Azure, I want to create an Azure private link service using Azure CLI
ms.service: virtual-network
ms.topic: article
ms.date: 09/11/2019
ms.author: kumud

---
# Create a private Link service using Azure CLI
This article shows you how to create a private link service in Azure using Azure CLI.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

If you decide to install and use Azure CLI locally instead, this quickstart requires you to use the latest Azure CLI version. To find your installed version, run `az --version`. See [Install Azure CLI](/cli/azure/install-azure-cli) for install or upgrade info.
## Create a private link service
### Create a resource group

Before you can create a virtual network, you have to create a resource group to host the virtual network. Create a resource group with [az group create](/cli/azure/group). This example creates a resource group named *myResourceGroup* in the *westcentralus* location:

```azurecli-interactive
az group create --name myResourceGroup --location westcentralus
```
### Create a virtual network
Create a virtual network with [az network vnet create](/cli/azure/network/az-network-vnet-create). This example creates a default virtual network named *myVirtualNetwork* with one subnet named *mySubnet*:

```azurecli-interactive
az network vnet create --resource-group myResourceGroup --name myVirtualNetwork --address-prefix 10.0.0.0/16  
```
### Create a subnet
Create a subnet for the virtual network with [az network vnet subnet create](/cli/azure/network/az-network-vnet-subnet-create). This example creates a subnet named *mySubnet* in the *myVirtualNetwork* virtual network:

```azurecli-interactive
az network vnet subnet create --resource-group myResourceGroup --vnet-name myVirtualNetwork --name mySubnet --address-prefixes 10.0.0.0/24    
```
### Create a public IP 
Create a public IP with [az network public-ip create](/cli/azure/network/az-network-public-ip-create). This example creates a public IP named *myPublicIP* in resource group named *myResourceGroup*. 
 
```azurecli-interactive
az network public-ip create --resource-group myResourceGroup --name myPublicIP --sku standard
```
### Create a Standard Load Balancer 
Create a standard load balancer with [az network lb create](/cli/azure/network/lb#az-network-lb-create). This example creates a standard load balancer named *myLoadBalancer*  using public IP *myPublicIP* in resource group named *myResourceGroup*. 
```azurecli-interactive
az network lb create --resource-group myResourceGroup --name myLoadBalancer --sku standard --public-ip-address myPublicIP --frontend-ip-name myFrontEnd --backend-pool-name myBackEndPool  
```
### Disable private link service network policies on subnet 
Private link service requires a NAT IP from any subnet of your choice  within a virtual network. Currently, we don’t support Network Policies on these NAT IPs.  Hence, we have to disable the network policies on the subnet. Update the subnet to disable private link service network policies with [az network vnet subnet update](/cli/azure/network/az-network-vnet-subnet-update).

```azurecli-interactive
az network vnet subnet update --resource-group myResourceGroup --vnet-name myVirtualNetwork --name MySubnet --disable-private-link-service-network-policies true 
```
 
### Create a private link service  
 
Create a private link service using standard load balancer frontend IP configuration with [az network private-link-service create](/cli/azure/network/az-network-private-link-service-create). This example creates a private link service named *myPLS* using Standard Load Balancer named *myLoadBalancer* in resource group named *myResourceGroup*. 
 
```azurecli-interactive
az network private-link-service create \
--resource-group myResourceGroup \
--name myPLS \
--vnet-name myVirtualNetwork \
--subnet mySubnet \
--lb-name myLoadBalancer \
--lb-frontend-ip-configs myFrontEnd \
--location westcentralus 
```
Once created, take note of the Private Link Service ID. You will need that later for requesting connection to this service.  
 
At this stage, your private link service is successfully created and is ready to receive the traffic. Note that above example is only to demonstrate creating private link service using Azure CLI.  We haven't configured the load balancer backend pools or any application on the backend pools to listen to the traffic. If you want to see end-to-end traffic flows, you can strongly advised to configure your application behind your Standard Load Balancer.  
 
Next, we will demonstrate how to map this service to a private endpoint in different virtual network using Azure CLI. Again, the example is limited to creating the private endpoint and connecting to private link service created above using Azure CLI. Additionally, you can create virtual machines in the virtual network to send/receive traffic to the private endpoint.        
 
## Private endpoints

### Create the virtual network 
Create a virtual network with [az network vnet create](/cli/azure/network/az-network-vnet-create). This example creates a virtual network named *myPEVNet* in resource group named *myResourcegroup*: 
```azurecli-interactive
az network vnet create \
--resource-group myResourceGroup \
--name myPEVnet \
--address-prefix 10.0.0.0/16  
```
### Create the subnet 
Create a subnet in virtual network with [az network vnet subnet create](/cli/azure/network/az-network-vnet-subnet-create). This example creates a subnet named *mySubnet* in virtual network named *myPEVnet* in resource group named *myResourcegroup*: 

```azurecli-interactive 
az network vnet subnet create \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--address-prefixes 10.0.0.0/24 
```   
## Disable private endpoint network policies on subnet 
Private Endpoint can be created in any subnet of your choice within a virtual network. Currently, we don’t support network policies on private endpoints.  Hence, we have to disable the network policies on the subnet. Update the subnet to disable private endpoint network policies with [az network vnet subnet update](/cli/azure/network/az-network-vnet-subnet-update). 

```azurecli-interactive
az network vnet subnet update \
--resource-group myResourceGroup \
--vnet-name myPEVnet \
--name myPESubnet \
--disable-private-endpoint-network-policies true 
```
## Create private endpoint and connect to private link service 
Create a private endpoint for consuming private link service created above in your virtual network:
  
```azurecli-interactive
az network private-endpoint create \
--resource-group myResourceGroup \
--name myPE \
--vnet-name myPEVnet \
--subnet myPESubnet \
--private-connection-resource-id {PLS_resourceURI} \
--connection-name myPEConnectingPLS \
--location westcentralus 
```
You can get the private-connection-resource-id through az network private-link-service show on private link service. The id will look like:   
/subscriptions/subID/resourceGroups/*resourcegroupname*/providers/Microsoft.Network/privateLinkServices/**privatelinkservicename*> 
 
## Show private link service connections 
 
See connection requests on your private link service  using [az network private-link-service show](/cli/azure/network/az-network-private-link-service-show).    
```azurecli-interactive 
az network private-link-service show --resource-group myResourceGroup --name myPLS 
```
## Next steps
- Learn more about [Azure private link service](private-link-service-overview.md)
 
