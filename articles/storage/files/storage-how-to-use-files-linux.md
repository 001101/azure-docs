---
title: Use Azure Files with Linux | Microsoft Docs
description: Learn how to mount an Azure file share over SMB on Linux.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: rogarana
ms.subservice: files
---

# Use Azure Files with Linux
[Azure Files](storage-files-introduction.md) is Microsoft's easy to use cloud file system. Azure file shares can be mounted in Linux distributions using the [SMB kernel client](https://wiki.samba.org/index.php/LinuxCIFS). This article shows two ways to mount an Azure file share: on-demand with the `mount` command and on-boot by creating an entry in `/etc/fstab`.

> [!NOTE]  
> In order to mount an Azure file share outside of the Azure region it is hosted in, such as on-premises or in a different Azure region, the OS must support the encryption functionality of SMB 3.0.

## Prerequisites for mounting an Azure file share with Linux
<a id="smb-client-reqs"></a>

* **An existing Azure storage account and file share**: In order to complete this article, you need to have a storage account and file share. If you haven't already created one, see one of our quickstarts on the subject: [Create file shares - CLI](storage-how-to-use-files-cli.md).

* **Your storage account name and key** You will need the storage account name and key in order to complete this article. If you created one using the CLI quickstart you should already have them, otherwise, consult the CLI quickstart that was linked earlier, in order to learn how to retrieve your storage account key.

* **Pick a Linux distribution to suit your mounting needs.**  
      Azure Files can be mounted either via SMB 2.1 and SMB 3.0. For connections coming from clients on-premises or in other Azure regions, you must use SMB 3.0; Azure Files will reject SMB 2.1 (or SMB 3.0 without encryption). If you're accessing the Azure file share from a VM within the same Azure region, you may access your file share using SMB 2.1, if and only if, *secure transfer required* is disabled for the storage account hosting the Azure file share. We always recommend requiring secure transfer and using only SMB 3.0 with encryption.

    SMB 3.0 encryption support was introduced in Linux kernel version 4.11 and has been backported to older kernel versions for popular Linux distributions. At the time of this document's publication, the following distributions from the Azure gallery support mounting option specified in the table headers. 

* **The most recent version of the Azure Command Line Interface (CLI).** For more information on how to install the Azure CLI, see [Install the Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) and select your operating system. If you prefer to use the Azure PowerShell module in PowerShell 6+, you may, however the instructions below are presented for the Azure CLI.

### Minimum recommended versions with corresponding mount capabilities (SMB version 2.1 vs SMB version 3.0)
|   | SMB 2.1 <br>(Mounts on VMs within same Azure region) | SMB 3.0 <br>(Mounts from on premises and cross-region) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04+ | 16.04+ |
| RHEL | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ | 9+<sup>*</sup> |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

<sup>*</sup>Usage of a backported kernel might be required. You can install it with `sudo apt-get install linux-image-cloud-amd64`.

If your Linux distribution is not listed here, you can check to see the Linux kernel version with the following command:

```bash
uname -r
```

* <a id="install-cifs-utils"></a>**The cifs-utils package is installed.**  
    The cifs-utils package can be installed using the package manager on the Linux distribution of your choice. 

    On **Ubuntu** and **Debian-based** distributions, use the `apt-get` package manager:

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    On **RHEL** and **CentOS**, use the `yum` package manager:

    ```bash
    sudo yum install cifs-utils
    ```

    On **openSUSE**, use the `zypper` package manager:

    ```bash
    sudo zypper install cifs-utils
    ```

    On other distributions, use the appropriate package manager or [compile from source](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)

