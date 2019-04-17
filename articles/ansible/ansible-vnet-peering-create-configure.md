---
title: Tutorial - Configure Azure virtual network peering using Ansible | Microsoft Docs
description: Learn how to use Ansible to connect virtual networks with virtual network peering.
keywords: ansible, azure, devops, bash, playbook, networking, peering
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/04/2019
---

# Tutorial: Configure Azure virtual network peering using Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Virtual network peering](/azure/virtual-network/virtual-network-peering-overview) (VNet peering) allows you to seamlessly connect two Azure virtual networks. Once peered, the two virtual networks appear as one for connectivity purposes. 

Traffic is routed between VMs in the same virtual network through private IP addresses. Similarly, traffic between VMs in a peered virtual network is routed through the Microsoft backbone infrastructure. As a result, VMs in different virtual networks can communicate with each other.

In this article, you use Ansible to create and peer two virtual networks.

## Prerequisites

- [!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-sub.md)]
- [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-cloudshell-use-or-vm-creation2.md)]

## Create two resource groups

A resource group is a logical container in which Azure resources are deployed and managed.

The first two tasks in the sample Ansible playbook create two resource groups.

```yaml
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
```

## Create the first virtual network

In this section, create the first virtual network and add a subnet.

```yml
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
```

## Create the second virtual network

In this section, create the second virtual network and add a subnet.

```yml
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
```

## Peer the two virtual networks

Establish virtual network peering between the two virtual networks.

```yml
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true
```

## Delete the virtual network peering

To delete the virtual network peering, run the following task:

```yaml
  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## Get the sample playbook

There are two ways to get the complete sample playbook:
- [Download the playbook](https://github.com/Azure-Samples/ansible-playbooks/blob/master/vnet_peering.yml) and save it to `vnet_peering.yml`.
- Create a new file called `vnet_peering.yml` and copy into it the following contents:

```yml
- hosts: localhost
  tasks:
    - name: Prepare random postfix
      set_fact:
        rpfx: "{{ 1000 | random }}"
      run_once: yes

- name: Connect virtual networks with virtual network peering
  hosts: localhost
  connection: local
  vars:
    resource_group: "{{ resource_group_name }}"
    resource_group_secondary: "{{ resource_group_name }}2"
    vnet_name1: "myVnet{{ rpfx }}"
    vnet_name2: "myVnet{{ rpfx }}2"
    peering_name: peer1
    location: eastus2
  tasks:
  - name: Create a resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create secondary resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group_secondary }}"
      location: "{{ location }}"
  - name: Create first virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefixes: "10.0.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name1 }}"
      address_prefix: "10.0.0.0/24"
      virtual_network: "{{ vnet_name1 }}"
  - name: Ceate second virtual network
    azure_rm_virtualnetwork:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ vnet_name2 }}"
      address_prefixes: "10.1.0.0/16"
  - name: Add subnet
    azure_rm_subnet:
      resource_group: "{{ resource_group }}"
      name: "{{ vnet_name2 }}"
      address_prefix: "10.1.0.0/24"
      virtual_network: "{{ vnet_name2 }}"
  - name: Initial vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group_secondary }}"
        name: "{{ vnet_name2 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Connect vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group_secondary }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name2 }}"
      remote_virtual_network:
        resource_group: "{{ resource_group }}"
        name: "{{ vnet_name1 }}"
      allow_virtual_network_access: true
      allow_forwarded_traffic: true

  - name: Delete vnet peering
    azure_rm_virtualnetworkpeering:
      resource_group: "{{ resource_group }}"
      name: "{{ peering_name }}"
      virtual_network: "{{ vnet_name1 }}"
      state: absent
```

## Run the sample playbook

In this section, run the playbook to test various features explained throughout this article.

In the **vars** section, replace the **{{ resource_group_name }}** placeholder with the name of your resource group.

To run the playbook, use the **ansible-playbook** command as follows:

```bash
ansible-playbook vnet_peering.yml
```

The output looks similar to the following results:

```Output
PLAY [localhost] ***********************************************************************

TASK [Gathering Facts] *****************************************************************
ok: [localhost]

TASK [Prepare random postfix] **********************************************************
ok: [localhost]

PLAY [Connect virtual networks with virtual network peering] ***************************

TASK [Gathering Facts] *****************************************************************
ok: [localhost]

TASK [Create a resource group] *********************************************************
changed: [localhost]

TASK [Create secondary resource group] *************************************************
changed: [localhost]

TASK [Create first virtual network] ****************************************************
changed: [localhost]

TASK [Add subnet] *************************************************************************************
changed: [localhost]

TASK [Ceate second virtual network] *******************************************************************
changed: [localhost]

TASK [Add subnet] *************************************************************************************
changed: [localhost]

TASK [Initial vnet peering] ***************************************************************************
changed: [localhost]

TASK [Connect vnet peering] ***************************************************************************
changed: [localhost]

TASK [Delete vnet peering] ****************************************************************************
changed: [localhost]

PLAY RECAP ********************************************************************************************
localhost                  : ok=12   changed=9    unreachable=0    failed=0    skipped=0   rescued=0    ignored=0
```

## Clean up resources

When no longer needed, delete the resources created in this article. 

Save the following code as `cleanup.yml`:

```bash
- hosts: localhost
  vars:
    resource_group: "{{ resource_group_name }}"
    resource_group_secondary: "{{ resource_group_name }}"
  tasks:
    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group }}"
        force_delete_nonempty: yes
        state: absent

    - name: Delete a resource group
      azure_rm_resourcegroup:
        name: "{{ resource_group_secondary }}"
        force_delete_nonempty: yes
        state: absent
```

In the **vars** section, replace the **{{ resource_group_name }}** placeholder with the name of your resource group.

Run the playbook using the **ansible-playbook** command:

```bash
ansible-playbook cleanup.yml
```

## Next steps

> [!div class="nextstepaction"] 
> [Ansible on Azure](/azure/ansible/)