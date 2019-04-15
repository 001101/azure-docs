---
title: Tutorial - Configure Azure Kubernetes Service (AKS) clusters in Azure using Ansible | Microsoft Docs
description: Learn how to use Ansible to create and manage an Azure Kubernetes Service cluster in Azure
keywords: ansible, azure, devops, bash, cloudshell, playbook, aks, container, aks, kubernetes
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/04/2019
---

# Tutorial: Configure Azure Kubernetes Service (AKS) clusters in Azure using Ansible

[!INCLUDE [ansible-26-note.md](../../includes/ansible-26-note.md)]

[Azure Kubernetes Service (AKS)](/azure/aks/intro-kubernetes) is a managed Kubernetes service that allows you to configure clusters. A cluster consists of a single master node and one or more worker nodes. In this article, Ansible is used to create and configure an AKS cluster.

## Prerequisites

- [!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-sub.md)]
- [!INCLUDE [open-source-devops-prereqs-create-sp.md](../../includes/open-source-devops-prereqs-create-sp.md)]
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

## Create a managed AKS cluster

The sample playbook creates a resource group and an AKS cluster that resides in the resource group.

Save the following playbook as `azure_create_aks.yml`:

```yaml
- name: Create Azure Kubernetes Service
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Create resource group
    azure_rm_resourcegroup:
      name: "{{ resource_group }}"
      location: "{{ location }}"
  - name: Create a managed Azure Container Services (AKS) cluster
    azure_rm_aks:
      name: "{{ aks_name }}"
      location: "{{ location }}"
      resource_group: "{{ resource_group }}"
      dns_prefix: "{{ aks_name }}"
      linux_profile:
        admin_username: "{{ username }}"
        ssh_key: "{{ ssh_key }}"
      service_principal:
        client_id: "{{ client_id }}"
        client_secret: "{{ client_secret }}"
      agent_pool_profiles:
        - name: default
          count: 2
          vm_size: Standard_D2_v2
      tags:
        Environment: Production
```

Before running the playbook, see the following notes:

- The first section within **tasks** defines a resource group named **myResourceGroup** within the **eastus** location.
- The second section within **tasks** defines an AKS cluster named **myAKSCluster** within the **myResourceGroup** resource group.
- For the `your_ssh_key` placeholder, enter your RSA public key in the single-line format - starting with "ssh-rsa" (without the quotes).

Run the playbook using the **ansible-playbook** command:

```bash
ansible-playbook azure_create_aks.yml
```

Running the playbook shows results similar to the following output:

```Output
PLAY [Create AKS] ****************************************************************************************

TASK [Gathering Facts] ********************************************************************************************
ok: [localhost]

TASK [Create resource group] **************************************************************************************
changed: [localhost]

TASK [Create an Azure Container Services (AKS) cluster] ***************************************************
changed: [localhost]

PLAY RECAP *********************************************************************************************************
localhost                  : ok=3    changed=2    unreachable=0    failed=0
```

## Scale AKS nodes

The sample playbook in the previous section defines two nodes. You adjust the number of nodes by modifying the **count** value in the **agent_pool_profiles** block.

Save the following playbook as `azure_configure_aks.yml`:

```yaml
- name: Scale AKS cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    location: eastus
    aks_name: myAKSCluster
    username: azureuser
    ssh_key: "your_ssh_key"
    client_id: "your_client_id"
    client_secret: "your_client_secret"
  tasks:
  - name: Scaling an existed AKS cluster
    azure_rm_aks:
        name: "{{ aks_name }}"
        location: "{{ location }}"
        resource_group: "{{ resource_group }}"
        dns_prefix: "{{ aks_name }}"
        linux_profile:
          admin_username: "{{ username }}"
          ssh_key: "{{ ssh_key }}"
        service_principal:
          client_id: "{{ client_id }}"
          client_secret: "{{ client_secret }}"
        agent_pool_profiles:
          - name: default
            count: 3
            vm_size: Standard_D2_v2
```

Before running the playbook, see the following notes:

- For the `your_ssh_key` placeholder, enter your RSA public key in the single-line format - starting with "ssh-rsa" (without the quotes).

Run the playbook using the **ansible-playbook** command:

```bash
ansible-playbook azure_configure_aks.yml
```

Running the playbook shows results similar to the following output:

```Output
PLAY [Scale AKS cluster] ***************************************************************

TASK [Gathering Facts] ******************************************************************
ok: [localhost]

TASK [Scaling an existed AKS cluster] **************************************************
changed: [localhost]

PLAY RECAP ******************************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## Delete a managed AKS cluster

The sample playbook delete an AKS cluster.

Save the following playbook as `azure_delete_aks.yml`:


```yaml
- name: Delete a managed Azure Container Services (AKS) cluster
  hosts: localhost
  connection: local
  vars:
    resource_group: myResourceGroup
    aks_name: myAKSCluster
  tasks:
  - name:
    azure_rm_aks:
      name: "{{ aks_name }}"
      resource_group: "{{ resource_group }}"
      state: absent
  ```

Run the playbook using the **ansible-playbook** command:

```bash
ansible-playbook azure_delete_aks.yml
```

Running the playbook shows results similar to the following output:

```Output
PLAY [Delete a managed Azure Container Services (AKS) cluster] ****************************

TASK [Gathering Facts] ********************************************************************
ok: [localhost]

TASK [azure_rm_aks] *********************************************************************

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=1    unreachable=0    failed=0
```

## Next steps

> [!div class="nextstepaction"]
> [Tutorial: Scale application in Azure Kubernetes Service (AKS)](/azure/aks/tutorial-kubernetes-scale)