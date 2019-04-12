---
title: Tutorial - Configure a queue in Azure Service Bus using Ansible | Microsoft Docs
description: Learn how to use Ansible to create an Azure Service Bus queue
keywords: ansible, azure, devops, bash, playbook, service bus, queue
ms.topic: tutorial
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/04/2019
---

# Tutorial: Configure a queue in Azure Service Bus using Ansible

[!INCLUDE [ansible-28-note.md](../../includes/ansible-28-note.md)]

[Azure Service Bus](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-messaging-overview) is a fully managed enterprise [integration](https://azure.microsoft.com/en-us/product-categories/integration/) message broker. Messages are sent to and received from queues. Queues enable you to store messages until the receiving application is available to receive and process them.

This tutorial shows you how to create a Service Bus queue.

## Prerequisites

- [!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-sub.md)]
- [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation1.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation1.md)] [!INCLUDE [ansible-prereqs-for-cloudshell-use-or-vm-creation2.md](../../includes/ansible-prereqs-for-cloudshell-use-or-vm-creation2.md)]

## Create a service bus queue

The code in this section presents a sample Ansible playbook to create a resource group, and a Service Bus namespace in this resource group. Finally create a queue under this namespace

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      location: eastus
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Ensure resource group exist
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          location: "{{ location }}"
    - name: Create a namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
    - name: Create a queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
      register: queue
    - debug:
          var: queue
```

Save this playbook as *servicebus_queue.yml*. To run this Ansible playbook, use the `ansible-playbook` command as follows:

```bash
ansible-playbook servicebus_queue.yml
```

## Create a Shared Access Signatures(SAS) policy for the Service Bus topic

Shared Access Signatures(SAS) are a claims-based authorization mechanism using simple tokens. This section shows you how to create two SAS policies for a Service Bus topic with different privileges.

> [!Tip] 
> The **rights** field represents the privilege a user have with the topic. The value can be one of `manage`, `listen`, `send` or `listen_send`

```yaml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          rights: listen_send
      register: policy
    - debug:
          var: policy
```

Save this playbook as *servicebus_queue_policy.yml*. To run this Ansible playbook, use the `ansible-playbook` command as follows:

```bash
ansible-playbook servicebus_queue_policy.yml
```

## Retrieve information about a namespace

This section shows you how to query a namespace.

> [!Tip]
> The **show_sas_policies** field indicates whether to show the SAS policies under this namespace. This field is set to `False` by default to avoid additional network overhead.

```yaml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
  tasks:
    - name: Get a namespace's information
      azure_rm_servicebus_facts:
          type: namespace
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: ns
    - debug:
          var: ns
```

Save this playbook as *servicebus_namespace_info.yml*. To run this Ansible playbook, use the `ansible-playbook` command as follows:

```bash
ansible-playbook servicebus_namespace_info.yml
```

## Retrieve the information about a queue

This section shows you how to query a queue's information. 

> [!Tip] 
> The **show_sas_policies** field indicates whether to show the SAS policies under this queue. This field is set to `False` to avoid additional network overhead by default.

```yaml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Get a queue's information
      azure_rm_servicebus_facts:
          type: queue
          name: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          show_sas_policies: yes
      register: queue
    - debug:
          var: queue
```

Save this playbook as *servicebus_queue_info.yml*. To run this Ansible playbook, use the `ansible-playbook` command as follows:

```bash
ansible-playbook servicebus_queue_info.yml
```

## Revoke a SAS policy for a queue

This playbook shows you how to delete a SAS policy for a queue.

```yaml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Create a policy with send and listen priviledge
      azure_rm_servicebussaspolicy:
          name: "{{ queue }}-policy"
          queue: "{{ queue }}"
          namespace: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
```

Save this playbook as *servicebus_queue_policy_delete.yml*. To run this Ansible playbook, use the `ansible-playbook` command as follows:

```bash
ansible-playbook servicebus_queue_policy_delete.yml
```

## Clean up

If you don't need these resources, you can delete them by running the following playbook.

```yml
---
- hosts: localhost
  vars:
      resource_group: servicebustest
      namespace: servicebustestns
      queue: servicebustestqueue
  tasks:
    - name: Delete queue
      azure_rm_servicebusqueue:
          name: "{{ queue }}"
          resource_group: "{{ resource_group }}"
          namespace: "{{ namespace }}"
          state: absent
    - name: Delete namespace
      azure_rm_servicebus:
          name: "{{ namespace }}"
          resource_group: "{{ resource_group }}"
          state: absent
    - name: Delete resource group
      azure_rm_resourcegroup:
          name: "{{ resource_group }}"
          state: absent
          force_delete_nonempty: yes
```

Save the preceding playbook as **rg_delete.yml**. To run the playbook, use the **ansible-playbook** command as follows:

```bash
ansible-playbook rg_delete.yml
```

## Next steps
> [!div class="nextstepaction"] 
> [Create an Azure Service Bus topic by using Ansible](https://docs.microsoft.com/azure/ansible/ansible-service-bus-create-topics)