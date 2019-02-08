---
title: Migrate data from an on-premises Hadoop cluster to Azure Data Lake Storage Gen2
description: Migrate data from an on-premises Hadoop cluster to Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.service: storage
ms.date: 01/16/2019
ms.author: normesta
ms.component: data-lake-storage-gen2
---

# Migrate data from an on-premises Hadoop cluster to Azure Data Lake Storage Gen2

You can migrate data from an on-premises Hadoop cluster by using a Data Box device and a few scripts. 

This article helps you complete these tasks:

:heavy_check_mark: Copy your data to a Data Box device.

:heavy_check_mark: Ship the Data Box device to Microsoft.

:heavy_check_mark: Move the data onto your Data Lake Storage Gen2 storage account.

:heavy_check_mark: Apply file permissions and map identities.

## Prerequisites

You need these things to complete the migration.

* An on-premises Hadoop cluster that contains your source data.

* Python version 2.7 or greater installed onto each head or edge node of the cluster.

* An [Azure Data Box device](https://azure.microsoft.com/services/storage/databox/).

* An Azure Storage account that **doesn't** have hierarchical namespaces enabled on it.

* An Azure Data Lake Storage Gen2 account (A storage account that **does** have hierarchical namespaces enabled on it).
A Hadoop cluster running in Azure (For example: An [HDInsight](https://azure.microsoft.com/services/hdinsight/) cluster).

* The Azure-based Hadoop cluster is configured to use your Azure Data Lake Storage Gen2 account.

If your ready, let's start.

## First, Copy your data to a Data Box device

To copy the data from your on-premises Hadoop cluster to a Data Box device, you'll set a few things up, and then use the [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) tool.

1. Connect the Data Box to an on-premises network.

   See [Need link description](need link).

2. Add the Domain Name Server (DNS) name to the host configuration file (*/etc/hosts*) of each node in your cluster.

3. Identify files and directories that you don't want to copy to the Data Box device (For example: files that contain information about system state).

   To do this, open a text editor such as Notepad, and then add a regular expression for each path that you want to exclude. Here's an example:

   ```
   .*ranger/audit.*
   .*/hbase/data/WALs.*
   ```

4. Save this information to a file that has the extension `.lst` (For example: `exclusions.lst`).

5. On your on-premises Hadoop cluster, run this DistCp job. `distcp` job.

   ```bash
   sudo -u hdfs hadoop distcp -Dfs.azure.account.key.{databox_dns}={databox_key} -filter ./exclusions.lst /[source directory] wasb://{container}@{databox_dns}/[path]
   ```

   This job copies data and metadata from your on-premises Hadoop Distributed File System (HDFS) store to the Data Box device.

## Ship the Data Box device to Microsoft

For guidance, see [Need link description](need link).

We'll load the data into your storage account (the account that **doesn't** have hierarchical namespaces enabled on it). You specified this account when you ordered the Data Box device.  

## Move the data onto your Data Lake Storage Gen2 storage account

From your Azure-based Hadoop cluster, run this DiscCp command:

```bash
hadoop distcp -Dfs.azure.account.key.{source_account}.dfs.windows.net={source_account_key} abfs://{source_container}@{source_account}.dfs.windows.net/[path] abfs://{dest_container}@{dest_account}.dfs.windows.net/[path]
```

This command copies both data and metadata from your storage account into your Data Lake Storage Gen2 storage account.

## Apply file permissions and map identities

Perform these tasks to complete the migration.

:heavy_check_mark: Create a service principle for your Azure Data Lake Storage Gen2 account.

:heavy_check_mark: Download helper scripts and set up your local computer to run them.

:heavy_check_mark: Generate a list of copied files with their permissions.

:heavy_check_mark: Generate a list of identities and map them to Azure Active Directory (ADD) identities.

:heavy_check_mark: Apply permissions to copied files and apply identity mappings.

Let's go through each task.

### Create a service principle for your Azure Data Lake Storage Gen2 account

Before you create one, find the file system endpoint URI of your Data Lake Storage Gen2 account.

1. In the Azure Portal, choose **All Services** and filter on the term *storage*. Then, select **Storage accounts** and locate your storage account.

2. Then, choose **Properties**, and in the properties pane find the value of the **Primary ADLS FILE SYSTEM ENDPOINT** field. Paste that value into a text file for later.

3. Create a service principal by following the guidance in this article: [How to: Use the portal to create an Azure AD application and service principal that can access resources](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

   There's a few specific things that you have to do as you perform the steps in that article.

   * Make sure to set the **Sign-on URL** field of the **Create** dialog box to the endpoint URI that you just collected.

   * Make sure to assign your application to the **Blob Storage Data Owner Role**.

   * Paste the application ID, and authentication key values into a text file. You'll need those soon.

### Download helper scripts and set up your local computer to run them

1. From your edge or head node of your on-premises Hadoop cluster, run this command:

   ```bash
   git clone https://github.com/jamesbak/databox-adls-loader.git
   cd databox-adls-loader
   ```

   This command clones the Github repository that contains the helper scripts.

2. Make sure that have the [jq](https://stedolan.github.io/jq/) package installed on your local computer.

   ```bash
   sudo apt-get install jq
   ```

3. Install the [Requests](http://docs.python-requests.org/en/master/) python package.

   ```bash
   pip install requests
   ```

4. Set execute permissions on the required scripts.

   ```bash
   chmod +x *.py *.sh
   ```

### Generate a list of copied files with their permissions

From the on-premises Hadoop cluster, run this command:

```bash
sudo -u hdfs ./copy-acls.sh -s /{hdfs_path} > ./filelist.json
```

This command generates a list of copied files with their permissions.

> [!NOTE]
> Depending on the number of files in the HDFS, this command can take a long time to run.

### Generate a list of identities and map them to Azure Active Directory (ADD) identities

1. Run this command to generate a list of unique identities.

   ```bash
   ./copy-acls.py -s ./filelist.json -i id_map.json -g
   ```

   This script generates a file named `id_map.json` that contains the identities that you need to map to ADD-based identities.

2. Open the `id_map.json` file in a text editor such as Notepad.

3. For each JSON object that appears in the file, update the `target` attribute of either an AAD User Principal Name (UPN) or ObjectId (OID), with the appropriate mapped identity. After you're done, save the file. You'll need this file in the next step.

### Apply permissions to copied files and apply identity mappings

Run this command to apply permissions to the data that you copied into the Data Lake Storage Gen2 account:

```bash
./copy-acls.py -s ./filelist.json -i ./id_map.json  -A adlsgen2hnswestus2 -C databox1 --dest-spn-id {application-id}  --dest-spn-secret {authentication-key}
```

Replace the `application-id` and `authentication-key` placeholders with the application ID and authentication key that you collected when you created the service principle.

## Next steps

Learn how Data Lake Storage Gen2 works with HDInsight clusters. See [Use Azure Data Lake Storage Gen2 with Azure HDInsight clusters](https://docs.microsoft.com/Azure/storage/blobs/data-lake-storage-use-hdi-cluster?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)