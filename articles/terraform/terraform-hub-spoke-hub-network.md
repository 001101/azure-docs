---
title: Create a Hub virtual network in hub-spoke network topology
description: Tutorial implements creation of Hub VNet that acts as a common connection point between all the other networks
services: terraform
ms.service: terraform
keywords: terraform, hub and spoke, networks, hybrid networks, devops, virtual machine, azure, VNet peering, hub-spoke, hub. 
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 2/28/2019
---

# Implementing Hub Virtual Network

The Hub virtual network acts as the central point of connectivity to the On-premises network. It is a place to host shared services that can be consumed by the different workloads hosted in the spoke VNets. For the sake of simplicity, we are not implementing any shared services in this tutorial.

[!div class="checklist"]

> * Use HCL (HashiCorp Language) to implement the hub VNet in hub-spoke topology
> * Use Terraform to create Hub Jump box virtual machine
> * Use Terraform to create Hub virtual private network gateway
> * Use Terraform to create Hub and On Premises Gateway connections

## Prerequisites

To implement this tutorial, complete the following articles in order

* [Prerequisites in the Introduction to Hub and Spoke topology article](./hub-spoke-introduction.md)
* [On Premises VNet article](./on-prem.md)

## Components

The hub network consists of the following components. 

* Hub virtual network
* Hub virtual network gateway
* Hub gateway connections 

To following Terraform configuration file defines these resources.

