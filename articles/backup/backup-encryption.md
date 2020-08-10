---
title: Encryption in Azure Backup
description: Learn how encryption features in Azure Backup help you protect your backup data and meet the security needs of your business.
ms.topic: conceptual
ms.date: 08/04/2020
ms.custom: references_regions 
---

# Encryption in Azure Backup

All your backed-up data is automatically encrypted when stored in the cloud using Azure Storage encryption, which helps you meet your security and compliance commitments. This data at rest is encrypted using 256-bit AES encryption, one of the strongest block ciphers available, and is FIPS 140-2 compliant.

In addition to encryption at rest, all your backup data in transit is transferred over HTTPS. It always remains on the Azure backbone network.

For more information, see [Azure Storage encryption for data at rest](../storage/common/storage-service-encryption.md). Refer to the [Azure Backup FAQ](./backup-azure-backup-faq.md#encryption) to answer any questions that you may have about encryption.

Azure Backup includes encryption on two levels: the encryption of data in the Recovery Services vault, and encryption of your VMs and disks. VMs can be encrypted using either your own keys or platform-managed keys (PMK). The types of encryption are discussed in the following sections.

## Backup of encrypted VMs and disks

Azure Backup supports backup of VMs with disks encrypted using platform-managed keys, as well as keys owned and managed by you.

### Backup of Azure VMs with disks encrypted using platform-managed keys

By default, all disks in your VMs are automatically encrypted-at-rest using platform-managed keys that use [storage service encryption](../storage/common/storage-service-encryption.md). You can back up these VMs using Azure Backup without any specific actions required to support encryption on your end. For more information about encryption with platform-managed keys, see [this article](https://docs.microsoft.com/azure/virtual-machines/windows/disk-encryption#platform-managed-keys).

![Encrypted disks](./media/backup-encryption/encrypted-disks.png)

### Backup of Azure VMs encrypted using Azure Disk Encryption (ADE)

With Azure Backup, you can also back up your Azure Virtual machines that have their OS or data disks encrypted using Azure Disk Encryption. ADE uses BitLocker for Windows VMs, and DM-Crypt for Linux VMs, to perform in-guest encryption. For details, see [Back up and restore encrypted virtual machines with Azure Backup](./backup-azure-vms-encryption.md).

### Backup of Azure VMs with managed disks encrypted using customer-managed keys

When you encrypt disks with custom-managed keys (CMK), the key used for encrypting the disks is stored in the Azure Key Vault and is managed by you. Storage Service Encryption (SSE) using CMK differs from ADE encryption. ADE uses the encryption tools of the operating system. SSE encrypts data in the storage service, enabling you to use any OS or images for your VMs. For more information about encryption of managed disks with customer-managed keys, see [this article](../virtual-machines/windows/disk-encryption.md#customer-managed-keys).

## Encryption of backup data in the Recovery Services vault

This section discusses the options available for encrypting your backup data stored in the Recovery Services vault.

### Encryption of backup data using platform-managed keys

By default, all your data is encrypted using platform-managed keys. You don't need to take any explicit action from your end to enable this encryption. It applies to all workloads being backed up to your Recovery Services vault.

### Encryption of backup data using customer-managed keys

When backing up your Azure Virtual Machines, you can choose to encrypt your data using encryption keys owned and managed by you. Azure Backup lets you use your RSA keys stored in the Azure Key Vault for encrypting your backups. The encryption key used for encrypting backups may be different from the one used for the source. The data is protected using an AES 256 based data encryption key (DEK), which is, in turn, protected using your keys. This gives you full control over the data and the keys. To allow encryption, it's required that the Recovery Services vault be granted access to the encryption key in the Azure Key Vault. You can disable the key or revoke access whenever needed. However, you must enable encryption using your keys before you attempt to protect any items to the vault.

Read more about how to encrypt your backup data [using customer-managed keys](encryption-at-rest-with-cmk.md).

### Infrastructure-level encryption for backup data

In addition to encrypting your data in the Recovery Services vault using customer-managed keys, you can also choose to have an additional layer of encryption configured on the storage infrastructure. This infrastructure encryption is managed by the platform and together with encryption at rest using customer-managed keys, it allows two-layer encryption of your backup data. It should be noted that infrastructure encryption can be configured only if you first choose to use your own keys for encryption at rest. Infrastructure encryption uses platform-managed keys for encrypting data.

>[!NOTE]
>Infrastructure encryption is currently in limited preview and is available in US East, US West2, US South Central, US Gov Arizona, and US GOV Virginia regions only. If you wish to use the feature in any of these regions, please fill out [this form](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR0H3_nezt2RNkpBCUTbWEapUN0VHNEpJS0ZUWklUNVdJSTEzR0hIOVRMVC4u) and email us at [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com).

## Next steps

- [Back up and restore an encrypted Azure VM](backup-azure-vms-encryption.md)
