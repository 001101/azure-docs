---
title: "Azure Data Studio: Connect and query SQL Analytics" 
description: Use Azure Data Studio to connect to and query SQL Analytics in Azure Synapse Analytics. 
services: synapse analytics
author: azaricstefan 
ms.service: synapse-analytics
ms.topic: overview 
ms.subservice: 
ms.date: 10/22/2019 
ms.author: v-stazar 
ms.reviewer: jrasnick
---

# Connect to SQL Analytics with Azure Data Studio

> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

You can use [Azure Data Studio](get-started-azure-data-studio.md) to connect to and query SQL Analytics in Azure Synapse Analytics. 

## Connect 

To connect to SQL Analytics, open Azure Data Studio and select **New Connection**.

![Open Azure Data Studio](media/sql-analytics-query-ads/1-start.png)

Choose **Microsoft SQL Server** as the **Connection type**. 

The connection requires the following parameters:

* **Server:** Server in the form `<`Server Name`>`.database.windows.net
* **Database:** Database name

Choose **Windows Authentication**, **Azure Active Directory**, or **SQL Login** as the **Authentication Type**. 

To use **SQL Login** as the authentication type, add the username/password parameters:

* **User:** Server user in the form `<`User`>`
* **Password:** Password associated with the user

This screenshot shows the **Connection Details** for **Windows Authentication**:

![Windows Authentication](media/sql-analytics-query-ads/3-windows-auth.png)

This screenshot shows the **Connection Details** using **SQL Login**: 

![SQL Login](media/sql-analytics-query-ads/2-database-details.png)


## Query

Once connected, you can query SQL Analytics using supported [Transact-SQL (T-SQL)](/sql/t-sql/language-reference) statements against the instance. Select **New query** from the dashboard view to get started.

![New Query](media/sql-analytics-query-ads/5-new-query.png)

For example, you can use this Transact-SQL statement to [query Parquet files](query-parquet-files.md): 

```sql
SELECT COUNT(*) 
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet', 
    FORMAT='PARQUET'
)
```

## Next steps 
Explore other ways to connect to SQL Analytics: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md)
- [sqlcmd](get-started-connect-sqlcmd.md)


 
