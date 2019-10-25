---
title: Querying parquet nested types 
description: In this section, we will show how to query Parquet files.
services: sql-data-warehouse
author: azaricstefan 
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: design
ms.date: 10/07/2019
ms.author: v-stazar
ms.reviewer: jrasnick
---

# Quickstart: Querying parquet nested types 

Reading this article you will learn how to write a query in SQL Analytics on-demand that will read Parquet nested types.

## Prerequisites

Before reading rest of the article, make sure to check following articles:
- [First time setup](query-data-in-storage.md#first-time-setup)
- [Prerequisites](query-data-in-storage.md#prerequisites)


## Projecting nested and/or repeated data

Following query reads file *justSimpleArray.parquet* file and projects all columns from Parquet file including nested and/or repeated data.

```mssql
SELECT 
	*
FROM  
	OPENROWSET(
		BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet', 
		FORMAT='PARQUET'
	) AS [r]
```



## Accessing elements from nested columns

Following query reads file *structExample.parquet* file and shows how to surface elements of nested column:

```mssql
SELECT 
	*
FROM  
	OPENROWSET(
		BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/structExample.parquet', 
		FORMAT='PARQUET'
	) 
	WITH (
		-- you can see original nested columns values by uncommenting lines below
		--DateStruct VARCHAR(8000),
		[DateStruct.Date] DATE,
		--TimeStruct VARCHAR(8000),
		[TimeStruct.Time] TIME,
		--TimestampStruct VARCHAR(8000),
		[TimestampStruct.Timestamp] DATETIME2,
		--DecimalStruct VARCHAR(8000),
		[DecimalStruct.Decimal] DECIMAL(18, 5),
		--FloatStruct VARCHAR(8000),
		[FloatStruct.Float] FLOAT
	) AS [r]
```



## Accessing elements from repeated columns

Following query reads file *justSimpleArray.parquet* file and utilizes [JSON_VALUE](https://docs.microsoft.com/en-us/sql/t-sql/functions/json-value-transact-sql?view=sql-server-2017) to retrieve **scalar** element from within repeated column (e.g. Array or Map):

```mssql
SELECT 
	*, 
	JSON_VALUE(SimpleArray, '$[0]') AS FirstElement,
	JSON_VALUE(SimpleArray, '$[1]') AS SecondElement,
	JSON_VALUE(SimpleArray, '$[2]') AS ThirdElement
FROM  
	OPENROWSET(
		BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/justSimpleArray.parquet', 
		FORMAT='PARQUET'
	) AS [r]
```



Following query reads file *mapExample.parquet* file and utilizes [JSON_QUERY](https://docs.microsoft.com/en-us/sql/t-sql/functions/json-query-transact-sql?view=sql-server-2017) to retrieve **non-scalar** element from within repeated column (e.g. Array or Map):

```mssql
SELECT 
	MapOfPersons, 
	JSON_QUERY(MapOfPersons, '$."John Doe"') AS [John]
FROM  
	OPENROWSET(
		BULK 'https://sqlondemandstorage.blob.core.windows.net/parquet/nested/mapExample.parquet', 
		FORMAT='PARQUET'
	) AS [r]
```

## Next steps

Advance to one of the following articles and learn how to query JSON files.
> [!div class="nextstepaction"]
> [Querying JSON files](querying-json-files.md)
