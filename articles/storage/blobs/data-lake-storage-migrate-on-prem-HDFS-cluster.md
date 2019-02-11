---
title: Migrate data from an on-premises Hadoop cluster to Azure Data Lake Storage Gen2
description: Migrate data from an on-premises Hadoop cluster to Azure Data Lake Storage Gen2
services: storage
author: normesta
ms.service: storage
ms.date: 02/11/2019
ms.author: normesta
ms.component: data-lake-storage-gen2
---

# Use Azure Data Box to migrate data from an on-premises Hadoop cluster to Azure Data Lake Storage Gen2

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

    - [Order your Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-ordered).
    - [Cable and connect your Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up) to an on-premises network.

* An Azure Storage account that **doesn't** have hierarchical namespaces enabled on it.

* An Azure Data Lake Storage Gen2 account (A storage account that **does** have hierarchical namespaces enabled on it).
A Hadoop cluster running in Azure (For example: An [HDInsight](https://azure.microsoft.com/services/hdinsight/) cluster).

* The Azure-based Hadoop cluster is configured to use your Azure Data Lake Storage Gen2 account.

If you are ready, let's start.

## Copy your data to a Data Box device

To copy the data from your on-premises Hadoop cluster to a Data Box device, you'll set a few things up, and then use the [DistCp](https://hadoop.apache.org/docs/stable/hadoop-distcp/DistCp.html) tool.

You can copy data to your Data Box via NFS or REST.

### Copy data via NFS

Before you begin, make sure that your Linux client is running a [Supported NFS version](). 

1. Create the directory where you will mount the Data Box NFS share.

    `mkdir /mnt/databox`

2. Mount the Data Box NFS share to the directory you created.

    `sudo mount -t nfs server_name:/nfsshare /mnt/databox`

3. You may need to give full access permission to the current user. Type:

    `chmod 777 /mnt/databox`

4. Create a folder in the mounted share of the Data Box. You will copy data to this folder.

    `mkdir /mnt/databox/hdfsdata`

5. Use the [Distcp](https://mapr.com/docs/52/ReferenceGuide/hadoop-distcp.html) command to copy from Hadoop HDFS to the Data Box NFS share. 

    `hadoop distcp -strategy dynamic -m 4 -update /source_hdfs_dir file:///mnt/databox/hdfsdata`

    - `m` or `map` is the maximum number of simultaneous copies. More maps do not necessarily improve throughput. Plan this number carefully to not affect the production cluster. Start with half the number of cluster nodes. For example, if the cluster has 8 nodes, use `m` as 4.
    - `strategy dynamic` allows faster data-nodes to copy more bytes than slower nodes. 

6. Wait for the copy jobs to finish. After the data copy is complete, proceed to [Ship the Data Box to Microsoft](#ship-data-box-to-microsoft).


### Copy data via REST

1. Before you copy the data via REST, you need to connect to the REST. Follow the instructions in 

    - [Connect to REST over http]().
    - [Connect to REST over https]().
    
2. You also need to configure and enable Data Box storage in the Hadoop configuration. First, verify the Hadoop version. 

    ```    
    # hadoop version
    ```
        
3. Verify that the following files are in your Hadoop installation:
    
    ```
    # ls -l $hadoop_install_dir/share/hadoop/tools/lib/ | grep azure
    -rw-r--r-- 1 user group   662947 Aug 17  2016 azure-storage-2.0.0.jar
    -rw-r--r-- 1 user group   157546 Aug 17  2016 hadoop-azure-2.7.3.jar
    ```

4. Modify the file `hadoop-env.sh` and add those two files to Hadoop classpath at the end of the file

    ```
    # vi $hadoop_install_dir/etc/hadoop/hadoop-env.sh
    export HADOOP_CLASSPATH=$HADOOP_HOME/share/hadoop/tools/lib/hadoop-azure-2.7.3.jar:$HADOOP_HOME/share/hadoop/tools/lib/azure-storage-2.0.0.jar       
    ```
5. Modify the file `core-site.xml` and add the following name-value pairs to point to the Data Box Object Storage (REST):

    ```
    # vi $hadoop_install_dir/etc/Hadoop/core-site.xml

    <property>
    <name>fs.AbstractFileSystem.wasb.Impl</name>
    <value>org.apache.hadoop.fs.azure.Wasb</value>
    </property>

    <property>
    <name>fs.azure.account.key.[Data_Box_Blob_Service_Endpoint]</name> 
    <value>my_blob_account_key</value>
    </property>
    ```
    You get the Data Box blob service endpoint from the **Connect and copy** page of the local web UI when you connect via REST. An example string for blob service endpoint (exclude `https://`) is: `mydataboxstorageaccount.blob.HCS-V6V3FS8OM9K.microsoftdatabox.com`.

6. Create a folder that maps to a blob container in the destination storage account. Follow the instructions in [Create a container](/azure/databox/data-box-deploy-copy-data-via-rest.md#create-a-container).

7. Copy data from the Hadoop HDFS to Data Box object storage location.

    ```
    # hadoop distcp -strategy dynamic -m 4 -update /[source_directory] wasb://[container_name]@[data_box_endpoint]/[destination_folder]
    ```
    The following example shows the command to copy data to Data Box object storage.
    
    ```
    # hadoop distcp -strategy dynamic -m 4 -update /data/testfiles wasb://hdfscontainer@utsac1.blob.HCS-V6V3FS8OM9K.microsoftdatabox.com/testfiles
    ```
    
## Ship the Data Box to Microsoft

Follow these steps to prepare and ship the Data Box device to Microsoft.

1. After the data copy is complete, run [Prepare to ship](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-rest#prepare-to-ship) on your Data Box. After the device preparation is complete, download the BOM files. You will use these BOM or manifest files later to verify the data uploaded to Azure. Shut down the device and remove the cables. 
2.	Schedule a pickup with UPS to [Ship your Data Box back to Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up). 
3.	After Microsoft receives your device, it is connected to the network datacenter and data is uploaded to the storage account you specified (with hierarchical namespaces disabled) when you ordered the Data Box. Verify against the BOM files that all your data is uploaded to Azure. You can now move this data to a Data Lake Storage Gen2 storage account.

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
