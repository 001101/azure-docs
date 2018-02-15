---
title: 'Connect to Azure Databricks from Excel, Python, R, and SQL database | Microsoft Docs'
description: Learn how to use the Simba driver to connect Azure Databricks to Excel, Python, R, and SQL database.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun

ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/16/2018
ms.author: nitinme
ms.custom: mvc

---

# Connect to Azure Databricks from Excel, Python, R, and SQL database

In this article, you learn how to use the Databricks ODBC driver to connect Azure Databricks with Microsoft Excel, Python, Microsoft ML Server, and a SQL database.

## Prerequisites

* Create an Azure Databricks workspace and cluster. For instructions, see [Get started with Azure Databricks](quickstart-create-databricks-workspace-portal.md).

* Download the Databricks ODBC driver from [here](https://databricks.com/spark/odbc-driver-download).

* Set up a personal access token in Databricks. For instructions, see [Token management](https://docs.azuredatabricks.net/api/latest/authentication.html#token-management).

## Set up the DSN

A data source name (DSN) contains the information about a specific data source that an ODBC driver needs in order to connect to it. In this section, we set up a DSN that can be used with the Databricks ODBC driver to connect to Azure Databricks from clients like Microsoft Excel, Python, etc.

1. From the Azure Databricks workspace, navigate to the Databricks cluster.

    ![Open Databricks cluster](./media/connect-databricks-excel-python-r-sql-database/open-databricks-cluster.png "Open Databricks cluster")

2. Under the **Configuration** tab, click the **JDBC/ODBC** tab and copy the values for **Server Hostname** and **HTTP Path**. You need these values to complete the steps in this article.

    ![Get Databricks configuration](./media/connect-databricks-excel-python-r-sql-database/get-databricks-jdbc-configuration.png "Get Databricks configuration")

3. On your computer, start **ODBC Data Sources** application (32-bit or 64-bit) depending on your computer.

    ![Launch ODBC](./media/connect-databricks-excel-python-r-sql-database/launch-odbc-app.png "Launch ODBC app")

4. Under the **User DSN** tab, click **Add**. In the **Create New Data Source** dialog box, select the **Simba Spark ODBC Driver**, and then click **Finish**.

    ![Launch ODBC](./media/connect-databricks-excel-python-r-sql-database/add-new-user-dsn.png "Launch ODBC app")

4. In the **Simba Spark ODBC Driver** dialog box, provide the following values.




## Create a Spark cluster in Databricks

1. In the Azure portal, go to the Databricks workspace that you created, and then click **Initialize Workspace**.

2. You are redirected to the Azure Databricks portal. From the portal, click **Cluster**.

    ![Databricks on Azure](./media/quickstart-create-databricks-workspace-portal/databricks-on-azure.png "Databricks on Azure")

3. In the **New cluster** page, provide the values to create a cluster.

    ![Create Databricks Spark cluster on Azure](./media/quickstart-create-databricks-workspace-portal/create-databricks-spark-cluster.png "Create Databricks Spark cluster on Azure")

    * Enter a name for the cluster.
    * Make sure you select the **Terminate after ___ minutes of activity** checkbox. Provide a duration (in minutes) to terminate the cluster, if the cluster is not being used.
    * Accept all other default values. 
    * Click **Create cluster**. Once the cluster is running, you can attach notebooks to the cluster and run Spark jobs.

For more information on creating clusters, see [Create a Spark cluster in Azure Databricks](https://docs.azuredatabricks.net/user-guide/clusters/create.html).

## Run a Spark SQL job

Before you begin with this section, you must complete the following:

* [Create an Azure storage account](../storage/common/storage-create-storage-account.md#create-a-storage-account). 
* Download a sample JSON file [from Github](https://github.com/Azure/usql/blob/master/Examples/Samples/Data/json/radiowebsite/small_radio_json.json). 
* Upload the sample JSON file to the Azure storage account you created. You can use [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) to upload files.

Perform the following steps to create a notebook in Databricks, configure the notebook to read data from an Azure Blob storage account, and then run a Spark SQL job on the data.

1. In the left pane, click **Workspace**. From the **Workspace** drop-down, click **Create**, and then click **Notebook**.

    ![Create notebook in Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-create-notebook.png "Create notebook in Databricks")

2. In the **Create Notebook** dialog box, enter a name, select **Scala** as the language, and select the Spark cluster that you created earlier.

    ![Create notebook in Databricks](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-details.png "Create notebook in Databricks")

    Click **Create**.

3. In the following snippet, replace `{YOUR STORAGE ACCOUNT NAME}` with the  Azure storage account name you created and `{YOUR STORAGE ACCOUNT ACCESS KEY}` with your storage account access key. Paste the snippet in an empty cell in the notebook and then press SHIFT + ENTER to run the code cell. This snippet configures the notebook to read data from an Azure blob storage.

       spark.conf.set("fs.azure.account.key.{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net", "{YOUR STORAGE ACCOUNT ACCESS KEY}")
    
    For instructions on how to retrieve the storage account key, see [Manage your storage access keys](../storage/common/storage-create-storage-account.md#manage-your-storage-account)

    > [!NOTE]
    > You can also use Azure Data Lake Store with a Spark cluster on Azure Databricks. For instructions, see [Use Data Lake Store with Azure Databricks](https://go.microsoft.com/fwlink/?linkid=864084).

4. Run a SQL statement to create a temporary table using data from the sample JSON data file, **small_radio_json.json**. In the following snippet, replace the placeholder values with your container name and storage account name. Paste the snippet in a code cell in the notebook, and then press SHIFT + ENTER. In the snippet, `path` denotes the location of the sample JSON file that you uploaded to your Azure Storage account.

    ```sql
    %sql 
    CREATE TEMPORARY TABLE radio_sample_data
    USING json
    OPTIONS (
     path "wasbs://{YOUR CONTAINER NAME}@{YOUR STORAGE ACCOUNT NAME}.blob.core.windows.net/small_radio_json.json"
    )
    ```

    Once the command successfully completes, you have all the data from the JSON file as a table in Databricks cluster.

    The `%sql` language magic command enables you to run a SQL code from the notebook, even if the notebook is of another type. For more information, see [Mixing languages in a notebook](https://docs.azuredatabricks.net/user-guide/notebooks/index.html#mixing-languages-in-a-notebook).

5. Let's look at a snapshot of the sample JSON data to better understand the query that we run. Paste the following snippet in the code cell and press **SHIFT + ENTER**.

    ```sql
    %sql 
    SELECT * from radio_sample_data
    ```

6. You see a tabular output like shown in the following screenshot (only some columns are shown):

    ![Sample JSON data](./media/quickstart-create-databricks-workspace-portal/databricks-sample-csv-data.png "Sample JSON data")

    Among other details, the sample data captures the gender of the audience of a radio channel (column name, **gender**)  and whether their subscription is free or paid (column name, **level**).

7. You now create a visual representation of this data to show for each gender, how many users have free accounts and how many are paid subscribers. From the bottom of the tabular output, click the **Bar chart** icon, and then click **Plot Options**.

    ![Create bar chart](./media/quickstart-create-databricks-workspace-portal/create-plots-databricks-notebook.png "Create bar chart")

8. In **Customize Plot**, drag-and-drop values as shown in the screenshot.

    ![Customize bar chart](./media/quickstart-create-databricks-workspace-portal/databricks-notebook-customize-plot.png "Customize bar chart")

    * Set **Keys** to **gender**.
    * Set **Series groupings** to **level**.
    * Set **Values** to **level**.
    * Set **Aggregation** to **COUNT**.

    Click **Apply**.

9. The output shows the visual representation as depicted in the following screenshot:

     ![Customize bar chart](./media/quickstart-create-databricks-workspace-portal/databricks-sql-query-output-bar-chart.png "Customize bar chart")

## Clean up resources

While creating the Spark cluster, if you selected the checkbox **Terminate after __ minutes of inactivity**, the cluster will automatically terminate if it has been inactive for the specified time.

If you did not select the checkbox, you must manually terminate the cluster. To do so, from the Azure Databricks workspace, from the left pane, click **Clusters**. For the cluster you want to terminate, move the cursor over the ellipsis under **Actions** column, and click the **Terminate** icon.

![Terminate Databricks cluster](./media/quickstart-create-databricks-workspace-portal/terminate-databricks-cluster.png "Terminate Databricks cluster")

## Next steps

In this article, you created a Spark cluster in Azure Databricks and ran a Spark job using data in Azure storage. You can also look at [Spark data sources](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html) to learn how to import data from other data sources into Azure Databricks. Advance to the next article to learn how to use Azure Data Lake Store with Azure Databricks.

> [!div class="nextstepaction"]
>[Use Data Lake Store with Azure Databricks](https://go.microsoft.com/fwlink/?linkid=864084)
