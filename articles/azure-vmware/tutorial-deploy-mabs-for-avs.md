---
title: Tutorial - Deploy Microsoft Azure Backup Server (MABS) for Azure VMware Solution (AVS)
description: Learn to configure your Azure VMware Solution (AVS) environment to backup virtual machines using Microsoft Azure Backup Server (MABS).
ms.topic: tutorial
ms.date: 06/02/2020
---

# Tutorial: Deploy Microsoft Azure Backup Server for AVS

Microsoft Azure Backup Server (MABS) is a robust enterprise backup and recovery system that contributes to your BCDR strategy by facilitating the backup and recovery of enterprise data. During the AVS preview, you can configure only Virtual Machine level backup using MABS. This article explains how to prepare your AVS environment to backup Virtual Machines using MABS.

MABS can store backup data to:

-  **Disk**: For short-term storage MABS backs up data to disk pools.

-  **Azure**: For both short-term and long-term storage off-premises, MABS data stored in disk pools can be backed up to the Microsoft Azure cloud using the Azure Backup service.

When outages occur and source data is unavailable, you can use MABS to easily restore data to the original source or to an alternate location. That way, if the original data is unavailable because of planned or unexpected issues, you can easily restore data to an alternate location.


In this tutorial, you learn how to:

> [!div class="checklist"]
> * Determine the recommended VM disk type and size to use
> * Create a Recovery Services vault that stores the recovery points
> * Set the storage replication for a Recovery Services vault
> * Add storage to Azure Backup Server

## Supported VMware features

-   **Agentless backup:** MABS does not require an agent to be installed on the vCenter or ESXi server, to back up the virtual machine. Instead, just provide the IP address or fully qualified domain name (FQDN), and sign in credentials used to authenticate the VMware server with MABS.

-   **Cloud-Integrated Backup:** MABS protects workloads to disk and cloud. Backup and recovery workflow of MABS helps you manage long-term retention and offsite backup.

-   **Detect and protect VMs managed by vCenter:** MABS detects and protects VMs deployed on a VMware server (vCenter or ESXi server). MABS also detects VMs managed by vCenter, allowing you to protect large deployments.

-   **Folder level auto protection:** vCenter lets you organize your VMs in VM folders. MABS detects these folders and lets you protect VMs at the folder level and includes all subfolders. When protecting folders, MABS not only protects the VMs in that folder, but also protects VMs added later. MABS detects new VMs daily and protects them automatically. As you organize your VMs in recursive folders, MABS automatically detects and protects the new VMs deployed in the recursive folders.

-   **MABS continues to protect vMotioned VMs within the cluster:** As VMs are vMotioned for load balancing within the cluster, MABS automatically detects and continues VM protection.

-   **Recover necessary files faster:** MABS can recover files/folders from a Windows VM without recovering the entire VM.

## Limitations

-   Update Rollup 1 for MABS v3 must be installed.

-   You cannot back up user snapshots before the first MABS backup. Once MABS completes the first backup, then you can back up user snapshots.

-   MABS cannot protect VMware VMs with pass-through disks and physical raw device mappings (pRDM).

-   MABS cannot detect or protect VMware vApps.

**To deploy the solution, you need to complete following five steps:**

-   Setup pre-requisites and environment

-   Create Azure Recovery Services Vault

-   Download and install the MABS server

-   Add storage to MABS server


![Deploy MABS solution diagram](./media/deploy-mabs/deploy-mabs-avs-diagram.png)

## Prerequisites for the Azure Backup Server environment


Consider the recommendations in this section when installing Azure Backup Server in your Azure environment.

### Azure Virtual Network

Ensure that you have configured virtual network as mentioned in the [Configure networking for your VMWare private cloud in Azure](tutorial-configure-networking.md) tutorial.

### Determine size of virtual machine

To install Microsoft Azure Backup Server, you must create a Windows virtual machine in the virtual network that you have created in the above step. When choosing a server for running Azure Backup Server, it is recommended you start with a gallery image of Windows Server 2019 Datacenter. The [Create your first Windows virtual machine in the Azure portal](../virtual-machines/windows/quick-create-portal.md) tutorial gets you started with the recommended VM in Azure, even if you’ve never used Azure.

The table summarizes the maximum number of protected workloads for each MABS virtual machine size. The information is based on internal performance and scale tests with canonical values for the workload size and churn. The actual workload size can be larger but should be accommodated by the disks attached to the MABS virtual machine.

