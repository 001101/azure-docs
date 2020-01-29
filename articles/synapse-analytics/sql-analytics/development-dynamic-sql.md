---
title: Use dynamic SQL in SQL Analytics 
description: Tips for using dynamic SQL in SQL Analytics.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice:
ms.date: 01/06/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: 
---

# Dynamic SQL in SQL Analytics
In this article, you'll find tips for using dynamic SQL and developing solutions using SQL Analytics.

## Dynamic SQL Example

When developing application code, you may need to use dynamic SQL to help deliver flexible, generic, and modular solutions. 

SQL pool doesn't support blob data types. As a result, the size of your strings might be limited since blob data types include both varchar(max) and nvarchar(max) types. If you've used these blob data types in your application code to build large strings, you'll need to break the code into chunks and use the EXEC statement instead.

A simple example:

```sql
DECLARE @sql_fragment1 VARCHAR(8000)=' SELECT name '
,       @sql_fragment2 VARCHAR(8000)=' FROM sys.system_views '
,       @sql_fragment3 VARCHAR(8000)=' WHERE name like ''%table%''';

EXEC( @sql_fragment1 + @sql_fragment2 + @sql_fragment3);
```

If the string is short, you can use [sp_executesql](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-executesql-transact-sql?view=sql-server-ver15) as normal.

> [!NOTE]
> Statements executed as dynamic SQL will still be subject to all T-SQL validation rules.

## Next steps
For more development tips, see [development overview](development-overview.md).

