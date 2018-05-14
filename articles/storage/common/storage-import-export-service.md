﻿---
title: Using Azure Import/Export to transfer data to and from Azure Storage | Microsoft Docs
description: Learn how to create import and export jobs in the Azure portal for transferring data to and from Azure Storage.
author: alkohli
manager: jeconnoc
services: storage

ms.service: storage
ms.topic: article
ms.date: 05/11/2018
ms.author: alkohli

---
# Use the Azure Import/Export service to transfer data to Azure Storage
In this article, we provide step-by-step instructions on using Azure Import/Export service to securely transfer large amounts of data to Azure Blob storage and Azure Files by shipping disk drives to an Azure data center. This service can also be used to transfer data from Azure storage to hard disk drives and ship to your on-premises sites. Data from a single internal SATA disk drive can be imported either to Azure Blob storage or Azure Files. 

## When should I use the Azure Import/Export service?
Consider using Azure Import/Export service when uploading or downloading data over the network is too slow, or getting additional network bandwidth is cost-prohibitive.

You can use this service in scenarios such as:

* **Migrating data to the cloud**: Move large amounts of data to Azure quickly and cost effectively.
* **Content distribution**: Quickly send data to your customer sites.
* **Backup**: Take backups of your on-premises data to store in Azure Storage.
* **Data recovery**: Recover large amount of data stored in storage and have it delivered to your on-premises location.

## How does the Azure Import/Export service work?
Transfer data between your on-premises site and Azure storage using the Azure Import/Export service by creating jobs and shipping disk drives to an Azure datacenter. !<--Each HDD you ship is associated with a single job.-->

