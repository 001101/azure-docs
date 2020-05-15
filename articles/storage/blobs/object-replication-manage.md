---
title: Configure object replication (preview)
titleSuffix: Azure Storage
description: 
services: storage
author: tamram

ms.service: storage
ms.topic: how-to
ms.date: 05/12/2020
ms.author: tamram
ms.subservice: blobs
---

# Configure object replication (preview)

With object replication (preview), you can automatically replicate block blobs from one storage account to another in any Azure region.

Object replication unblocks a new set of common replication scenarios:

- Minimize latency – have your users consume the data locally rather than issuing cross-region read requests.
- Increase efficiency – have your compute clusters process/transform the same set of objects locally in different regions.
- Optimize data distribution – have your data consolidated in a single location for processing/analytics and then distribute only resulting dashboards to your offices worldwide.
- Optimize cost – tier down your data to Archive upon replication completion using Lifecycle Management policies to optimize the cost.

## Getting started

### Configuration

You can enable object replication using various methods including Azure Portal, Azure CLI, Azure PowerShell, ARM and Azure Storage Management SDKs.

# [Azure portal](#tab/portal)

In the Portal, choose the source account and go to Object replication’ tab.

![](media/a52b28461668a4e7e0c38daff6a7f8ab.png)

Click ‘Create replication rules’, choose ‘Target destination account subscription’, ‘Target destination storage account’:

![](media/1ef19f29cca4f683cc45ff126e0d8b90.png)

Then specify ‘Source container’, ‘Destination container’, any ‘Filters’ you’d like to use, and the ‘Copy over’ parameter:

![](media/593555b64f3424cf21d79360b3ae1db2.png)

‘Copy over’ parameter allows you to specify the replication behavior for the data which existed prior to object replication being enabled. ‘Copy over’ parameter has the following three values:

- Everything. All the data that existed prior to object replication being enabled will be replicated to the destination account/container.

- Only new objects. Only new blobs added after object replication had been enabled will be replicated to the destination account/container.

- Custom. Allows you to only replicate the blobs which were created after the specified time.

![](media/1495ca177f50696b024dfc21e9dd2a5a.png)

Once all required fields are filled in, click ‘Save and apply’:

![](media/5ffdde969dc1091e9010eaab42ade58b.png)

Using the ‘Object replication’ tab, you can further review the list of policies that are in effect both in which the account is used as a source and as a destination:

![](media/14fcf960963c53f4a01be40bc55a2cb5.png)

# [PowerShell](#tab/powershell)

Install and import the PowerShell preview module which supports object replication:

Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.13.4-preview –AllowPrerelease –AllowClobber –Force

Import-Module Az.Storage -RequiredVersion 1.13.4

To start working with Azure PowerShell, sign in with your Azure credentials.

Connect-AzAccount

