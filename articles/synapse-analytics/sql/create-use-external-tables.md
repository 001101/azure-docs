---
title: Create and use external tables in SQL on-demand (preview)
description: In this section, you'll learn how to create and use external tables in SQL on-demand (preview). External tables are useful when you want to control access to external data in SQL On-demand and if you want to use tools, such as Power BI, in conjunction with SQL on-demand.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice:
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick, carlrab
---

# Create and use external tables in SQL on-demand (preview) using Azure Synapse Analytics

In this section, you'll learn how to create and use external tables in SQL on-demand (preview). External tables are useful when you want to control access to external data in SQL On-demand and if you want to use tools, such as Power BI, in conjunction with SQL on-demand. External tables can access two types of storage:
- Public storage where user access public storage files
- Protected storage where user access storage files using SAS credential, Azure AD identity, or Managed Identity of Synapse workspace.

## Prerequisites

Your first step is to create database where the tables will be created and initialize the objects by executing [setup script](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) on that database. All queries in this article will be executed on your sample database

## Create an external table

You can create external tables the same way you create regular SQL Server external tables. The query below creates an external table that reads *population.csv* file.

> [!NOTE]
> Change the first line in the query, i.e., [mydbname], so you're using the database you created. If you have not created a database, please read [First-time setup](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

CREATE EXTERNAL DATA SOURCE [CsvDataSource] WITH (
    LOCATION = 'https://sqlondemandstorage.blob.core.windows.net/csv'
    , CREDENTIAL = sqlondemand -- creadential created in setup script
);
GO

CREATE EXTERNAL FILE FORMAT CSVFileFormat
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT,
    FORMAT_OPTIONS (
        FIELD_TERMINATOR = ',',
        STRING_DELIMITER = '"',
        FIRST_ROW = 2
    )
);
GO

CREATE EXTERNAL TABLE populationExternalTable
(
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
    [country_name] VARCHAR (100) COLLATE Latin1_General_BIN2,
    [year] smallint,
    [population] bigint
)
WITH (
    LOCATION = 'population/population.csv',
    DATA_SOURCE = CsvDataSource,
    FILE_FORMAT = CSVFileFormat
);
GO
```

## Create an external table on public storage

You can create external tables that reads data from the files placed on publicly available Azure storage. [Setup script](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql) will create public external data source and Parquet file format definition that is used in the following query:

```sql
CREATE EXTERNAL TABLE Taxi (
     vendor_id VARCHAR(100) COLLATE Latin1_General_BIN2, 
     pickup_datetime DATETIME2, 
     dropoff_datetime DATETIME2
) WITH ( LOCATION = 'puYear=*/puMonth=*/*.parquet',
         DATA_SOURCE = YellowTaxi,
         FILE_FORMAT = ParquetFormat );
```
## Use a external table

You can use external tables in your queries the same way you use them in SQL Server queries.

The following query demonstrates using the *population* external table we created in [Create an external table](#create-an-external-table) section. It returns country names with their population in 2019 in descending order.

> [!NOTE]
> Change the first line in the query, i.e., [mydbname], so you're using the database you created. If you have not created a database, please read [First-time setup](query-data-storage.md#first-time-setup).

```sql
USE [mydbname];
GO

SELECT
    country_name, population
FROM populationExternalTable
WHERE
    [year] = 2019
ORDER BY
    [population] DESC;
```

## Next steps

For information on how to store results of a query to the storage refer to the [Store query results to the storage](../sql/create-external-table-as-select.md).