Each job is associated with a single storage account. Review the [pre-requisites](#pre-requisites) carefully to learn about the specifics of this service such as supported data types, disk types, locations, and shipping.

In this section, high level steps involved in import and export of jobs are described. Later in the [Quick Start section](#quick-start), step-by-step instructions to create an import and export job is provided.

### Inside an import job
At a high level, an import job involves the following steps:

* Determine the data to be imported, and the number of drives you need.
* Identify the destination blob or file location for your data in Azure storage.
* Use the WAImportExport Tool to copy your data to one or more hard disk drives and encrypt them with BitLocker.
* Create an import job in your target storage account using the Azure portal or the Import/Export REST API. If using the Azure portal, upload the drive journal files.
* Provide the return address and carrier account number to be used for shipping the drives back to you.
* Ship the hard disk drives to the shipping address provided during job creation.
* Update the delivery tracking number in the import job details and submit the import job.
* Drives are received and processed at the Azure data center.
* Drives are shipped using your carrier account to the return address provided in the import job.
  
    ![Figure 1:Import job flow](./media/storage-import-export-service/importjob.png)

### Inside an export job
> [!IMPORTANT]
> The service only support export of Azure Blobs and does not support export of Azure Files..

At a high level, an export job involves the following steps:

* Determine the data to be exported and the number of drives you need.
* Identify the source blobs or container paths of your data in Blob storage.
* Create an export job in your source storage account using the Azure portal or the Import/Export REST API.
* Specify the source blobs or container paths of your data in the export job.
* Provide the return address and carrier account number for to be used for shipping the drives back to you.
* Ship the hard disk drives to the shipping address provided during job creation.
* Update the delivery tracking number in the export job details and submit the export job.
* The drives are received and processed at the Azure data center.
* The drives are encrypted with BitLocker; the keys are available via the Azure portal.  
* The drives are shipped using your carrier account to the return address provided in the import job.
  
    ![Figure 2:Export job flow](./media/storage-import-export-service/exportjob.png)

## Region availability 

### Azure regions for storage accounts
### Country availability

The Azure Import/Export service supports copying data to and from all Public Azure storage accounts. You can ship hard disk drives to one of the listed locations. If your storage account is in a public Azure location which is not specified here, an alternate shipping location will be provided when you are creating the job using the Azure portal or the Import/Export REST API.

Supported shipping locations:

* East US
* West US
* East US 2
* West US 2
* Central US
* North Central US
* South Central US
* West Central US
* North Europe
* West Europe
* East Asia
* Southeast Asia
* Australia East
* Australia Southeast
* Japan West
* Japan East
* Central India
* South India
* West India
* Canada Central
* Canada East
* Brazil South
* Korea Central
* US Gov Virginia
* US Gov Iowa
* US DoD East
* US DoD Central
* China East
* China North
* UK South
* Germany Central
* Germany Northeast

## Security considerations
The data on the drive is encrypted using BitLocker Drive Encryption. This encryption protects your data while it is in transit.

For import jobs, there are two ways to perform the encryption. The first way is to specify the option when using dataset CSV file while running the WAImportExport tool during drive preparation. The second way is to enable BitLocker encryption manually on the drive and specify the encryption key in the driveset CSV when running WAImportExport tool command line during drive preparation.

For export jobs, after your data is copied to the drives, the service encrypts the drive using BitLocker before shipping it back to you. The encryption key is provided to you via the Azure portal. 

<!--### View your job and drive status
You can track the status of your import or export jobs from the Azure portal. Click the **Import/Export** tab. A list of your jobs appears on the page.

![View Job State](./media/storage-import-export-service/jobstate.png)

You see one of the following job statuses depending on where your drive is in the process.

| Job Status | Description |
|:--- |:--- |
| Creating | After a job is created, its state is set to Creating. While the job is in the Creating state, the Import/Export service assumes the drives have not been shipped to the data center. A job may remain in the Creating state for up to two weeks, after which it is automatically deleted by the service. |
| Shipping | After you ship your package, you should update the tracking information in the Azure portal.  This will turn the job into "Shipping". The job will remain in the Shipping state for up to two weeks. 
| Received | After all drives have been received at the data center, the job state will be set to the Received. |
| Transferring | Once at least one drive has begun processing, the job state will be set to the Transferring. See the Drive States section below for detailed information. |
| Packaging | After all drives have completed processing, the job will be placed in the Packaging state until the drives are shipped back to you. |
| Completed | After all drives have been shipped back to the customer, if the job has completed without errors, then the job will be set to the Completed state. The job will be automatically deleted after 90 days in the Completed state. |
| Closed | After all drives have been shipped back to the customer, if there have been any errors during the processing of the job, then the job will be set to the Closed state. The job will be automatically deleted after 90 days in the Closed state. |

The table below describes the life cycle of an individual drive as it transitions through an import or export job. The current state of each drive in a job is now visible from the Azure portal.
The following table describes each state that each drive in a job may pass through.

| Drive State | Description |
|:--- |:--- |
| Specified | For an import job, when the job is created from the Azure portal, the initial state for a drive is the Specified state. For an export job, since no drive is specified when the job is created, the initial drive state is the Received state. |
| Received | The drive transitions to the Received state when the Import/Export service operator has processed the drives that were received from the shipping company for an import job. For an export job, the initial drive state is the Received state. |
| NeverReceived | The drive will move to the NeverReceived state when the package for a job arrives but the package doesn't contain the drive. A drive can also move into this state if it has been two weeks since the service received the shipping information, but the package has not yet arrived at the data center. |
| Transferring | A drive will move to the Transferring state when the service begins to transfer data from the drive to Windows Azure Storage. |
| Completed | A drive will move to the Completed state when the service has successfully transferred all the data with no errors.
| CompletedMoreInfo | A drive will move to the CompletedMoreInfo state when the service has encountered some issues while copying data either from or to the drive. The information can include errors, warnings, or informational messages about overwriting blobs.
| ShippedBack | The drive will move to the ShippedBack state when it has been shipped from the data center back to the return address. |

This image from the Azure portal displays the drive state of an example job:

![View Drive State](./media/storage-import-export-service/drivestate.png)

The following table describes the drive failure states and the actions taken for each state.

| Drive State | Event | Resolution / Next step |
|:--- |:--- |:--- |
| NeverReceived | A drive that is marked as NeverReceived (because it was not received as part of the job's shipment) arrives in another shipment. | The operations team will move the drive to the Received state. |
| N/A | A drive that is not part of any job arrives at the data center as part of another job. | The drive will be marked as an extra drive and will be returned to the customer when the job associated with the original package is completed. |

### Time to process job
The amount of time it takes to process an import/export job varies based on a number of factors such as shipping time, the load at the DataCenter, the job type and size of the data being copied, and number of disks in a job. Import/Export service does not have an SLA but the service strives to complete the copy in 7 to 10 days after the disks are received. In addition to the status posted on Azure Portal, REST APIs can be used to track the job progress. The percent complete parameter in the List Jobs operation API call provides the percentage copy progress.-->


### Pricing
**Drive handling fee**

There is a drive handling fee for each drive processed as part of your import or export job. See the details on the [Azure Import/Export Pricing](https://azure.microsoft.com/pricing/details/storage-import-export/).

**Shipping costs**

When you ship drives to Azure, you pay the shipping cost to the shipping carrier. When Microsoft returns the drives to you, the shipping cost is charged to the carrier account which you provided at the time of job creation.

**Transaction costs**

There are no transaction costs in addition to standard storage transaction costs when importing data into Azure Storage. The standard egress charges are applicable when data is exported from Blob storage. For more details on transaction costs, see [Data transfer pricing.](https://azure.microsoft.com/pricing/details/data-transfers/)










## Prerequisites
In this section, we list the prerequisites required to use this service. Review them carefully before shipping your drives.

### Storage account
You must have an existing Azure subscription and one or more storage accounts to use the Import/Export service. Azure Import/Export only supports classic, Blob Storage accounts and General Purpose v1 storage accounts. Each job may be used to transfer data to or from only one storage account. In other words, a single import/export job cannot span across multiple storage accounts. For information on creating a new storage account, see [How to Create a Storage Account](storage-create-storage-account.md#create-a-storage-account).

> [!IMPORTANT] 
> The Azure Import Export service does not support storage accounts where the [Virtual Network Service Endpoints](../../virtual-network/virtual-network-service-endpoints-overview.md) feature has been enabled. 
> 


### Data types


### Job
To begin the process of importing to or exporting from storage, you first create a job. A job can be an import job or an export job:

* Create an import job when you want to transfer data you have on-premises to your Azure storage account.
* Create an export job when you want to transfer data currently stored in your storage account to hard drives that are shipped to Microsoft. When you create a job, you notify the Import/Export service that you will be shipping one or more hard drives to an Azure data center.

* For an import job, you will be shipping hard drives containing your data.
* For an export job, you will be shipping empty hard drives.
* You can ship up to 10 hard disk drives per job.

You can create an import or export job using the Azure portal or the [Azure Storage Import/Export REST API](/rest/api/storageimportexport).

> [!Note]
> The RDFE APIs will not be supported February 28, 2018 onwards. To continue using the service, migrate to the [ARM Import/Export REST APIs](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/storageimportexport/resource-manager/Microsoft.ImportExport/stable/2016-11-01/storageimportexport.json). 

### WAImportExport tool
The first step in creating an **import** job is to prepare your drives that will be shipped for import. To prepare your drives, you must connect it to a local server and run the WAImportExport Tool on the local server. This WAImportExport tool facilitates copying your data to the drive, encrypting the data on the drive with BitLocker, and generating the drive journal files.

The journal files store basic information about your job and drive such as drive serial number and storage account name. This journal file is not stored on the drive. It is used during import job creation. Step-by-step details about job creation are provided later in this article.

The WAImportExport tool is only compatible with 64-bit Windows operating system. See the [Operating System](#operating-system) section for specific OS versions supported.

Download the latest version of the [WAImportExport tool](http://download.microsoft.com/download/3/6/B/36BFF22A-91C3-4DFC-8717-7567D37D64C5/WAImportExportV2.zip). For more details about using the WAImportExport Tool, see the [Using the WAImportExport Tool](storage-import-export-tool-how-to.md).

>[!NOTE]
>**Previous Version:** You can [download WAImportExpot V1](http://download.microsoft.com/download/0/C/D/0CD6ABA7-024F-4202-91A0-CE2656DCE413/WaImportExportV1.zip) version of the tool and refer to [WAImportExpot V1 usage guide](storage-import-export-tool-how-to-v1.md). WAImportExpot V1 version of the tool does provide support for **preparing disks when data is already pre-written to the disk**. If the only key available is SAS-Key, you need to use WAImportExpot V1 tool .

>

### Hard disk drives
Only 2.5 inch SSD or 2.5" or 3.5" SATA II or III internal HDD are supported for use with the Import/Export service. A single import/export job can have a maximum of 10 HDD/SSDs and each individual HDD/SSD can be of any size. Large number of drives can be spread across multiple jobs and there is no limits on the number of jobs that can be created. 

For import jobs, only the first data volume on the drive is processed. The data volume must be formatted with NTFS.

> [!IMPORTANT]
> External hard disk drives that come with a built-in USB adaptor are not supported by this service. Also, the disk inside the casing of an external HDD cannot be used; do not send external HDDs.
> 
> 

Following is a list of external USB adaptors used to copy data to internal HDDs. 
Anker 68UPSATAA-02BU
Anker 68UPSHHDS-BU
Startech SATADOCK22UE
Orico 6628SUS3-C-BK (6628 Series)
Thermaltake BlacX Hot-Swap SATA External Hard Drive Docking Station (USB 2.0 & eSATA)

### Encryption
The data on the drive must be encrypted using BitLocker Drive Encryption. This encryption protects your data while it is in transit.

For import jobs, there are two ways to perform the encryption. The first way is to specify the option when using dataset CSV file while running the WAImportExport tool during drive preparation. The second way is to enable BitLocker encryption manually on the drive and specify the encryption key in the driveset CSV when running WAImportExport tool command line during drive preparation.

For export jobs, after your data is copied to the drives, the service will encrypt the drive using BitLocker before shipping it back to you. The encryption key is provided to you via the Azure portal.  

### Operating System
You can use one of the following 64-bit Operating Systems to prepare the hard drive using the WAImportExport Tool before shipping the drive to Azure:

Windows 7 Enterprise, Windows 7 Ultimate, Windows 8 Pro, Windows 8 Enterprise, Windows 8.1 Pro, Windows 8.1 Enterprise, Windows 10, Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2. All of these operating systems support BitLocker Drive Encryption.


Follow the below steps if the data on the disk is to be imported into Azure Storage.
### Step 1: Prepare the drive/s using WAImportExport tool and generate journal file/s.

1.  Identify the data to be imported into Azure Storage. You can import directories and standalone files on a local server or a network share.
2.  Depending on total size of the data, procure the required number of 2.5 inch SSD or 2.5" or 3.5" SATA II or III hard disk drives.
3.	Attach the hard drives directly using SATA or with external USB adaptors to a windows machine.
1.  Create a single NTFS volume on each hard drive and assign a drive letter to the volume. No mountpoints.
2.  To enable encryption on the windows machine, enable bit locker encryption on the NTFS volume. Use the instructions on https://technet.microsoft.com/library/cc731549(v=ws.10).aspx.
3.  Completely copy data to these encrypted single NTFS volumes on disks using copy & paste or  drag & drop or Robocopy or any such tool.
7.	Download WAImportExport V1 from https://www.microsoft.com/en-us/download/details.aspx?id=42659
8.	Unzip to the default folder waimportexportv1. For example, C:\WaImportExportV1  
9.	Run as Administrator and open a PowerShell or Command line and change directory to the unzipped folder. For example, cd C:\WaImportExportV1
10.	Copy the following command line to a text editor and edit it to create a command line:

    ```
    ./WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /sk:***== /t:D /bk:*** /srcdir:D:\ /dstdir:ContainerName/ /skipwrite 
    ```
    
    These command line options are described in the following table:

    |Option  |Description  |
    |---------|---------|
    |/j:     |The name of the journal file, with the .jrn extension. A journal file is generated per drive. Using the disk serial number as the journal file name is recommended.         |
    |/sk:     |The Azure Storage account key.         |
    |/t:     |The drive letter of the disk to be shipped. For example, drive `D`.         |
    |/bk:     |The BitLocker key for the drive. Its numerical password from output of ` manage-bde -protectors -get D: `      |
    |/srcdir:     |The drive letter of the disk to be shipped followed by `:\`. For example, `D:\`.         |
    |/dstdir:     |The name of the destination container in Azure Storage         |
    |/skipwrite:     |The option that specifies that there is no new data required to be copied and existing data on the disk is to be prepared         |
1. Repeat step 10 for each of disk that needs to be shipped.
2. A journal file with name provided with /j: parameter is created for every run of the command line.

### Step 2: Create an Import Job on Azure Portal.

1. Log on to https://portal.azure.com/ and under More services -> STORAGE -> "Import/export jobs" Click **Create Import/export Job**.

2. In Basics section, select "Import into Azure", enter a string for job name, select a subscription, enter or select a resource group. Enter a descriptive name for the import job. Note that the name you enter may contain only lowercase letters, numbers, hyphens, and underscores, must start with a letter, and may not contain spaces. You use the name you choose to track your jobs while they are in progress and once they are completed.

3. In Job details section, upload the drive journal files that you obtained during the drive preparation step. If waimportexport.exe version1 was used, you need to upload one file for each drive that you have prepared. Select the storage account that the data will be imported into in the "Import destination" Storage account section. The Drop-Off location is automatically populated based on the region of the storage account selected.
   
   ![Create import job - Step 3](./media/storage-import-export-service/import-job-03.png)
4. In Return shipping info section, select the carrier from the drop-down list and enter a valid carrier account number that you have created with that carrier. Microsoft uses this account to ship the drives back to you once your import job is complete. Provide a complete and valid contact name, phone, email, street address, city, zip, state/proviince and country/region.
   
5. In the Summary section, Azure DataCenter shipping address is provided to be used for shipping disks to Azure DC. Ensure that the job name and the full address are mentioned on the shipping label. 

6. Click OK on the Summary Page to complete Import job creation.

### Step 3: Ship the drive/s to the Azure Datacenter shipping address provided in Step 2.
FedEx, UPS, or DHL can be used to ship the package to Azure DC.

### Step 4: Update the job created in Step2 with tracking number of the shipment.
After shipping the disks, return to the **Import/Export** page on the Azure portal to update the tracking number using the steps below, 
     a) Navigate and click on the import job
     b) Click on **Update job status and tracking info once drives are shipped**. 
     c) Select the check box "Mark as shipped"
     d) Provide the Carrier and Tracking number.
If the tracking number is not updated within 2 weeks of creating the job, the job will expire. The job progress can be tracked on the portal dashboard. See what each job state in the previous section means by [Viewing your job status](#viewing-your-job-status).

## Next steps

* [Setting up the WAImportExport tool](storage-import-export-tool-how-to.md)
* [Transfer data with the AzCopy command-line utility](storage-use-azcopy.md)
* [Azure Import Export REST API sample](https://azure.microsoft.com/documentation/samples/storage-dotnet-import-export-job-management/)

