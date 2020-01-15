---
title: Features - Azure Synapse Analytics
description: List of features that are available in SQL Pool, SQL On-Demand, and Spark Pool.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice:
ms.date: 01/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
---

# Features supported in Synapse capabilities

Azure Synapse Analytic is a big data analytic service that enables you to query and analyze your data using the powerful query languages. You can use the following languages for data analysis:

- Transact-SQL language - standard ANSI-compliant dialect of SQL language used on SQL Server and Azure SQL Database. This language is available in SQL Pool and SQL On-Demand capability.
- Spark SQL, PySpark(Python), Scala(Java), and .NET Spark (C#) languages that are used on Apache Spark. These languages are available on Spark Pool capability.

 The languages used in these capabilities have different language elements and objects. In this page, you can find high-level query language differences between Azure Synapse Capabilities.

## Database objects

Synapse capabilities enable you to use different database objects. The comparison of supported object types across capabilities is shown in the following table:

|   | SQL Pool | SQL On-demand | Spark Pool |
| --- | --- | --- | --- |
| Tables | [Yes](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse) | No, SQL On-Demand capability can query only external data placed on [Azure Storage](#storage-options) | Yes |
| Views | [Yes](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql) | [Yes](https://docs.microsoft.com/sql/t-sql/statements/create-view-transact-sql) | Yes |
| Schemas | [Yes](https://docs.microsoft.com/sql/t-sql/statements/create-schema-transact-sql) | [Yes](https://docs.microsoft.com/sql/t-sql/statements/create-schema-transact-sql) | Spark schema is equivalent with the Spark database |
| Temporary tables | [Yes](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-temporary) | [Yes](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-temporary) | Yes |
| Procedures | [Yes](https://docs.microsoft.com/sql/t-sql/statements/create-procedure-transact-sql) | No |   |
| Functions | [Yes](https://docs.microsoft.com/sql/t-sql/statements/create-function-sql-data-warehouse) | No | [Yes](https://docs.microsoft.com/azure/databricks/spark/latest/spark-sql/udf-python) |
| Triggers | No | No | No |
| External tables | [Yes](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest). See supported [data formats](#data-file-formats). | [Yes](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest) | Yes. See supported [data formats](#data-file-formats). |
| Caching queries | Yes, multiple forms (SSD-based caching, in-memory, resultset caching). In addition, Materialized View are supported | No | Yes, using CACHE TABLE |
| Table variables | [No](https://docs.microsoft.com/sql/t-sql/data-types/table-transact-sql), use temporary tables | [No](https://docs.microsoft.com/sql/t-sql/data-types/table-transact-sql), use temporary tables | N/A, use temporary tables |

## Query language

Query languages used in Synapse capabilities can have different supported features. The following table outlines the most important query language differences in Transact-SQL dialects used in SQL On-demand and SQL Pool and the language used in Spark Pool.

|   | SQL Pool | SQL On-demand | Spark Pool |
| --- | --- | --- | --- |
| SELECT statement | Yes. Transact-SQL query clauses [FOR XML/FOR JSON](https://docs.microsoft.com/sql/t-sql/queries/select-for-clause-transact-sql) and [MATCH](https://docs.microsoft.com/sql/t-sql/queries/match-sql-graph) are not supported. | Yes. Transact-SQL query clauses [FOR XML/FOR JSON](https://docs.microsoft.com/sql/t-sql/queries/select-for-clause-transact-sql) and [MATCH](https://docs.microsoft.com/sql/t-sql/queries/match-sql-graph) are not supported. | Yes |
| INSERT statement | Yes | Limited (temp tables only) |   |
| UPDATE statement | Yes | Limited (temp tables only) |   |
| DELETE statement | Yes | Limited (temp tables only) |   |
| MERGE statement | Yes | Limited (temp tables only) |   |
| Data load | Yes. Preferred utility is [COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) statement, but the system supports both BULK load (BCP) and [CETAS](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=aps-pdw-2016-au7) for data loading. | No |   |
| Data export | Yes. Using [CETAS](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=aps-pdw-2016-au7). | Yes. Using [CETAS](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-as-select-transact-sql?view=aps-pdw-2016-au7). |   |
| Types | Yes, all Transact-SQL types except [cursor](https://msdn.microsoft.com/library/ms190498.aspx), [hierarchyid](https://msdn.microsoft.com/library/bb677290.aspx), [ntext, text, and image](https://msdn.microsoft.com/library/ms187993.aspx), [rowversion](https://msdn.microsoft.com/library/ms182776.aspx), [Spatial Types](https://msdn.microsoft.com/library/ff848797.aspx), [sql\_variant](https://msdn.microsoft.com/library/ms173829.aspx), and [xml](https://msdn.microsoft.com/library/ms187339.aspx) | Yes, all Transact-SQL types except [cursor](https://msdn.microsoft.com/library/ms190498.aspx), [hierarchyid](https://msdn.microsoft.com/library/bb677290.aspx), [ntext, text, and image](https://msdn.microsoft.com/library/ms187993.aspx), [rowversion](https://msdn.microsoft.com/library/ms182776.aspx), [Spatial Types](https://msdn.microsoft.com/library/ff848797.aspx), [sql\_variant](https://msdn.microsoft.com/library/ms173829.aspx), [xml](https://msdn.microsoft.com/library/ms187339.aspx), and Table type | Yes, [Apache Spark types](https://spark.apache.org/docs/latest/sql-reference.html#data-types) |
| Built-in functions (analysis) | Yes, all Transact-SQL [Analytic](https://docs.microsoft.com/sql/t-sql/functions/analytic-functions-transact-sql?view=sql-server-ver15), Conversion, [Date and Time](https://docs.microsoft.com/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=sql-server-ver15), Logical, [Mathematical](https://docs.microsoft.com/sql/t-sql/functions/mathematical-functions-transact-sql?view=sql-server-ver15) functions, except `CHOOSE`, `IIF`, and `PARSE`  | Yes, all Transact-SQL [Analytic](https://docs.microsoft.com/sql/t-sql/functions/analytic-functions-transact-sql?view=sql-server-ver15), Conversion, [Date and Time](https://docs.microsoft.com/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?view=sql-server-ver15), Logical, [Mathematical](https://docs.microsoft.com/sql/t-sql/functions/mathematical-functions-transact-sql?view=sql-server-ver15) functions, except `COMPRESS` and `DECOMPRESS` | Yes |
| Built-in functions (text) | Yes. All Transact-SQL [String](https://docs.microsoft.com/sql/t-sql/functions/string-functions-transact-sql?view=sql-server-ver15), [JSON](https://docs.microsoft.com/sql/t-sql/functions/json-functions-transact-sql?view=sql-server-ver15), and Collation functions, except [STRING_ESCAPE](https://docs.microsoft.com/sql/t-sql/functions/string-escape-transact-sql?view=sql-server-ver15) and TRANSLATE | Yes. All Transact-SQL [string](https://docs.microsoft.com/sql/t-sql/functions/string-functions-transact-sql?view=sql-server-ver15), [JSON](https://docs.microsoft.com/sql/t-sql/functions/json-functions-transact-sql?view=sql-server-ver15), and collation functions, except [STRING_ESCAPE](https://docs.microsoft.com/sql/t-sql/functions/string-escape-transact-sql?view=sql-server-ver15) | Yes |
| Built-in table-value functions | Yes, [Transact-SQL Rowset functions](https://docs.microsoft.com/sql/t-sql/functions/functions?view=sql-server-ver15#rowset-functions), except [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql?view=sql-server-ver15), [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql), [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql), and [OPENROWSET](https://docs.microsoft.com/sql/t-sql/functions/openrowset-transact-sql) | Yes, [Transact-SQL Rowset functions](https://docs.microsoft.com/sql/t-sql/functions/functions?view=sql-server-ver15#rowset-functions), except [OPENXML](https://docs.microsoft.com/sql/t-sql/functions/openxml-transact-sql?view=sql-server-ver15), [OPENDATASOURCE](https://docs.microsoft.com/sql/t-sql/functions/opendatasource-transact-sql), and [OPENQUERY](https://docs.microsoft.com/sql/t-sql/functions/openquery-transact-sql)  | Yes |
| Aggregates |  Transact-SQL built-in aggregates except, except `CHECKSUM_AGG`, `GROUPING`, `GROUPING_ID` | Transact-SQL built-in aggregates except [STRING\_AGG](https://docs.microsoft.com/sql/t-sql/functions/string-agg-transact-sql) | [Yes](https://spark.apache.org/docs/latest/sql-getting-started.html#aggregations) |
| Operators | Yes, all [Transact-SQL operators](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql) except `!>` and `!<` | Yes, all [Transact-SQL operators](https://docs.microsoft.com/sql/t-sql/language-elements/operators-transact-sql)  | Yes |
| Control of flow | Yes. All [Transact-SQL Control-of-flow statement](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow?view=sql-server-ver15) except `CONTINUE`, `GOTO`, `RETURN`, `USE`, and `WAITFOR` | Yes. All [Transact-SQL Control-of-flow statement](https://docs.microsoft.com/sql/t-sql/language-elements/control-of-flow?view=sql-server-ver15) except `RETURN` and SELECT query in `WHILE (...)` condition | Yes |
| DDL statements (CREATE, ALTER, DROP) | Yes. All Transact-SQL DDL statement applicable to the supported object types | Yes. All Transact-SQL DDL statement applicable to the supported object types | Yes. All Apache Spark DDL statement |

**Security**

Azure Synapse Analytic enables you to use built-in security features to secure your data and control access. The following table compares high-level differences between capabilities.

|   | SQL Pool | SQL On-demand | Spark Pool |
| --- | --- | --- | --- |
| Logins | N/A (only contained users are supported in DW databases) | Yes |   |
| Users | Yes. **Note:** only one AAD user can be unrestricted admin | Yes |   |
| Permissions - Object-level | Yes, including ability to GRANT, DENY, and REVOKE permissions to users | Yes, including ability to GRANT, DENY, and REVOKE permissions to users/logins on the system objects that are supported | No, permissions can be granted/denied only on underlying storage |
| Permissions - Schema-level | Yes, including ability to GRANT, DENY, and REVOKE permissions to users/logins on the schema | Yes, including ability to GRANT, DENY, and REVOKE permissions to users/logins on the schema | N/A |
| Roles/groups | Yes | Yes |   |
| AAD passthrough | Yes | Yes | Yes |
| Security &amp; identity functions | Some Transact-SQL security functions and operators:  `CURRENT_USER`, `HAS_DBACCESS`, `IS_MEMBER`, `IS_ROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS`, `OPEN/CLOSE MASTER KEY` | Some Transact-SQL security functions and operators:  `CURRENT_USER`, `HAS_DBACCESS`, `HAS_PERMS_BY_NAME`, `IS_MEMBER', 'IS_ROLEMEMBER`, `IS_SRVROLEMEMBER`, `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, `USER_NAME`, `EXECUTE AS`, and `REVERT`  |   |

SQL Pool and SQL On-Demand use standard Transact-SQL language to query data. For detailed differences, look at the [Transact-SQL language reference](https://docs.microsoft.com/sql/t-sql/language-reference).

Spark Pool uses Apache Spark language. For more details about the Spark language capabilities, look at the [Apache Spark documentation](https://spark.apache.org/docs/latest/sql-getting-started.htm).

## Tools

You can use various tools to connect to synapse capabilities and query data.

|   | SQL Pool | SQL On-demand | Spark Pool |
| --- | --- | --- | --- |
| Synapse Studio | Yes, SQL scripts | Yes, SQL scripts | Yes, Notebooks |
| Power BI | Yes | Yes |   |
| Azure Analysis Service | Yes | Yes |   |
| Azure Data Studio | Yes | Yes, version 1.14 or higher | No |
| SQL Server Management Studio | Yes | Yes, version 18.4 or higher | No |

Most of the applications that query the services using Transact-SQL language can query SQL Pools and SQL On-demand.

Most of the applications that are compatible with Apache Spark can use Spark Pools.

## Storage options

Data that is analyzed can be stored on various storage types. The following table lists all available storage options:

|   | SQL Pool | SQL On-demand | Spark Pool |
| --- | --- | --- | --- |
| Internal storage | Yes | No | No |
| Azure Data Lake v2 | Yes | Yes | Yes |
| Azure Blob Storage | Yes | Yes | Yes |

## Data formats

Data that is analyzed can be stored in various storage formats. The following table lists all available data formats that can be analyzed:

|   | SQL Pool | SQL On-demand | Spark Pool |
| --- | --- | --- | --- |
| Delimited | [Yes](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql) | Yes | [Yes](https://spark.apache.org/docs/latest/sql-data-sources-load-save-functions.html) |
| CSV | Yes (multi-character delimiters not supported) | Yes | [Yes](https://spark.apache.org/docs/latest/sql-data-sources-load-save-functions.html) |
| Parquet | [Yes](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql) | Yes | [Yes](https://spark.apache.org/docs/latest/sql-data-sources-parquet.html) |
| Hive ORC | [Yes](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql) | No | [Yes](https://spark.apache.org/docs/latest/sql-data-sources-orc.html) |
| Hive RC | [Yes](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql) | No |   |
| AVRO | No | No | [Yes](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) |
| JSON | Yes | Yes | [Yes](https://spark.apache.org/docs/latest/sql-data-sources-json.html) |
| [Delta-lake](https://delta.io/) | No | No | Yes |
| [CDM](https://docs.microsoft.com/common-data-model/) | No | No | No |

## Table replication

Synapse workspace provides unified view over data and enables you to query the same data set using different capabilities. Whenever you create a table on external files in one Synapse capability, the table definition will be copied to other capabilities.

Currently, only databases and external tables created in Spark Pool that reference CSV and Parquet formats are replicated to SQL On-Demand.
