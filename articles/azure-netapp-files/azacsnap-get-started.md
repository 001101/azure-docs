---
title: Get started with Azure Application Consistent Snapshot Tool for Azure NetApp Files | Microsoft Docs
description: Provides a guide for installing the Azure Application Consistent Snapshot Tool that you can use with Azure NetApp Files. 
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''

ms.assetid:
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
---

# Get started with Azure Application Consistent Snapshot Tool

This article provides a guide for installing the Azure Application Consistent Snapshot Tool that you can use with Azure NetApp Files. 

## Getting the snapshot tools

It is recommended customers get the most recent version of the self-installation file (e.g.
azacsnap_installer_v5.0.run or later) which contains the snapshot tools from Microsoft. Then 
follow the steps in the Technical Setup section of this guide to install.

The self-installation file is signed with Microsoft's public key to allow for GPG verification of the
download.

### Verifying the download

The installer, which is downloadable per above, has an associated PGP signature file with an `.asc`
filename extension. This file can be used to verify the downloaded installer to ensure this file is a
Microsoft provided file. The Microsoft PGP Public Key used for signing Linux packages is available here
(<http://packages.microsoft.com/keys/microsoft.asc>) and has been used to sign the signature file.

The Microsoft PGP Public Key can be imported to a user's local as follows:

```bash
> wget http://packages.microsoft.com/keys/microsoft.asc
> gpg --import microsoft.asc
```

The following commands trust the Microsoft PGP Public Key:

1. List the keys in the store.
2. Edit the Microsoft key.
3. Check the fingerprint with `fpr`
4. Sign the key to trust it.

```bash
> gpg --list-keys
----<snip>----
pub rsa2048 2015- 10 - 28 [SC]
BC528686B50D79E339D3721CEB3E94ADBE1229CF
uid [ unknown] Microsoft (Release signing) gpgsecurity@microsoft.com

> gpg --edit-key gpgsecurity@microsoft.com
gpg (GnuPG) 2.1.18; Copyright (C) 2017 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
[ unknown] (1). Microsoft (Release signing) <gpgsecurity@microsoft.com>

gpg> fpr
pub rsa2048/EB3E94ADBE1229CF 2015- 10 - 28 Microsoft (Release signing)
<gpgsecurity@microsoft.com>
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF

gpg> sign
pub rsa2048/EB3E94ADBE1229CF
created: 2015- 10 - 28 expires: never usage: SC
trust: unknown validity: unknown
Primary key fingerprint: BC52 8686 B50D 79E3 39D3 721C EB3E 94AD BE12 29CF
Microsoft (Release signing) <gpgsecurity@microsoft.com>
Are you sure that you want to sign this key with your
key "XXX XXXX <xxxxxxx@xxxxxxxx.xxx>" (A1A1A1A1A1A1)
Really sign? (y/N) y

gpg> quit
Save changes? (y/N) y
```

The PGP signature file for the installer can be checked as follows:

```bash
> gpg --verify azacsnap_installer_v5.0.run.asc azazsnap_installer_v5.0.run
gpg: Signature made Sat 13 Apr 2019 07:51:46 AM STD
gpg: using RSA key EB3E94ADBE1229CF
gpg: Good signature from "Microsoft (Release signing)
<gpgsecurity@microsoft.com>" [full]
```

For more details about using GPG, see [The GNU Privacy Handbook](https://www.gnupg.org/gph/en/manual/book1.html).

## Supported Scenarios

The snapshot tools can be used in the following scenarios.

- Single SID
- Multiple SID
- HSR
- Scale-out
- MDC (Only single tenant supported)
- Single Container
- SUSE Operating System
- RHEL Operating System
- SKU TYPE I
- SKU TYPE II

See [Supported scenarios for HANA Large Instances](/azure/virtual-machines/workloads/sap/hana-supported-scenario)

## Snapshot Support Matrix from SAP

The following matrix is provided as a guideline on which versions of SAP HANA
are supported by SAP for Storage Snapshot Backups.

|                         |1.0 SPS12|2.0 SPS0|2.0 SPS1|2.0 SPS2|2.0 SPS3|2.0 SPS4|
|-------------------------|---------|--------|--------|--------|--------|--------|
|Single Container Database| √       | √      | -      | -      | -      | -      |
|MDC Single Tenant        | -       | -      | √      | √      | √      | √      |
|MDC Multiple Tenants     | -       | -      | -      | -      | -      | √      |
> √ = <small>supported by SAP for Storage Snapshots</small>

## Important things to remember

- After the setup of the snapshot tools, continuously monitor the storage space available and if
    necessary, delete the old snapshots on a regular basis to avoid storage fill out.
- Always use the latest snapshot tools.
- Use the same version of the snapshot tools across the landscape.
- Test the snapshot tools and get comfortable with the parameters required and output of the
    command before using in the production system.
- When setting up the HANA user for backup (details below in this document), you need to
    set up the user for each HANA instance. Create an SAP HANA user account to access HANA
    instance under the SYSTEMDB (and not in the SID database) for MDC. In the single container
    environment, it can be set up under the tenant database.
- Customers must provide the SSH public key for storage access. This action must be done once per
    node and for each user under which the command is executed.
- The number of snapshots per volume is limited to 250.
- If manually editing the configuration file, always use a Linux text editor such as "vi" and not
    Windows editors like Notepad. Using Windows editor may corrupt the file format.
  - Set up `hdbuserstore` for the SAP HANA user to communicate with SAP HANA.
- For DR: The snapshot tools must be tested on DR node before DR is set up.
- Monitor disk space regularly, automated log deletion is managed with the `--trim` option of the
    `azacsnap -c backup` for SAP HANA 2 and later releases.
- **Risk of snapshots not being taken** - The snapshot tools only interact with the node of the SAP HANA
system specified in the configuration file.  If this node becomes unavailable, there is no mechanism to
automatically start communicating with another node.  
  - For an **SAP HANA Scale-Out with Standby** scenario it is typical to install and configure the snapshot
 tools on the master node. But, if the master node becomes unavailable, the standby node will take over
the master node role. In this case, the implementation team should configure the snapshot tools on both
nodes (Master and Stand-By) to avoid any missed snapshots. In the normal state, the master node will take
HANA snapshots initiated by crontab, but after master node failover those snapshots will have to be
executed from another node such as the new master node (former standby). To achieve this outcome, the standby
node would need the snapshot tool installed, storage communication enabled, hdbuserstore configured,
`azacsnap.json` configured, and crontab commands staged in advance of the failover.
  - For an **SAP HANA HSR HA** scenario, it is recommended to install, configure, and schedule the
snapshot tools on both (Primary and Secondary) nodes. Then, if the Primary node becomes unavailable,
the Secondary node will take over with snapshots being taken on the Secondary. In the normal state, the
Primary node will take HANA snapshots initiated by crontab and the Secondary node would attempt to take
snapshots but fail as the Primary is functioning correctly.  But after Primary node failover, those
snapshots will be executed from the Secondary node. To achieve this outcome, the Secondary node needs the
snapshot tool installed, storage communication enabled, `hdbuserstore` configured, azacsnap.json
configured, and crontab enabled in advance of the failover.

## Guidance provided in this document

The following guidance is provided to illustrate the usage of the snapshot tools.

### Taking Snapshot Backups

- [What are the prerequisites for the storage snapshot](#pre-requisites-for-installation)
  - [Enable communication with storage](#enable-communication-with-storage)
  - [Enable communication with SAP HANA](#enable-communication-with-sap-hana)
- [How to take snapshots manually](#1-How-to-take-snapshots-manually)
- [How to set up automatic snapshot backup](#2-How-to-setup-automatic-snapshot-backup)
- [How to monitor the snapshots](#3-how-to-monitor-the-snapshots)
- [How to delete a snapshot?](#4-how-to-delete-a-snapshot)
- [How to restore a `hana` snapshot](#5-how-to-restore-a-hana-snapshot)
- [How to restore a `boot` snapshot](#6-how-to-restore-a-boot-snapshot)
- [What are key facts to know about the snapshots](#7-what-are-key-facts-to-know-about-the-snapshots)

> Snapshots are tested for both single SID and multi SID.

### Performing Disaster Recovery

- [What are the prerequisites for DR setup](#1-what-are-the-prerequisites-for-dr-setup)
- [How to set up a disaster recovery](#2-how-to-setup-a-disaster-recovery)
- [How to monitor the data replication from Primary to DR site](#3-how-to-monitor-the-data-replication-from-primary-to-dr-site)
- [How to perform a failover to DR site?](#4-how-to-perform-a-failover-to-dr-site)

## Next steps

- [Introduction to Azure Application Consistent Snapshot Tool](azacsnap-introduction.md)
- [Get started with Azure Application Consistent Snapshot Tool](azacsnap-get-started.md)
- [Install Azure Application Consistent Snapshot Tool](azacsnap-installation.md)
- [Configure Azure Application Consistent Snapshot Tool](azacsnap-cmd-ref-configure.md)
- [Test Azure Application Consistent Snapshot Tool](azacsnap-cmd-ref-test.md)
- [Back up with Azure Application Consistent Snapshot Tool](azacsnap-cmd-ref-backup.md)