Enable both Change feed and Versioning on the source storage account. Make sure
you’re already registered for [Change feed](https://azure.microsoft.com/blog/change-feed-support-now-available-in-preview-for-azure-blob-storage/) and Versioning capabilities.

Update-AzStorageBlobServiceProperty -ResourceGroupName \<resource group\> -StorageAccountName \<source storage account\> -EnableChangeFeed \$true -IsVersioningEnabled \$true

Enable Versioning on the destination storage account.

Update-AzStorageBlobServiceProperty -ResourceGroupName \<resource group\> -StorageAccountName \<destination storage account\> -IsVersioningEnabled \$true

Create source and destination containers you’re planning to replicate the data between.

Get-AzStorageAccount -ResourceGroupName \<resource group\> -StorageAccountName \<source account\> \| New-AzStorageContainer \<source container\>

Get-AzStorageAccount -ResourceGroupName \<resource group\> -StorageAccountName \<destination account\> \| New-AzStorageContainer \<destination container\>

Create a new replication rule and specify the desired prefix and replication behavior for the objects, that existed prior to configuring object replication.

\$rule = New-AzStorageObjectReplicationPolicyRule -SourceContainer \<source container\> -DestinationContainer \<destination container\> -PrefixMatch \<desired prefix match\> -MinCreationTime (Get-Date).AddDays(-3)

For list of available parameters and their values, run Get-Help New-AzStorageObjectReplicationPolicyRule -Full and/or Get-Help New-AzStorageObjectReplicationPolicyRule -Examples.

Create a replication policy on the destination account and pass on the rule you created earlier.

Set-AzStorageObjectReplicationPolicy -ResourceGroupName \<resource group\> -StorageAccountName \<destination storage account\> -PolicyId default -SourceAccount \<source storage account\> -Rule \$rule

Retrieve the replication policy you created and enable object replication on the source account by adding the replication policy to it.

\$dstpolicy = Get-AzStorageObjectReplicationPolicy -ResourceGroupName \<resource group\> -StorageAccountName \<destination storage account\>

Set-AzStorageObjectReplicationPolicy -ResourceGroupName \<resource group\> -StorageAccountName \<source storage account\> -InputObject \$dstpolicy

Get details on the replication policy in effect on the destination account and its associated properties.

\$dstpolicy

\$dstpolicy.Rules

Get details on the replication policy in effect on the source account and its associated properties.

\$srcpolicy = Get-AzStorageObjectReplicationPolicy -ResourceGroupName \<resource group\> -StorageAccountName \<source storage account\>

\$srcpolicy

\$srcpolicy.Rules

When no longer needed, remove the replication policy from the source and destination accounts.

Get-AzStorageObjectReplicationPolicy -ResourceGroupName \<resource group\> -StorageAccountName \<destination storage account\> \| Remove-AzStorageObjectReplicationPolicy

Get-AzStorageObjectReplicationPolicy -ResourceGroupName \<resource group\> -StorageAccountName \<source storage account\> \| Remove-AzStorageObjectReplicationPolicy

# [Azure CLI](#tab/cli)

Refer to [Install the Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) article to install and import the preview module supporting object replication capability.

az extension add -n storage-ors-preview

To start working with Azure CLI, sign in with your Azure credentials.

az login

Enable both Change feed and Versioning on the source storage account. Make sure you’re already registered for [Change feed](https://azure.microsoft.com/blog/change-feed-support-now-available-in-preview-for-azure-blob-storage/) and Versioning capabilities.

az storage blob service-properties update --enable-versioning --resource-group \<resource group\> --account-name \<source storage account\>

az storage blob service-properties update --enable-change-feed --resource-group \<resource group\> --account-name \<source storage account\>

Enable Versioning on the destination storage account.

az storage blob service-properties update --enable-versioning --resource-group \<resource group\> --account-name \<destination storage account\>

Create a new replication policy and an associated rule(s) and specify the desired prefix and replication behavior for the objects, that existed prior to configuring object replication.

az storage account ors-policy create \\

\--account-name \<destination storage account\> \\

\--resource-group \<resource group\> \\

\--source-account \<source storage account\> \\

\--destination-account \<destination storage account\> \\

\--source-container \<source container\> \\

\--min-creation-time '2020-02-19T16:05:00Z' \\

\--prefix-match prod\_

For list of available parameters and their values refer to Azure CLI object replication extension documentation.

Enable object replication on the source account by adding the replication policy to it.

az storage account ors-policy show -g \<resource group\> -n \<destination storage account\> --policy-id \<policy id from the previous step\> \| az storage account ors-policy create -g \<resource group\> -n \<source storage account\> -p "\@-"

Save the replication policy ID and the rule ID into variables for further orchestration.

\$policyid = (az storage account ors-policy create --account-name \<source/destination storage account\> --resource-group \<resource group\> --properties \@{path}) --query policyId)

\$ruleid = (az storage account ors-policy create --account-name \<source/destination storage account\> --resource-group \<resource group\> --properties \@{path}) --query rules.ruleId)

List replication policies in effect on a given storage account.

az storage account ors-policy list \\

\--account-name \<source/destination storage account\> \\

\--resource-group \<resource group\>

Retrieve details of a specific replication policy.

az storage account ors-policy show \\

\--policy-id \$policyid \\

\--account-name \<source/destination storage account\> \\

\--resource-group \<resource group\>

Change the source account name by updating the existing replication policy.

az storage account ors-policy update \\

\--policy-id \$policyid \\

\--account-name \<destination storage account\> \\

\--resource-group \<resource group\> \\

\-s \<new source storage account\>

Add rule to an existing replication policy.

az storage account ors-policy rule add \\

\--policy-id \$policyid \\

\--account-name \<destination storage account\> \\

\--resource-group \<resource group\> \\

\--destination-container \<destination container\> \\

\--source-container \<source container\> \\

\--prefix-match test\_ \\

\--min-creation-time '2020-02-19T16:05:00Z'

List all rules in effect for a given replication policy.

az storage account ors-policy rule list \\

\--policy-id \$policyid \\

\--account-name \<source/destination storage account\> \\

\--resource-group \<resource group\>

When no longer needed, remove the replication policy from the source and
destination accounts.

az storage account ors-policy remove \\

\--policy-id \$policyid \\

\--account-name \<source/destination storage account\> \\

\--resource-group \<resource group\>

---

## Python SDK

<https://github.com/zfchen95/azure-rest-api-specs-pr/blob/ors1018/specification/storage/resource-manager/Microsoft.Storage/stable/2019-06-01/examples/StorageAccountUpdateObjectReplicationPolicyOnDestination.json>

<https://github.com/zfchen95/azure-rest-api-specs-pr/blob/ors1018/specification/storage/resource-manager/Microsoft.Storage/stable/2019-06-01/examples/StorageAccountUpdateObjectReplicationPolicyOnSource.json>

## Next steps

- [Object replication (preview)](object-replication-overview.md)