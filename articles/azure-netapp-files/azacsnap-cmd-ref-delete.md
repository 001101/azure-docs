---
title: Perform deletes using the Azure Application Consistent Snapshot Tool for Azure NetApp Files | Microsoft Docs
description: This article provides a guide for running the delete command of the Azure Application Consistent Snapshot Tool that you can use with Azure NetApp Files. 
services: azure-netapp-files
documentationcenter: ''
author: phjensen
manager: ''
editor: ''

ms.assetid:
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
---

# Perform deletes using the Azure Application Consistent Snapshot Tool

This article provides a guide for running the delete command of the Azure Application Consistent Snapshot Tool that you can use with Azure NetApp Files.

## Introduction

It is possible to delete volume snapshots and database catalog entries with the `azacsnap -c delete` command.

## Delete a snapshot `azacsnap -c delete`

This command deletes snapshots from the volumes.

Snapshots created less than 10 minutes prior to running this command should not be deleted due to the
potential for interference with snapshot replication.

This command takes the following arguments:

- `--delete=` type of delete operation ('sync', 'storage', 'hana').
  - `sync` type deletion tries to link the SAP HANA backup ID to the volume snapshot name in order to
       remove both the SAP HANA backup ID from the catalog and the snapshot from the storage volume.
  - `storage` type deletion removes the snapshot from each of the volumes in the configuration file.
       There is no interaction with SAP HANA.
  - `hana` type deletion deletes the SAP HANA backup ID entry from the backup catalog.
- `--snapshot=` (optional) The snapshot name to be permanently removed.
- `--hanasid=` (optional) SAP HANA database SID containing the backup ID to be deleted.
- `--hanabackupid=` (optional) SAP HANA backup ID to be deleted from SAP HANA backup catalog.
- `--force` (optional) **use with caution** this will force deletion without prompting for confirmation.

### Delete a snapshot using `sync` option`

```bash
azacsnap -c delete --delete sync --hanasid H80 --hanabackupid 157979797979
```

> **Note* Checks for any entries in the backup catalog for the SAP HANA backup ID 157979797979,
gets the storage snapshot name and deletes both the entry in the backup catalog
and the snapshot from all of the volumes containing the named snapshot.

```bash
azacsnap -c delete --delete sync --snapshot hana_hourly.2020-01-22_2358
```

> **Note* Checks for any entries in the backup catalog for the snapshot named hana_hourly.2020-01-22_2358,
gets the SAP HANA backup ID and deletes both the entry in the backup catalog
and the snapshot from any of the volumes containing the named snapshot.

### Delete a snapshot using `hana` option`

```bash
azacsnap -c delete --delete hana --hanasid H80 --hanabackupid 157979797979
```

> **Note* Deletes the SAP HANA backup ID 157979797979 from the backup catalog for SID H80.

### Delete a snapshot using `storage` option`

```bash
azacsnap -c delete --delete storage --snapshot hana_hourly.2020-01-22_2358
```

> **Note* Deletes the snapshot from any volumes containing snapshot named hana_hourly.2020-01-22_2358.

**Output using the `--delete storage` option**

Note the user is asked to confirm the deletion.

```bash
> azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T221702.2535255Z
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z'.
Are you sure you want to permanently delete the snapshot 'azacsnap-hsr-ha.2020-07-02T221702.2535255Z' from all storage volumes.? (y/n) [n]: y
Snapshot deletion completed
```

It is possible to avoid user confirmation, by using the optional `--force` parameter as follows:

```bash
> azacsnap -c delete --delete storage --snapshot azacsnap-hsr-ha.2020-07-02T222201.4988618Z --force
Processing delete request for snapshot 'azacsnap-hsr-ha.2020-07-02T222201.4988618Z'.
Snapshot deletion completed
```

## Next steps

- [Introduction to Azure Application Consistent Snapshot Tool](azacsnap-introduction.md)
- [Get started with Azure Application Consistent Snapshot Tool](azacsnap-get-started.md)
- [Install Azure Application Consistent Snapshot Tool](azacsnap-installation.md)
- [Configure Azure Application Consistent Snapshot Tool](azacsnap-configuration.md)
- [Test Azure Application Consistent Snapshot Tool](azacsnap-test.md)
- [Back up with Azure Application Consistent Snapshot Tool](azacsnap-backup.md)
