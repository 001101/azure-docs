---
title: Overview of security features
description: Learn about security capabilities in Azure Backup that help you protect your backup data and meet the security needs of your business.
ms.topic: conceptual
ms.date: 03/12/2020
---

# Overview of security features in Azure Backup

This article introduces security capabilities in Azure Backup that help you protect your backup data and meet the security needs of your business.

## Separation between guest and Azure storage

With Azure Backup,  which includes full IaaS VM backup and SQL/HANA in VM backup, the backup data is stored in Azure storage and the guest has no direct access to backup storage or its contents.  In the case of IaaS VM backup, the backup snapshot creation and storage is done by Azure fabric where the guest has no involvement other than quiescing the workload for application consistent backups.  In the case of SQL and HANA, the backup extension gets temporary access to write to specific blobs.  In this way existing backups can't be tampered with or deleted by the guest, should it be compromised.

## Manage and control identity and user access

Azure Backup enables you to manage fine-grained access using [Azure Role-Based Access Control (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles). RBAC allows you to segregate duties within your team and grant only the amount of access to users necessary to do their jobs.

* Azure Backup provides three built-in roles to control backup management operations:
  * [Backup Contributor](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#backup-contributor) - to create and manage backup, except deleting Recovery Services vault and giving access to others
  * [Backup Operator](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#backup-operator) - everything a contributor does except removing backup and managing backup policies
  * [Backup Reader](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#backup-reader) - permissions to view all backup management operations

    [Learn more about Role-Based Access control to manage Azure Backup](https://docs.microsoft.com/azure/backup/backup-rbac-rs-vault).

* Azure Backup has several security controls built into the service to prevent, detect, and respond to security vulnerabilities. [Learn more about security controls for Azure Backup](https://docs.microsoft.com/azure/backup/backup-security-controls).

## Encryption

Encryption protects your data and helps you to meet your organizational security and compliance commitments. Within Azure, data in transit between Azure storage and the vault is protected by HTTPS. This data remains on the Azure backbone network.

* Backup data is automatically encrypted using [Microsoft managed keys](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-microsoft-managed-keys). You also can encrypt your backup data in the Recovery Services Vault using [your encryption keys](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#encryption-of-backup-data-using-customer-managed-keys) stored in the Azure Key Vault.

* Azure Backup supports backup and restore of Azure VMs that have their OS/data disks encrypted with Azure Disk Encryption (ADE). [Learn more about encrypted Azure VMs and Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption).

## Protection of backup data from accidental or malicious deletes

Azure Backup provides security features to help protect backup data even after deletion. One such feature is [soft delete](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete). With soft delete, if a malicious actor deletes the backup of a VM (or backup data is accidentally deleted), the backup data is retained for 14 additional days, allowing the recovery of that backup item with no data loss. The additional 14 days retention of backup data in the "soft delete" state doesn't incur any cost to the customer. [Learn more about soft delete](https://docs.microsoft.com/azure/backup/backup-azure-security-feature-cloud#soft-delete).

## Monitoring and alerts

Azure Backup provides [built-in monitoring and alerting capabilities](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor) to view and configure actions for business-critical events. [Backup Reports](https://docs.microsoft.com/azure/backup/configure-reports) serve as a one-stop destination for tracking usage, auditing of backups and restores, and identifying key trends at different levels of granularity.

## Security of hybrid backup data

Azure Backup service uses the Microsoft Azure Recovery Services (MARS) agent to back up and restore files, folders, and the volume or system state from an on-premises computer to Azure. MARS now provides security features to help protect hybrid backups. These features include:

* **Prevention**: An additional layer of authentication is added whenever a critical operation like changing a passphrase is performed. This validation is to ensure that such operations can be performed only by users who have valid Azure credentials. [Learn more about the features that prevent attacks](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#prevent-attacks).

* **Alerting**: An email notification is sent to the subscription admin whenever a critical operation like deleting backup data is performed. This email ensures that the user is notified quickly about such actions. [Learn more about notifications for critical operations](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#notifications-for-critical-operations).

* **Recovery**: Deleted backup data is retained for an additional 14 days from the date of deletion. This ensures recoverability of the data within a given time period, so there's no data loss even if an attack happens. Also, a greater number of minimum recovery points are maintained to guard against corrupt data. [Learn more about recovering deleted backup data](https://docs.microsoft.com/azure/backup/backup-azure-security-feature#recover-deleted-backup-data).

## Compliance

To help organizations comply with national, regional, and industry-specific requirements governing the collection and use of individuals' data, Microsoft Azure & Azure Backup offer a comprehensive set of certifications and attestations. [See the list of compliance certifications](compliance-offerings.md)

## Next steps

* [Security features to help protect cloud workloads that use Azure Backup](backup-azure-security-feature-cloud.md)
* [Security features to help protect hybrid backups that use Azure Backup](backup-azure-security-feature.md)
