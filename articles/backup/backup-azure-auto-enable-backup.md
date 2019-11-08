---
title: Auto-Enable Backup on VM Creation using Azure Policy
description: 'An article describing how to use Azure Policy to auto-enable backup for all VMs created in a given scope'
ms.reviewer: dcurwin
author: adbalaji
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: adbalaji
---

# Auto-Enable Backup on VM Creation using Azure Policy

One of the key responsibilities of a Backup or Compliance Admin in an organization is to ensure that all business-critical machines are backed up with the appropriate retention.

Today, Azure Backup provides a built-in policy (using Azure Policy) that can be assigned to **all Azure VMs in a specified location within a subscription or resource group**. When this policy is assigned to a given scope, all new VMs created in that scope are automatically configured for backup to an **existing vault in the same location and subscription**. The user can specify the vault and the retention policy to which the backed up VMs should be associated.

## Supported Scenarios 

* The built-in policy is currently supported only for Azure VMs (see all supported VM images in the below section). Users must take care to ensure that the retention policy specified during assignment is a VM retention policy. Refer to [this](https://aka.ms/AA6il6q) document to see all the VM SKUs supported by this policy.

* The policy can be assigned to a single location and subscription at a time. To enable backup for VMs across locations and subscriptions, multiple instances of the policy assignment need to be created, one for each combination of location and subscription.

* The specified vault and the VMs configured for backup can be under different resource groups.

* Management Group scope is currently unsupported.

## Using the built-in policy

To assign the policy to the required scope, please follow the below steps:

1. Login to the Azure Portal and navigate to the **Policy** Dashboard.
2. Select **Definitions** in the left menu to get a list of all built-in policies across Azure Resources.
3. Filter the list for **Category=Backup**. You will see the list filtered down to a single policy named ‘Deploy prerequisites to backup VMs of a location to an existing central Vault in the same location’.
![Policy Dashboard](./media/backup-azure-auto-enable-backup/policy_dashboard.png)
4. Click on the name of the policy. You will be redirected to the detailed definition for this policy.
![Policy Definition Blade](./media/backup-azure-auto-enable-backup/policy_definition_blade.png)
5. Click on the **Assign** button at the top of the blade. This redirects you to the **Assign Policy** blade.
6. Under basics, click on the three dots next to the **Scope** field. This opens up a right context blade where you can select the subscription for the policy to be applied on. You can also optionally select a resource group, so that the policy is applied only for VMs in a particular resource group.
![Policy Assignment Basics](./media/backup-azure-auto-enable-backup/policy_assignment_basics.png)
7. In the **Parameters** tab, choose a location from the drop-down, and select the vault and backup policy to which the VMs in the scope must be associated.
![Policy Assignment Parameters](./media/backup-azure-auto-enable-backup/policy_assignment_parameters.png)
8. Ensure that **Effect** is set to deployIfNotExists.
9. Navigate to **Review+create** and click **Create**.

## Next Steps

[Learn more about Azure Policy](https://docs.microsoft.com/en-us/azure/governance/policy/overview)