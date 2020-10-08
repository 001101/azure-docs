---
title: 'Quickstart: Connect Azure Data Explorer to a Synapse workspace'
description: How to connect an Azure Data Explorer cluster to a Synapse workspace using Azure Synapse Apache Spark
services: synapse-analytics 
author: manojraheja
ms.service: synapse-analytics 
ms.topic: quickstart
ms.subservice: 
ms.date: 10/07/2020
ms.author: maraheja
ms.reviewer: jrasnick
---

# Connect to Azure Data Explorer using Synapse Apache Spark

This article describes how to access an Azure Data Explorer databases from Azure Synapse Analytics studio with Synapse Apache Spark. 

## Prerequisites

* [Create an Azure Data Explorer cluster and database](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal).
* Existing Synapse workspace or create a new workspace following this [quickstart](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace) 
* Existing Synapse Apache Spark pool  or create a new pool following this [quickstart](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool-portal)
* [Create Azure AD app by provisioning an Azure AD application.](https://docs.microsoft.com/azure/data-explorer/kusto/management/access-control/how-to-provision-aad-app)
* Grant your Azure AD app access your database following [Manage Azure Data Explorer database permissions](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions)

## Navigate to Synapse Studio

From a Synapse workspace, select **Launch Synapse Studio**. On the Synapse Studio home page, select **Data**, which will take you to the **Data Object Explorer**.

## Connect an Azure Data Explorer database to a Synapse workspace

Connecting an Azure Data Explorer database to a workspace is done through linked service. An Azure Data Explore linked service enables users to browse and explore data, read, and write from Apache Spark for Azure Synapse Analytics and run orchestration jobs in a pipeline.

From the Data Object Explorer, you can directly connect an Azure Data Explorer cluster by doing the following steps:

1. Select **+** icon near Data
2. Select **Connect** to external data
3. Select **Azure Data Explorer (Kusto)**
5. Select **Continue**
6. Name the linked service. The name will be displayed in the Object Explorer and used by Synapse run-times to connect to the database. We recommend using a friendly name.
7. Select the Azure Data Explore cluster from your Subscription or enter the URI.
8. Enter the "Service principal ID" and "Service principal key" (ensure this Service principal has view access on the database for read operation and ingestor access for ingesting data)
9. Enter the Azure Data Explorer database name
10. Click **Test connection** to ensure you have the right permisisons. 
11. Select **Create**

    ![New Linked Service](./media/quickstart-connect-azure-data-explorer/003-NewLinkService.png)

    > [!NOTE]
    > (Optional) Test connection does not validate write access, ensure your Service Principal Id has write access to the Azure Data Explorer database.

12. Azure Data Explorer clusters and databases are visible under the tab **Linked** in the Azure Data Explorer section. 

    ![Browse Clusters](./media/quickstart-connect-azure-data-explorer/004-BrowseClusters.png)

    > [!NOTE] 
    > In the current release, the database objects are populated based on your AAD account permissions on the Azure Data Explorer databases. When you run the Apache Spark notebooks or orchestration jobs, the credential in the link service will be used (i.e. Service Principal).


## Quickly interact with code-generated actions

* When you right-click into a database or table, you'll have a list of gestures that will trigger a sample Spark notebook for reading data, writing data, streaming data to Azure Data Explorer. 
    ![New Sample Notebooks](./media/quickstart-connect-azure-data-explorer/005-NewNotebook.png)

* Here is an example of reading data. Attached the Notebook to your Spark pool and run the cell
    ![New Read Notebook](./media/quickstart-connect-azure-data-explorer/006-ReadData.png)

   > [!NOTE] 
   > First time execution may take more than three minutes to initiate the Spark session. Subsequent executions will be significantly faster.  


## Limitations
Azure Data Explorer connector is currently not supported with Azure Synapse Managed VNET.


## Next steps

* [Sample code with advanced options](https://github.com/Azure/azure-kusto-spark/blob/master/samples/src/main/python/SynapseSample.py)
* [Azure Data Explorer (Kusto) Spark connector](https://github.com/Azure/azure-kusto-spark)