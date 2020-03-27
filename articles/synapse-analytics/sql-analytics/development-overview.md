---
title: Resources for developing SQL Analytics features
description: Development concepts, design decisions, recommendations, and coding techniques for SQL Analytics.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice:
ms.date: 10/20/2019
ms.author: fipopovi
ms.reviewer: jrasnick
---

# Design decisions and coding techniques for SQL Analytics features in Azure Synapse Analytics
In this article, you'll find a list of resources for SQL pool and SQL on-demand (preview) functions of SQL Analytics. The recommended articles are split up into two sections: Key design decisions and Development and coding techniques.

The goal of these articles is to help you develop the optimal technical approach for the SQL Analytics components within Synapse Analytics.

## Key design decisions
The articles below highlight concepts and design decisions for SQL Analytics development:

|                                                          |   SQL pool   | SQL on-demand |
| -----------------------------------------------------    | ---- | ---- |
| [Connections](connect-overview.md)                    | Yes | Yes |
| [Resource classes and concurrency](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Yes    | No |
| [Transactions](development-transactions.md)              | Yes | No |
| [User-defined schemas](development-user-defined-schemas.md) | Yes | Yes |
| [Table distribution](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Yes | No |
| [Table indexes](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Yes | No |
| [Table partitions](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Yes | No |
| [Statistics](development-tables-statistics.md)            | Yes | Yes |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Yes | No |
| [External tables](development-tables-external-tables.md) | Yes | Yes |
| [CETAS](development-tables-cetas.md)                     | Yes | Yes |


## Recommendations

Below you'll find essential articles that emphasize specific coding techniques, tips, and recommendations for development:

|                                            | SQL pool | SQL on-demand |
| ------------------------------------------ | ------------------ | ----------------------- |
| [Stored procedures](development-stored-procedures.md)  | Yes                | No                      |
| [Labels](development-label.md)                           | Yes                | No                      |
| [Views](development-views.md)                             | Yes                | Yes                     |
| [Temporary tables](development-tables-temporary.md)       | Yes                | Yes                     |
| [Dynamic SQL](development-dynamic-sql.md)                 | Yes                | Yes                     |
| [Looping](development-loops.md)                         | Yes                | Yes                     |
| [Group by options](development-group-by-options.md)       | Yes                | No                      |
| [Variable assignment](development-variable-assignment.md) | Yes                | Yes                     |

## Next steps
For more reference information, see [SQL pool T-SQL statements](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

