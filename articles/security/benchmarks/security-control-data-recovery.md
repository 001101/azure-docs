---
title: Security Control - Data Recovery
description: Security Control Data Recovery
author: msmbaldwin
manager: rkarlin

ms.service: security
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: mbaldwin
ms.custom: security-recommendations

---

# Security Control: Data Recovery

## 9.1: Ensure Regular Automated Back Ups

| Azure ID | CIS Control IDs | Responsibility |
|--|--|--|
| 9.1 | 10.1 | Customer |

Enable Azure Backup and configure the backup source (Azure VMs, SQL Server, or File Shares), as well as the desired frequency and retention period.<br><br>How to enable Azure Backup:<br>https://docs.microsoft.com/en-us/azure/backup/

## 9.2: Perform Complete System Backups and Backup any Customer Managed Keys

| Azure ID | CIS Control IDs | Responsibility |
|--|--|--|
| 9.2 | 10.2 | Customer |

Enable Azure Backup and target VM(s), as well as the desired frequency and retention periods. Backup customer managed keys within Azure Key Vault.<br><br>How to enable Azure Backup:<br>https://docs.microsoft.com/en-us/azure/backup/<br><br>How to backup key vault keys in Azure:<br>https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?view=azurermps-6.13.0

## 9.3: Validate all Backups including Customer Managed Keys

| Azure ID | CIS Control IDs | Responsibility |
|--|--|--|
| 9.3 | 10.3 | Customer |

Customer to ensure ability to periodically perform data restoration of content within Azure Backup. If necessary, test restore content to an isolated Vlan. Customer to test restoration of backed up customer managed keys.<br><br><br><br>How to recover files from Azure Virtual Machine backup:<br><br>https://docs.microsoft.com/en-us/azure/backup/backup-azure-restore-files-from-vm<br><br><br><br>How to restore key vault keys in Azure:<br><br>https://docs.microsoft.com/en-us/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

## 9.4: Ensure Protection of Backups and Customer Managed Keys

| Azure ID | CIS Control IDs | Responsibility |
|--|--|--|
| 9.4 | 10.4 | Customer |

For on-premises backup, encryption-at-rest is provided using the passphrase you provide when backing up to Azure. For Azure VMs, data is encrypted-at-rest using Storage Service Encryption (SSE). Customer to enable Soft-Delete in Key Vault to protect keys against accidental or malicious deletion.<br><br>How to enable Soft-Delete in Key Vault:<br>https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal

## 9.5: Ensure Backups Have At least One Non-Continuously Addressable Destination

| Azure ID | CIS Control IDs | Responsibility |
|--|--|--|
| 9.5 | 10.5 | Customer |

Azure Backup data is stored in a Recovery Service vault, separate from the storage location (VM, SQL Server, File Share). Contents of the vault are not directly accessible via the source/OS.

