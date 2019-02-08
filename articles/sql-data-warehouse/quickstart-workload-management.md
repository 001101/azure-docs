---
title: 'Quickstart: Create a workload classifier'
description: Create a workload classifier with high importance
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/07/2019
ms.author: rortloff
ms.reviewer: jrasnick
---

# Quickstart: Create a workload classifier 

Quickly create a workload classifier with high importance for the CEO of your organization. This will allow CEO queries to take precedence over other queries in the queue.

If you don't have an Azure subscription, create a [free](https://azure.microsoft.com/free/) account before you begin.

> [!NOTE]
> Creating a SQL Data Warehouse may result in a new billable service.  For more information, see [SQL Data Warehouse pricing](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## Before you begin

This quickstart assumes you already have a SQL data warehouse and that you have CONTRO DATABASE permissions. If you need to create one, use [Create and Connect - portal](create-data-warehouse-portal.md) to create a data warehouse called **mySampleDataWarehouse**.

## Sign in to the Azure portal

Sign in to the [Azure portal](https://portal.azure.com/).

## Create user 

## run queries to show CEO waiting

## Create the workload classifier

## run queries again to show CEO not waiting

##  


## Clean up resources

## drop workload classifier
## Drop user

You are being charged for data warehouse units and data stored your data warehouse. These compute and storage resources are billed separately. 

- If you want to keep the data in storage, you can pause compute when you aren't using the data warehouse. By pausing compute, you are only charged for data storage. You can resume compute whenever you are ready to work with the data.
- If you want to remove future charges, you can delete the data warehouse. 

Follow these steps to clean up resources as you desire.

1. Sign in to the [Azure portal](https://portal.azure.com), click on your data warehouse.

    ![Clean up resources](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

1. To pause compute, click the **Pause** button. When the data warehouse is paused, you see a **Start** button.  To resume compute, click **Start**.

2. To remove the data warehouse so you are not charged for compute or storage, click **Delete**.

3. To remove the SQL server you created, click **mynewserver-20180430.database.windows.net** in the previous image, and then click **Delete**.  Be careful with this deletion, since deleting the server also deletes all databases assigned to the server.

4. To remove the resource group, click **myResourceGroup**, and then click **Delete resource group**.


## Next steps
You have now created a data warehouse, created a firewall rule, connected to your data warehouse, and run a few queries. To learn more about Azure SQL Data Warehouse, continue to the tutorial for loading data.
> [!div class="nextstepaction"]
>[Load data into a SQL data warehouse](load-data-from-azure-blob-storage-using-polybase.md)
