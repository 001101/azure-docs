---
title: Data transfer for small datasets with low to moderate network bandwidth| Microsoft Docs
description: Learn how to choose an Azure solution for data transfer when you have low to moderate network bandwidth in your environment and you are planning to transfer small datasets.
services: storage
author: alkohli

ms.service: storage
ms.subservice: blob
ms.topic: article
ms.date: 11/28/2018
ms.author: alkohli
---

# Data transfer for small datasets with low to moderate network bandwidth
 
This article provides an overview of the data transfer solutions when you have low to moderate network bandwidth in your environment and you are planning to transfer small datasets. The article also describes the recommended data transfer options and the respective key capability martix for this scenario.

To understand an overview of all the available data transfer options, go to [Choose an Azure data transfer solution](storage-choose-data-transfer-solution.md).

## Scenario description

Small datasets refer to data sizes in the order of GBs. Low to moderate network bandwidth implies 45 Mbps (T3 connection in datacenter) to 1 Gbps.

- If you are transferring only a handful of files or objects and you don’t need to automate data transfer, consider the tools with a graphical interface.
- If you are comfortable with system administration, consider command line or programmatic/scripting tools.

## Recommended options

The options recommended in this scenario are:

- **Graphical tools** such as Azure Storage Explorer, and Azure Blob storage and Data Lake Storage Gen 1, available in Azure portal. These provide an easy way to view your data and quickly transfer a few files or objects.

    - **Azure Storage Explorer** - This cross-platform tool lets you manage the contents of your Azure storage accounts. It allows you to upload, download, and manage blobs, files, queues, tables, and Azure Cosmos DB entities. Use it with Blob storage to manage blobs and folders, as well as upload and download blobs between your local file system and Blob storage, or between storage accounts.
    - **Azure portal** - Both Azure Blob storage and Data Lake Gen 1 Storage in Azure portal provide a web-based interface to explore files and upload new files one at a time. This is a good option if you do not want to install any tools or issue commands to quickly explore your files, or to simply upload a handful of new ones.

- **Scripting/programmatic tools** such as AzCopy/Azure PowerShell/Azure CLI and Azure Storage REST APIs.

    - **AzCopy** - Use this command-line tool to easily copy data to and from Azure Blobs, Files, and Table storage with optimal performance. AzCopy supports concurrency and parallelism, and the ability to resume copy operations when interrupted.
    - **PowerShell** - For users comfortable with system administration, use the `Start-AzureStorageBlobCopy` PowerShell cmdlet.
    - **Azure CLI** - Use this cross-platform tool to manage Azure services and upload data to Azure Storage.
    - **Azure Storage REST APIs** – When building an application, you can develop the application against Azure Storage REST APIs and use the Azure client libraries offered in multiple languages.


## Comparison of key capabilities

The following table summarizes the differences in key capabilities.

| Feature                                                            | Azure Storage Explorer                    | Azure portal (Blob storage or <br> Data Lake Storage Gen1) | AzCopy<br>PowerShell<br>Azure CLI            | Azure Storage REST APIs/SDKs/Azure client libraries |
|----------------------------------------------|-------------------------------------------|--------------------------------------|-------------------|---------------------------------------|
| Availability                                                       | Download and install <br>Standalone tool | Web-based exploration tools in Azure portal           | Command line tool |                                                         |
| Graphical   interface                                              | Yes                                       | Yes                                                     | No                | No                                                      |
| Supported   platforms                                              | Windows, Mac, Linux                       | Web-based     |<li> AzCopy, Azure CLI - Windows, Mac, Linux </li><li> Windows PowerShell - Windows     |                                                         |
| Allowed Blob storage operations<br>for blobs and folders            | Copy<br>Upload<br>Download<br>Manage    | Copy<br>Upload<br>Download<br>Manage  | Copy              |                                                         |
| Allowed Data Lake Gen1 storage<br>operations for files and folders  | Upload                                    | Upload                                                  |                   |                                                         |
| Allowed File storage operations<br>for files and directories        | Copy                                      | No                                                      | Copy              |                                                         |
| Allowed Table storage operations<br>for tables                      | Upload                                    | No            |||
| Allowed Azure Cosmos DB storage                                    | Upload                                    | No  |||
| Allowed Queue storage                                              | Upload                                    | No  |||
| Pricing                                                            | Free, data egress charges may apply    |  Free, data egress charges may apply  |  ||


## Next steps

- [Learn how to transfer data with Import/Export](/azure/storage/common/storage-import-export-data-to-blobs).
- Understand how to

    - [Transfer data with Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-quickstart-portal).
    - [Transfer data with Data Box](https://docs.microsoft.com/azure/databox/data-box-quickstart-portal).
 