1. Browse to the [Azure portal](http://portal.azure.com).

1. Open [Azure Cloud Shell](/azure/cloud-shell/overview). If you didn't select an environment previously, select **Bash** as your environment.

    ![Cloud Shell prompt](./media/hub-spoke/azure-portal-cloud-shell-button-min.png)

1. Change directories to the `clouddrive` directory.

    ```bash
    cd clouddrive
    ```

1. Change directories to the new directory:

    ```bash
    cd hub-spoke
    ```

## Declare the Hub VNet Terraform configuration files

Create the Terraform configuration file that declares Hub virtual network.

1. In Cloud Shell, create a file named `hub-vnet.tf`.

    ```bash
    vi hub-vnet.tf
    ```

1. Enter insert mode by selecting the I key.

1. Paste the following code into the editor:


    ```JSON
    locals {
      prefix-hub         = "hub"
      hub-location       = "CentralUS"
      hub-resource-group = "hub-vnet-rg"
      shared-key         = "4-v3ry-53cr37-1p53c-5h4r3d-k3y"
    }
    
    resource "azurerm_resource_group" "hub-vnet-rg" {
      name     = "${local.hub-resource-group}"
      location = "${local.hub-location}"
    }
    
    resource "azurerm_virtual_network" "hub-vnet" {
      name                = "${local.prefix-hub}-vnet"
      location            = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.hub-vnet-rg.name}"
      address_space       = ["10.0.0.0/16"]
    
      tags {
        environment = "hub-spoke"
      }
    }
    
    resource "azurerm_subnet" "hub-gateway-subnet" {
      name                 = "GatewaySubnet"
      resource_group_name  = "${azurerm_resource_group.hub-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.hub-vnet.name}"
      address_prefix       = "10.0.255.224/27"
    }
    
    resource "azurerm_subnet" "hub-mgmt" {
      name                 = "mgmt"
      resource_group_name  = "${azurerm_resource_group.hub-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.hub-vnet.name}"
      address_prefix       = "10.0.0.64/27"
    }
    
    resource "azurerm_subnet" "hub-dmz" {
      name                 = "dmz"
      resource_group_name  = "${azurerm_resource_group.hub-vnet-rg.name}"
      virtual_network_name = "${azurerm_virtual_network.hub-vnet.name}"
      address_prefix       = "10.0.0.32/27"
    }
    
    resource "azurerm_network_interface" "hub-nic" {
      name                 = "${local.prefix-hub}-nic"
      location             = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name  = "${azurerm_resource_group.hub-vnet-rg.name}"
      enable_ip_forwarding = true
    
      ip_configuration {
        name                          = "${local.prefix-hub}"
        subnet_id                     = "${azurerm_subnet.hub-mgmt.id}"
        private_ip_address_allocation = "Dynamic"
      }
    
      tags {
        environment = "${local.prefix-hub}"
      }
    }
    
    #Virtual Machine
    resource "azurerm_virtual_machine" "hub-vm" {
      name                  = "${local.prefix-hub}-vm"
      location              = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name   = "${azurerm_resource_group.hub-vnet-rg.name}"
      network_interface_ids = ["${azurerm_network_interface.hub-nic.id}"]
      vm_size               = "${var.vmsize}"
    
      storage_image_reference {
        publisher = "Canonical"
        offer     = "UbuntuServer"
        sku       = "16.04-LTS"
        version   = "latest"
      }
    
      storage_os_disk {
        name              = "myosdisk1"
        caching           = "ReadWrite"
        create_option     = "FromImage"
        managed_disk_type = "Standard_LRS"
      }
    
      os_profile {
        computer_name  = "${local.prefix-hub}-vm"
        admin_username = "${var.username}"
        admin_password = "${var.password}"
      }
    
      os_profile_linux_config {
        disable_password_authentication = false
      }
    
      tags {
        environment = "${local.prefix-hub}"
      }
    }
    
    # Virtual Network Gateway
    resource "azurerm_public_ip" "hub-vpn-gateway1-pip" {
      name                = "hub-vpn-gateway1-pip"
      location            = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.hub-vnet-rg.name}"
    
      allocation_method = "Dynamic"
    }
    
    resource "azurerm_virtual_network_gateway" "vnet-gateway" {
      name                = "hub-vpn-gateway1"
      location            = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.hub-vnet-rg.name}"
    
      type     = "Vpn"
      vpn_type = "RouteBased"
    
      active_active = false
      enable_bgp    = false
      sku           = "VpnGw1"
    
      ip_configuration {
        name                          = "vnetGatewayConfig"
        public_ip_address_id          = "${azurerm_public_ip.hub-vpn-gateway1-pip.id}"
        private_ip_address_allocation = "Dynamic"
        subnet_id                     = "${azurerm_subnet.hub-gateway-subnet.id}"
      }
    }
    
    resource "azurerm_virtual_network_gateway_connection" "hub-onprem-conn" {
      name                = "hub-onprem-conn"
      location            = "${azurerm_resource_group.hub-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.hub-vnet-rg.name}"
    
      type           = "Vnet2Vnet"
      routing_weight = 1
    
      virtual_network_gateway_id      = "${azurerm_virtual_network_gateway.vnet-gateway.id}"
      peer_virtual_network_gateway_id = "${azurerm_virtual_network_gateway.onprem-vpn-gateway.id}"
    
      shared_key = "${local.shared-key}"
    }
    
    resource "azurerm_virtual_network_gateway_connection" "onprem-hub-conn" {
      name                = "onprem-hub-conn"
      location            = "${azurerm_resource_group.onprem-vnet-rg.location}"
      resource_group_name = "${azurerm_resource_group.onprem-vnet-rg.name}"
    
      type                            = "Vnet2Vnet"
      virtual_network_gateway_id      = "${azurerm_virtual_network_gateway.onprem-vpn-gateway.id}"
      peer_virtual_network_gateway_id = "${azurerm_virtual_network_gateway.vnet-gateway.id}"
    
      shared_key = "${local.shared-key}"
    }
    ```
    
1. Exit insert mode by selecting the **Esc** key.

1. Save the file and exit the vi editor by entering the following command:

    ```bash
    :wq
    ```

## Next steps

In this article, we created a central virtual network with its subnets. This network acts as central point and establishes gateway connections between hub and the on premises network. To continue with the entire hub-spoke topology tutorial, complete the remaining steps.

 > [!div class="nextstepaction"] 

 > [Hub network virtual appliance](./terraform-hub-spoke-hub-nva.md))