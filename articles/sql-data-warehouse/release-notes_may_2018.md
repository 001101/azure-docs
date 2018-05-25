---
title: Azure SQL Data Warehouse Release Nodes May 2018 | Microsoft Docs
description: Release notes for Azure SQL Data Warehouse.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 05/25/2018
ms.author: twounder
ms.reviewer: twounder
---

# Release Notes May 2018
The following new features, enhancements, and changes have been introduced this month.

## Gen 2 Instances

## GDPR Compliance
## ALTER VIEW
[ALTER VIEW](https://docs.microsoft.com/sql/t-sql/statements/alter-view-transact-sql) allows a user to modify a previously created view withouth having to DELETE/CREATE the view and reapply permissions. 

### Examples
The following example modifies a previously created view.
```sql
ALTER VIEW test_view AS SELECT 1 [data];
```

## CONCAT_WS
[The CONCAT_WS()](https://docs.microsoft.com/sql/t-sql/functions/concat-ws-transact-sql) function resturns a string resulting from the concatenation of two or more values in an end-to-end manner. It separates the concatenated values with the delimiter specified in the first argument. The `CONCAT_WS` function is useful for generating Comma Separated Value (CSV) output.

### Examples
The following example shows concatenating a set of int values with a comma.
```sql
SELECT CONCAT_WS(',', 1, 2, 3);
```
The following example shows concatenating a set of mixed data type values with a comma.
```sql
SELECT CONCAT_WS(',', 1, 2, 'String', GETDATE())
```

## Rejected Row Support

## SP_DATATYPE_INFO
The [sp_datatype_info](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-datatype-info-transact-sql) system stored procedure returns information about the data types supported by the current environment. It is commonly used by tools connecting through ODBC connections for data type investigation.

### Examples
The following example retrieves details for all data types supported by SQL Data Warehouse.

```sql
EXEC sp_datatype_info
```