| Max protected workloads | Average workload size | Average workload churn (daily) | Min Storage IOPS | Recommended Disk Type / Size      | Recommended VM Size |
|-------------------------|-----------------------|--------------------------------|------------------|-----------------------------------|---------------------|
| 20                      | 100 GB                | Net 5% churn                   | 2000             | Standard HDD (8 TB or above size per disk)  | A4V2       |
| 40                      | 150 GB                | Net 10% churn                  | 4500             | Premium SSD* (1 TB or above size per disk) | DS3_V2     |
| 60                      | 200 GB                | Net 10% churn                  | 10500            | Premium SSD* (8 TB or above size per disk) | DS3_V2     |

* To get the required IOPs use minimum recommended or higher size disks. Smaller size disk will offer lower IOPS.

> [!NOTE]
> Azure Backup Server is designed to run on a dedicated, single-purpose server. You cannot install Azure Backup Server on a computer:
> * A computer running as a domain controller
> * A computer on which the Application Server role is installed
> * A computer that is a System Center Operations Manager management server
> * A computer on which Exchange Server is running
> * A computer that is a node of a cluster

### Disks and storage

MABS requires disks for MABS installation, including system files, installation files, prerequisite software, and database files. Also dedicated disks for storage pool.

| Requirement                      | Recommended Size  |
|----------------------------------|-------------------------|
| MABS installation                | MABS installation location: 3 GB<br />Database files drive: 900 MB<br />System drive: 1 GB for SQL installation<br /><br />Additionally, you'll need space for MABS to copy the file catalog to a temporary MABS installation location when archiving. 2-3 GB of free space is recommended for the MABS installation volume.       |
| Disk for storage pool<br />(Uses basic volumes, cannot be on a dynamic disk.) | 2 to 3 times the size of the protected data<br />For detailed storage calculation, refer [DPM Capacity Planner](https://www.microsoft.com/download/details.aspx?id=54301).   |

The [Attach a managed data disk to a Windows VM by using the Azure portal](../virtual-machines/windows/attach-managed-disk-portal.md) articles shows you how to attach a new managed data disk to an existing Azure VM.

> [!NOTE]
> A single Azure Backup Server has a soft limit of 120 TB for storage pool.

### Store backup data on local disk and in Azure

Storing backup data in Azure reduces backup infrastructure on Azure Backup Server VM. For operational recovery (backup), Azure Backup Server stores backup data on Azure disks attached to the virtual machine. Once the disks and storage space are attached to the virtual machine, Azure Backup Server manages the storage for you. The amount of backup data storage depends on the number and size of disks attached to each Azure VM, and each size of the Azure VM has a maximum number of disks that can be attached. For example, A2 is four disks. A3 is eight disks. A4 is 16 disks. Again, the size and number of disks determine the total backup storage pool capacity.

> [!IMPORTANT]
> You should **not** retain operational recovery data on Azure Backup Server-attached disks for more than five days. If data is more than five days old, store it in Azure Recovery Services Vault.

To store backup data in Azure, create or use a Recovery Services vault. When preparing to back up the Azure Backup Server workload, you [configure the Recovery Services vault](#create-a-recovery-services-vault). Once configured, each time an online backup job runs, a recovery point gets created in the vault. Each Recovery Services vault holds up to 9999 recovery points. Depending on the number of recovery points created, and how long they are retained, you can retain backup data for many years. For example, you could create monthly recovery points and retain them for five years.

> [!IMPORTANT]
> Whether you send backup data to Azure or keep it locally, you must register Azure Backup Server with a Recovery Services vault.

### Scale deployment

If you want to scale your deployment, you have the following options:

-   **Scale up** - Increase the size of the Azure Backup Server virtual machine from A series to DS3 series, and increase the local storage.

-   **Offload data** - send older data to Azure and retain only the newest data on the storage attached to the Azure Backup Server.

-   **Scale out** - Add more Azure Backup Servers to protect the workloads.

### .NET Framework

The VM must have .NET Framework 3.5 SP1 or higher installed.

### Join a domain

The Azure Backup Server VM must be joined to a domain and a domain user with administrator privileges on the VM must install Azure Backup Server.

Though this is not supported at the time of preview, MABS deployed in an Azure VM can back up workloads on the VMs in AVS, but they should be in same domain to enable backup operation.

## Create a Recovery Services vault

A Recovery Services vault is a storage entity that stores the recovery points created over time. It also contains the backup policies that are associated with protected items.

To create a Recovery Services vault, follow these steps.

1.  Sign into your subscription in the [Azure portal](https://portal.azure.com/).

1.  On the left menu, select **All services**.

    ![Select All services](../backup/media/backup-create-rs-vault/click-all-services.png)

1.  In the **All services** dialog box, enter *Recovery Services* and select **Recovery Services vaults** from the list.

    ![Enter and choose Recovery Services vaults](../backup/media/backup-create-rs-vault/all-services.png)

    The list of Recovery Services vaults in the subscription appears.

1.  On the **Recovery Services vaults** dashboard, select **Add**.

    ![Add a Recovery Services vault](../backup/media/backup-create-rs-vault/add-button-create-vault.png)

    The **Recovery Services vault** dialog box opens.

1.  Provide values for the **Name**, **Subscription**, **Resource group**, and **Location**.

    ![Configure the Recovery Services vault](../backup/media/backup-create-rs-vault/create-new-vault-dialog.png)

    -  **Name**: Enter a friendly name to identify the vault. The name must be unique to the Azure subscription. Specify a name that has at least 2 but not more than 50 characters. The name must start with a letter and consist only of letters, numbers, and hyphens.

    -  **Subscription**: Choose the subscription to use. If you're a member of only one subscription, you'll see that name. If you're not sure which subscription to use, use the default (suggested) subscription. There are multiple choices only if your work or school account is associated with more than one Azure subscription.

    -  **Resource group**: Use an existing resource group or create a new one. To see the list of available resource groups in your subscription, select **Use existing**, and then select a resource from the drop-down list. To create a new resource group, select **Create new** and enter the name.

    -  **Location**: Select the geographic region for the vault. To create a vault to protect AVS virtual machines, the vault *must* be in the same region as the AVS Private Cloud.

1.  When you're ready to create the Recovery Services vault, select **Create**.

    ![Create the Recovery Services vault](../backup/media/backup-create-rs-vault/click-create-button.png)

    It can take a while to create the Recovery Services vault. Monitor the status notifications in the **Notifications** area at the upper-right corner of the portal. After your vault is created, it's visible in the list of Recovery Services vaults. If you don't see your vault, select **Refresh**.

    ![Refresh the list of backup vaults](../backup/media/backup-create-rs-vault/refresh-button.png)

## Set Storage Replication

The storage replication option lets you choose between geo-redundant storage (the default) and locally redundant storage. Geo-redundant storage copies the data in your storage account to a secondary region, making your data durable. Locally redundant storage is a cheaper option that isn’t durable. Read more about
geo-redundant and locally redundant storage options in the [Azure Storage redundancy](../storage/common/storage-redundancy.md).

> [!IMPORTANT]
> Changing **Storage Replication type** (Locally-redundant/ Geo-redundant) for a Recovery services vault must be done before configuring backups in the vault. Once you configure backup, the option to modify it is disabled and you cannot change the **Storage Replication type**.

1.  From **Recovery Services vaults**, click the new vault. 

1.  Under the **Settings** section, click **Properties**.

2.  In **Properties**, under **Backup Configuration**, click **Update**.

3.  Select the storage replication type, and click **Save**.

    ![Set the storage configuration for new vault](../backup/media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

## Download and Install Software package

### Downloading the software package

1. Sign in to the [Azure portal](https://portal.azure.com/).

2. If you already have a Recovery Services vault open, continue to the next step. If you do not have a Recovery Services vault open, but are in the Azure portal, on the main menu, click **Browse**.

   1.  In the list of resources, type **Recovery Services**.

   1.  As you begin typing, the list will filter based on your input. When you see **Recovery Services vaults**, click it.

   ![Create Recovery Services Vault step 1](../backup/media/backup-azure-microsoft-azure-backup/open-recovery-services-vault.png)

    The list of Recovery Services vaults appears.

1.  From the list of Recovery Services vaults, select a vault.

    The selected vault dashboard opens.

    ![Open vault blade](../backup/media/backup-azure-microsoft-azure-backup/vault-dashboard.png)

    The **Settings** opens by default. If closed, select **Settings** to open it.

    ![Open vault blade](../backup/media/backup-azure-microsoft-azure-backup/vault-setting.png)

1.  Click **Backup** to open the Getting Started wizard.

    ![Backup getting started](../backup/media/backup-azure-microsoft-azure-backup/getting-started-backup.png)

1.  In the window that opens, do the following:

    1.  From the **Where is your workload running** menu, select **On-premises**.

       ![Where is your workload running?](./media/deploy-mabs/deploy-mabs-on-premises-workload.png)

    1.  From the **What do you want to back up** menu, select the workloads you want to protect using Azure Backup Server.

    1.  Click on **Prepare Infrastructure** to download and install Azure Backup Server and the vault credentials.

       ![Prepare Infrastructure](./media/deploy-mabs/deploy-mabs-prepare-infrastructure.png)

1.  In the **Prepare infrastructure** window that opens, do the following:

    1.  Click the **Download** link to Install Azure Backup Server.

    2.  Download the vault credentials by selecting the **Already Downloaded or using the latest Azure Backup Server installation** checkbox and then click **Download**. You use the vault credentials during registration of Azure Backup Server to the recovery services vault. The links take you to the Download Center where the software package can be downloaded.

    ![Prepare Infrastructure – Azure Backup Server](./media/deploy-mabs/deploy-mabs-prepare-infrastructure2.png)

1.  On the download page, select all the files and click **Next** to download
    all the files.

    > [!NOTE]
    > You must download all the files to the same folder. Also, since the download size of all the files together is > 3G, on a 10-Mbps download link it may take up to 60 minutes for the download to complete.

    ![Download center 1](../backup/media/backup-azure-microsoft-azure-backup/downloadcenter.png)

### Extracting the software package

If you downloaded the software package to different server, copy the files onto
the Virtual Machine that you created to deploy the MABS server.

> [!WARNING]
> At least 4 GB of free space is required to extract the setup files.

1.  After you've downloaded all the files, double-click **MicrosoftAzureBackupInstaller.exe** to open will start the **Microsoft Azure Backup Setup Wizard** and then click **Next**.

2.  Select the location to extract the files and click **Next**.

3.  Click **Extract** to begin the extraction process.

   ![Microsoft Azure Backup Setup Wizard](../backup/media/backup-azure-microsoft-azure-backup/extract/03.png)

1.  Once extracted, select the option to **Execute setup.exe** and then click **Finish**.

> [!TIP]
> You can also locate the setup.exe file from the folder where you extracted the software package.

### Installing the software package

1.  On the setup window under Install, click **Microsoft Azure Backup** to open the setup wizard.

    ![Microsoft Azure Backup Setup Wizard](../backup/media/backup-azure-microsoft-azure-backup/launch-screen2.png)

1.  On the Welcome screen, click **Next** to continue to the Prerequisite Checks.

2.  Click **Check** to determine if the hardware and software prerequisites for Azure Backup Server have been met. If met successfully, click **Next**.

    ![Azure Backup Server - Welcome and Prerequisites check](../backup/media/backup-azure-microsoft-azure-backup/prereq/prereq-screen2.png)

1.  The Azure Backup Server installation package comes bundled with the appropriate SQL Server binaries needed. When starting a new Azure Backup Server installation, select the **Install new Instance of SQL Server with this Setup** option and click **Check and Install**.

    ![Azure Backup Server - SQL check](../backup/media/backup-azure-microsoft-azure-backup/sql/01.png)

    > [!NOTE]
    > If you wish to use your own SQL server, the supported SQL Server versions are SQL Server 2014 SP1 or higher, 2016 and 2017. All SQL Server versions should be Standard or Enterprise 64-bit. Azure Backup Server will not work with a remote SQL Server instance. The instance being used by Azure Backup Server needs to be local. If you are using an existing SQL server for MABS, the MABS setup only supports the use of *named instances* of SQL server.

    If a failure occurs with a recommendation to restart the machine, do so and click **Check Again**. If there are any SQL configuration issues, reconfigure SQL as per the SQL guidelines and retry to install/upgrade MABS using the existing instance of SQL.

    **Manual configuration**

    When you use your own instance of SQL, make sure you add builtin\Administrators to sysadmin role to master DB.

    **SSRS Configuration with SQL 2017**

    When you are using your own instance of SQL 2017, you need to manually configure SSRS. After SSRS configuration, ensure that *IsInitialized* property of SSRS is set to *True*. When set to True, MABS assumes that SSRS is already configured and will skip the SSRS configuration.

    To check the SSRS configuration status, you can use the following command.

    ```powershell

    $configset =Get-WmiObject –namespace
    "root\Microsoft\SqlServer\ReportServer\RS_SSRS\v14\Admin" -class
    MSReportServer_ConfigurationSetting -ComputerName localhost

    $configset.IsInitialized

    ```

    Use the following values for SSRS configuration:

    -   Service Account: ‘Use built-in account’ should be Network Service

    -   Web Service URL: ‘Virtual Directory’ should be ReportServer_<SQLInstanceName>

    -   Database: DatabaseName should be ReportServer$<SQLInstanceName>

    -   Web Portal URL: ‘Virtual Directory’ should be Reports_<SQLInstanceName>

    [Learn more](https://docs.microsoft.com/sql/reporting-services/report-server/configure-and-administer-a-report-server-ssrs-native-mode?view=sql-server-2017) about SSRS configuration.

    > [!NOTE]

    > Licensing for SQL Server used as the database for MABS is governed by [Microsoft Online Services Terms](https://www.microsoft.com/licensing/product-licensing/products)  (OST). According to OST, SQL Server bundled with MABS can be used only as the database for MABS.

1.  Once the prerequisites are successfully installed, click **Next**.

2.  Provide a location for the installation of Microsoft Azure Backup server files and click **Next**.

    > [!NOTE]

    > The scratch location is required for back up to Azure. Ensure the scratch location is at least 5% of the data planned to be backed up to the cloud. For disk protection, separate disks need to be configured once the installation completes. For more information regarding storage pools, see [Configure storage pools and disk storage](https://docs.microsoft.com/previous-versions/system-center/system-center-2012-R2/hh758075(v=sc.12)).

    ![Microsoft Azure Backup PreReq2](../backup/media/backup-azure-microsoft-azure-backup/space-screen.png)

1.  Provide a strong password for restricted local user accounts and click **Next**.

    ![Microsoft Azure Backup PreReq2](../backup/media/backup-azure-microsoft-azure-backup/security-screen.png)

1.  Select whether you want to use Microsoft Update to check for updates and click **Next**.

    > [!NOTE]
    > We recommend having Windows Update redirect to Microsoft Update, which offers security and important updates for Windows and other products like Microsoft Azure Backup Server.

   ![Microsoft Azure Backup PreReq2](../backup/media/backup-azure-microsoft-azure-backup/update-opt-screen2.png)

1.  Review the *Summary of Settings* and click **Install**.

    The installation happens in phases. The first phase installs the Microsoft Azure Recovery Services Agent and the second phase checks for Internet connectivity. If Internet connectivity is available you can continue with the installation, if not, you need to provide proxy details to connect to the Internet. The final phase checks if the prerequisite software is installed, and if not, any missing software will be installed along with the Microsoft Azure Recovery Services Agent.

1.  Click **Browse** to locate your vault credentials to register the machine to the Recovery Services vault and then click **Next**.

2.  Choose a passphrase to encrypt/decrypt the data sent between Azure and your premises.

    > [!TIP]
    > You can automatically generate a passphrase or provide your own minimum 16-character passphrase.

1. Enter the location to save the passphrase and then click **Next** to register the server.

    > [!IMPORTANT]
    > It is important to also save the passphrase to a safe location besides the local server. Microsoft strongly suggested using an Azure Key Vault for storing the passphrase.

    After the Microsoft Azure Recovery Services Agent setup completes, it moves onto THe installation and configuration of SQL Server and the Azure Backup Server components.

    ![Azure Backup Server](../backup/media/backup-azure-microsoft-azure-backup/final-install/venus-installation-screen.png)

1.  Once the installation step completes, click **Close**.

### Install Update Rollup 1

Download and install the Update Rollup 1 for MABS v3 before protecting the workloads. You can download the update from this link. [still need the link to the download]

## Add storage to Azure Backup Server

Azure Backup Server V3 supports Modern Backup Storage that offers:

-   Storage savings of 50%

-   Backups that are three times faster

-   More efficient storage

-   Workload-aware storage

### Volumes in Backup Server

Add the data disks with the required storage capacity to the Azure Backup server virtual machine if not already added.

Backup Server V3 only accepts storage volumes. When you add a volume, Backup Server formats the volume to Resilient File System (ReFS), which Modern Backup Storage requires.

### Add volumes to Backup Server disk storage

1. In the **Management** pane, rescan the storage, and then select **Add**. 

2. Select from the available volumes to add to the storage pool. 

3. After adding the available volumes, give them a friendly name to help you manage them. 

4. Click **OK** to format these volumes to ReFS so Backup Server can use the benefits of Modern Backup Storage.

![Add Available Volumes](../backup/media/backup-mabs-add-storage/mabs-add-storage-7.png)

**Next Steps**

In this tutorial, you learned how to:

> [!div class="checklist"]
> * Determine the recommended VM disk type and size to use
> * Create a Recovery Services vault that stores the recovery points
> * Set the storage replication for a Recovery Services vault
> * Add storage to Azure Backup Server


Continue to the next tutorial to learn how to configure back up of VMware VMs running on Azure VMware Solution (AVS) using Azure Backup Server.

> [!div class="nextstepaction"]
> [Configure backup of AVS VMs](tutorial-backup-avs-vms-with-mabs.md)

