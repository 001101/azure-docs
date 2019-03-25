---
 title: include file
 description: include file
 services: cosmos-db
 author: SnehaGunda
 ms.service: cosmos-db
 ms.topic: include
 ms.date: 03/21/2019
 ms.author: sngun
 ms.custom: include file
---
You can use queries in Data Explorer to retrieve and filter your data.

At the left of the **Documents** tab in Data Explorer, the default query is set to `SELECT * FROM c`. This query retrieves and displays all documents in the collection in ID order. 

    ![Default query in Data Explorer is `SELECT * FROM c`](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png)

To change the query, select **Edit Filter**, replace the default query with `ORDER BY c._ts DESC`, and then select **Apply Filter**.

    ![Change the default query by adding ORDER BY c._ts DESC and clicking Apply Filter](./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png)

The modified query displays the documents in descending order based on their time stamp, so now your second document is listed first. 

If you're familiar with SQL syntax, you can enter any supported [SQL queries](../articles/cosmos-db/sql-api-sql-query.md) in the query predicate box. You can also use Data Explorer to create stored procedures, UDFs, and triggers for server-side business logic. 

Data Explorer provides easy Azure portal access to all of the built-in programmatic data access features available in the APIs. You also use the portal to scale throughput, get keys and connection strings, and review metrics and SLAs for your Cosmos DB account. 

