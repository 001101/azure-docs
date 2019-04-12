---
title: Quickstart - Run Ansible playbooks via Bash in Azure Cloud Shell | Microsoft Docs
description: In this quickstart, learn how to perform various Ansible tasks with Bash in Azure Cloud Shell
keywords: ansible, azure, devops, bash, cloudshell, playbook, bash
ms.topic: quickstart
ms.service: ansible
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.date: 04/04/2019
---

# Quickstart: Run Ansible playbooks via Bash in Azure Cloud Shell

In this article, you learn how to use Bash within Azure Cloud Shell to configure an Azure subscription as your Ansible workspace. 

## Prerequisites

- [!INCLUDE [open-source-devops-prereqs-azure-sub.md](../../includes/open-source-devops-prereqs-azure-sub.md)]
- **Configure Azure Cloud Shell** - If you are new to Azure Cloud Shell, the article, [Quickstart for Bash in Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/quickstart), illustrates how to start and configure Cloud Shell. 
[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## Automatic credential configuration

When signed into the Cloud Shell, Ansible authenticates with Azure to manage infrastructure without any additional configuration. If you have more than one subscription, you can choose which subscription Ansible should work with by exporting the `AZURE_SUBSCRIPTION_ID` environment variable. To list all of your Azure subscriptions, run the following command:

```azurecli-interactive
az account list
```

Using the **id** of the subscription with which you want to work, set the **AZURE_SUBSCRIPTION_ID** as follows:

```azurecli-interactive
export AZURE_SUBSCRIPTION_ID=<your-subscription-id>
```

## Verify the configuration
To verify the successful configuration, use Ansible to create a resource group.

[!INCLUDE [create-resource-group-with-ansible.md](../../includes/ansible-create-resource-group.md)]

## Next steps

> [!div class="nextstepaction"] 
> [Quickstart: Configure virtual machine in Azure using Ansible](/azure/virtual-machines/linux/ansible-create-vm)