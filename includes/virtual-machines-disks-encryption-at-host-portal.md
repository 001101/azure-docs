---
 title: include file
 description: include file
 services: virtual-machines
 author: roygara
 ms.service: virtual-machines
 ms.topic: include
 ms.date: 07/07/2020
 ms.author: rogarana
 ms.custom: include file
---

## Restrictions

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](virtual-machines-disks-encryption-at-host-restrictions.md)]

### Supported regions

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](virtual-machines-disks-encryption-at-host-regions.md)]

### Supported VM sizes

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## Prerequisites

In order to be able to use encryption at host for your VMs or virtual machine scale sets, you must get the feature enabled on your subscription. Send an email to encryptionAtHost@microsoft.com with your subscription Ids to get the feature enabled for your subscriptions.

Sign in to the Azure portal using the [provided link](https://aka.ms/diskencryptionupdates).

> [!IMPORTANT]
> You must access the Azure portal using the [provided link](https://aka.ms/diskencryptionupdates), encryption at host is not currently available in the public Azure portal.

### Create an Azure Key Vault and DiskEncryptionSet

Once the feature is enabled, you'll need to set up an Azure Key Vault and a DiskEncryptionSet, if you haven't already.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](virtual-machines-disks-encryption-create-key-vault-portal.md)]

## Deploy a VM

Fill in values

:::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="example text":::

Select encryption at host

:::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="example text":::

## Next steps