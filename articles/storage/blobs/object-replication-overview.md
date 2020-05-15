---
title: Object replication (preview)
titleSuffix: Azure Storage
description: Configure object replication (preview) to asynchronously replicate block blobs between two storage accounts.
services: storage
author: tamram

ms.service: storage
ms.topic: conceptual
ms.date: 05/12/2020
ms.author: tamram
ms.subservice: blobs
---

# Object replication (preview)

With object replication (preview), you can asynchronously replicate block blobs between two storage accounts.

- Object replication can reduce latency for read requests by enabling clients to consume data from a region that is in closer physical proximity.
- 


Object replication unblocks a new set of common replication scenarios:

- Minimize latency – have your users consume the data locally rather than issuing cross-region read requests.
- Increase efficiency – have your compute clusters process/transform the same set of objects locally in different regions.
- Optimize data distribution – have your data consolidated in a single location for processing/analytics and then distribute only resulting dashboards to your offices worldwide.
- Optimize cost – tier down your data to Archive upon replication completion using Lifecycle Management policies to optimize the cost.

## How object replication works

Object replication asynchronously copies a specified set of block blobs from a container in one storage account to a container in another account. Object replication happens asynchronously and is [eventually consistent](https://en.wikipedia.org/wiki/Eventual_consistency). After you configure object replication on the source account, Azure Storage checks the change feed every minute(???) and replicates any write or delete operations to the destination account. Replication latency depends on the size of the block blob being replicated.

???You can configure the destination container’s Access Policy, delete and tier down the objects to Archive in the destination container when necessary.???

### Configure a replication policy

To use object replication, create a replication policy on the source account that specifies the destination account. Then add one or more replication rules to the policy. Replication rules specify the source and destination containers and determine which block blobs in those containers will be copied.

A storage account can serve as the source account for up to two destination accounts. For example, suppose you wish to replicate data from a source account to two destination accounts that are in different regions, to reduce latency. You can configure two separate replication policies to replicate data to each of the destination accounts.

### Create rules on the replication policy

You can specify up to 10 replication rules for each replication policy. Each rule defines a single source and destination container, and each source and destination container can be used in only one rule.

The source and destination containers must both exist before you can create the replication policy. After you create the policy, the destination container is read-only. Any attempts to write to the destination container fail with error code 403 (Forbidden).

When you create a replication rule, by default only new block blobs that are subsequently added to the source container are copied. You can also specify that both new and existing block blobs are copied, or you can define a custom copy scope that copies block blobs created from a specified time onward.

A replication rule can also filter block blobs by prefix. If you specify a prefix, only blobs matching that prefix in the source container will be copied to the destination container.

## About the preview

Object replication is supported for general-purpose v2 storage accounts only. Object replication is available in the following regions in preview:

- US East 2
- US Central
- Europe West

Both the source and destination accounts must reside in one of these regions in order to use object replication. The accounts can be in two different regions.

During the preview, there are no additional costs associated with replicating data between storage accounts.

> [!IMPORTANT]
> The object replication preview is intended for non-production use only. Production service-level agreements (SLAs) are not currently available.

### Prerequisites for object replication

Object replication requires that the following Azure Storage features are enabled:

- [Change feed (preview)](storage-blob-change-feed.md)
- [Blob versioning (preview)](versioning-overview.md)

Enable these features for the storage account before you enable object replication. Be sure to register for the change feed and blob versioning previews before you enable them.

Enabling these prerequisites may incur additional costs. For more details, refer to the [Azure Storage pricing page](https://azure.microsoft.com/pricing/details/storage/).

### Register for the preview

You can register for the object replication preview using PowerShell or Azure CLI. Make sure that you also register for the change feed and blob versioning previews if you haven't already.

# [PowerShell](#tab/powershell)

To register for the preview with PowerShell, run the following commands:

```powershell
# Register for the object replication preview
Register-AzProviderFeature -FeatureName AllowObjectReplication -ProviderNamespace Microsoft.Storage

# Register for change feed (preview)
Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage

# Register for blob versioning (preview)
Register-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning

# Refresh the Azure Storage provider namespace
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# [Azure CLI](#tab/azure-cli)

To register for the preview with Azure CLI, run the following commands:

```azurecli
az feature register --namespace Microsoft.Storage --name AllowObjectReplication
az feature register --namespace Microsoft.Storage --name Changefeed
az feature register --namespace Microsoft.Storage --name Versioning
az provider register --namespace 'Microsoft.Storage'
```

---

### Check registration status

You can check the status of your registration requests using PowerShell or Azure CLI.

# [PowerShell](#tab/powershell)

To check the status of your registration requests using PowerShell, run the following commands:

```powershell
Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowObjectReplication

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Changefeed

Get-AzProviderFeature -ProviderNamespace Microsoft.Storage `
    -FeatureName Versioning
```

# [Azure CLI](#tab/azure-cli)

To check the status of your registration requests using Azure CLI, run the following commands:

```azurecli
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/AllowObjectReplication')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Changefeed')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.Storage/Versioning')].{Name:name,State:properties.state}"
```

---

## Next steps

- [Configure object replication (preview)](object-replication-manage.md)