* **Decide on the directory/file permissions of the mounted share**: In the examples below, the permission `0777` is used to give read, write, and execute permissions to all users. You can replace it with other [chmod permissions](https://en.wikipedia.org/wiki/Chmod) as desired, though this will mean potentially restricting access. If you do use other permissions, you should consider also using uid and gid in order to retain access for local users and groups of your choice.

> [!NOTE]
> If you do not explicitly assign directory and file permission with dir_mode and file_mode, they will default to 0755.

* **Ensure port 445 is open**: SMB communicates over TCP port 445 - check to see if your firewall is not blocking TCP ports 445 from client machine. Replace **<your-resource-group>** and **<your-storage-account>**
    ```bash
    resourceGroup="<your-resource-group>"
    storageAccount="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroup \
        --name $storageAccount \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $fileHost | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    If the connection was successful, you should see something similar to the following output:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

## Using an Azure file share with Linux
To use an Azure file share with your Linux distribution, you must create a directory to serve as the mount point for the Azure file share. A mount point can be created anywhere on your Linux system, but it's common convention to create this under /mnt. After the mount point, you use the `mount` command to access the Azure file share.

You can mount the same Azure file share to multiple mount points if desired.

### Mount the Azure file share on-demand with `mount`
1. **Create a folder for the mount point**: Replace **<your-resource-group>**, **<your-storage-account>**, and **<your-file-share>** with the appropriate information for your environment:

    ```bash
    resourceGroup="<your-resource-group>"
    storageAccount="<your-storage-account>"
    fileShare="<your-file-share>"

    mntPath="/mnt/$storageAccount/$fileShare"

    sudo mkdir -p $mntPath
    ```

1. **Use the mount command to mount the Azure file share**. If you like, you can change the directory and file permissions of your mounted share.

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroup \
        --name $storageAccount \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShare

    storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroup \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccount,password=$storageAccountKey,dir_mode=0777,file_mode=0777,serverino
    ```

    > [!Note]  
    > The above mount command mounts with SMB 3.0. If your Linux distribution does not support SMB 3.0 with encryption or if it only supports SMB 2.1, you may only mount from an Azure VM within the same region as the storage account. To mount your Azure file share on a Linux distribution that does not support SMB 3.0 with encryption, you will need to [disable encryption in transit for the storage account](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

When you are done using the Azure file share, you may use `sudo umount $mntPath` to unmount the share.

### Create a persistent mount point for the Azure file share with `/etc/fstab`
1. **Create a folder for the mount point**: A folder for a mount point can be created anywhere on the file system, but it's common convention to create this under /mnt. For example, the following command creates a new directory, replace **<your-resource-group>**, **<your-storage-account>**, and **<your-file-share>** with the appropriate information for your environment:

    ```bash
    resourceGroup="<your-resource-group>"
    storageAccount="<your-storage-account>"
    fileShare="<your-file-share>"

    mntPath="/mnt/$storageAccount/$fileShare"

    sudo mkdir -p $mntPath
    ```

1. **Create a credential file to store the username (the storage account name) and password (the storage account key) for the file share.** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir /etc/smbcredentials
    fi

    storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroup \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccount.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccount" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **Change permissions on the credential file so only root can read or modify the password file.** Since the storage account key is essentially a super-administrator password for the storage account, setting the permissions on the file such that only root can access is important so that lower privilege users cannot retrieve the storage account key.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **Use the following command to append the following line to `/etc/fstab`**: Remember to replace **<storage_account_name>**, **<share_name>**, **<smb_version>**, and **<mount_point>** with the appropriate information for your environment. If your Linux distribution supports SMB 3.0 with encryption (see [Understand SMB client requirements](#smb-client-reqs) for more information), use **3.0** for **<smb_version>**. For Linux distributions that do not support SMB 3.0 with encryption, use **2.1** for **<smb_version>**. An Azure file share can only be mounted outside of an Azure region (including on-premises or in a different Azure region) with SMB 3.0.

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroup \
        --name $storageAccount \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShare

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,dir_mode=0777,file_mode=0777,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > The above mount command mounts with SMB 3.0. If your Linux distribution does not support SMB 3.0 with encryption or if it only supports SMB 2.1, you may only mount from an Azure VM within the same region as the storage account. To mount your Azure file share on a Linux distribution that does not support SMB 3.0 with encryption, you will need to [disable encryption in transit for the storage account](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## Securing your Linux distribution
In order to mount an Azure file share on Linux, port 445 must be accessible. Many organizations block port 445 because of the security risks inherent with SMB 1. SMB 1, also known as CIFS (Common Internet File System), is a legacy file system protocol included with many Linux distributions. SMB 1 is an outdated, inefficient, and most importantly insecure protocol. The good news is that Azure Files does not support SMB 1, and starting with Linux kernel version 4.18, Linux makes it possible to to disable SMB 1. We always [strongly recommend](https://aka.ms/stopusingsmb1) disabling the SMB 1 on your Linux clients before using SMB file shares in production.

Starting with Linux kernel 4.18, the SMB kernel module, which is called `cifs` (for legacy reasons), exposes a new module parameter (often referred to as *parm* by various external documenation), called `disable_legacy_dialects`. You can see this parameter with the following command:

```bash
modinfo -p cifs | grep disable_legacy_dialects
```

This command should output the following message:

```Output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Before disabling SMB 1, you must check to make sure that the SMB module is not currently loaded on your system (this happens automatically if you have mounted an SMB share). You can do this with the following command, which should output nothing if SMB is not loaded:

```bash
lsmod | grep cifs
```

To unload the module, first unmount all SMB shares (using the `umount` command as described above). You can identify all the mounted SMB shares on your system with the following command:

```bash
mount | grep cifs
```

Once you have unmounted all SMB file shares, it's safe to unload the module. You can do this with the modprobe command:

```bash
sudo modprobe -r cifs
```

You can manually load the module with SMB 1 unloaded using the modprobe command:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Finally, you can check the SMB module has been loaded with the parameter by looking at the loaded parameters in `/sys/module/cifs/parameters`:

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

### Disabling SMB 1 on Ubuntu and Debian-based distributions

### Disable SMB 1 on Fedora and CentOS

### Disable SMB 1 on openSUSE and SUSE Linux Enterprise Server 

## Feedback
Linux users, we want to hear from you!

The Azure Files for Linux users' group provides a forum for you to share feedback as you evaluate and adopt File storage on Linux. Email [Azure Files Linux Users](mailto:azurefileslinuxusers@microsoft.com) to join the users' group.

## Next steps

See these links for more information about Azure Files:

* [Planning for an Azure Files deployment](storage-files-planning.md)
* [FAQ](../storage-files-faq.md)
* [Troubleshooting](storage-troubleshoot-linux-file-connection-problems.md)
