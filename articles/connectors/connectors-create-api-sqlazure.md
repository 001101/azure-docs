---
title: Connect to SQL Server or Azure SQL Database - Azure Logic Apps | Microsoft Docs
description: Create connections to SQL Server and Azure SQL Database with Azure Logic Apps
services: logic-apps
documentationcenter: 
author: ecfan
manager: cfowler
editor: 
tags: connectors

ms.assetid: d8a319d0-e4df-40cf-88f0-29a6158c898c
ms.service: logic-apps
ms.workload: logic-apps
ms.devlang: 
ms.tgt_pltfrm: 
ms.topic: article
ms.date: 05/07/2018
ms.author: estfan; LADocs
---

# Connect to SQL Server or Azure SQL Database with Azure Logic Apps

To automate workflows that manage data for SQL Server or 
Azure SQL Database, you can build logic apps that orchestrate 
tasks and processes by using the SQL Server connector. 
This article shows you how to connect your logic 
app to SQL Server or Azure SQL Database. 

Here are some example tasks that you can automate and 
orchestrate with logic apps and SQL Server or Azure SQL Database:

* Get, insert, or delete rows of data. 
* Add customers to a customer database, 
or update orders in an order database. 
* When a new item gets added to a database, 
send a confirmation email.

So, for example, you can create a logic app that 
automatically inserts a row in an Azure SQL Database 
when a new record is created in Dynamics CRM Online. 
If you're new to logic apps, see 
[What is Azure Logic Apps](../logic-apps/logic-apps-overview.md) 
and [Quickstart: Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

## Prerequisites

* If you don't have an Azure subscription, 
<a href="https://azure.microsoft.com/free/" target="_blank">sign up for a free Azure account</a>.

* The logic app where you want to connect to your SQL database. 
If you want to start your logic app with a SQL trigger, 
you need a blank logic app. 

* If you don't have an Azure SQL Database or SQL Server, 
see [Create an Azure SQL Database](../sql-database/sql-database-get-started-portal.md) 
or [Create a database - SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database). 
If you create an Azure SQL Database, 
you can also create the sample databases that are included with SQL. 

  After you have your SQL database set up, you'll need your server name, 
  database name, user name, and password.

  * For Azure SQL Database, the connection string has this information:

    "Server=tcp:<*yourServerName*>.database.windows.net,1433;Initial Catalog=<*yourDatabaseName*>;Persist Security Info=False;User ID=<*yourUserName*>;Password=<*yourPassword*>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"

  * For SQL Server, the connection string has this information: 

    "Server=<*yourServerAddress*>;Database=<*yourDatabaseName*>;User Id=<*yourUserName*>;Password=<*yourPassword*>;"

  > [!NOTE]
  > Before you start running your logic app, 
  > make sure that your database tables contain data. 
  > Otherwise, Logic Apps won't return any results 
  > from empty tables, including any schema values, 
  > when you perform operations, such as "Get rows".

* Before you can connect to on-premises systems, such as SQL Server, 
with logic apps, you must [install and set up the on-premises data gateway](../logic-apps/logic-apps-gateway-connection.md). You need the gateway 
information for creating the SQL database connection in your logic app.

<a name="create-connection"></a>

## Connect to SQL database

For your logic app to access any service, you must create 
a *connection* from your logic app to that service. 
This step happens when you go to add a SQL Server or 
Azure SQL Database operation to your logic app. 
At that time, you're prompted to create the connection 
and provide your credentials for accessing the service. 
The Logic Apps Designer provides an easy way for you to 
create this connection directly from your logic app. 

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

<a name="add-sql-trigger"></a>

## Add SQL trigger

In Azure Logic Apps, every logic app must start with a [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), 
which fires when a specific event happens or 
when a specific condition is met. Each time the trigger fires, 
the Logic Apps engine creates a logic app instance that 
starts and runs your workflow.

1. In the Azure portal, create a blank logic app, 
which opens Logic Apps Designer. 

2. In the search box, enter "sql server" as your filter. 
From the triggers list, select the SQL trigger that you want. 
This example uses this trigger: **SQL Server - When an item is created**

3. If you're prompted for connection information, 
   then [enter your connection details](#create-connection). 

   Otherwise, if your connection already exists, 
   select the **Table name** that you want from the list.

4. Now set up the **Interval** and **Frequency** so 
that you can specify how often you want your logic app 
to check your SQL database.

<a name="add-sql-action"></a>

## Add SQL action

In Azure Logic Apps, an [action](../logic-apps/logic-apps-overview.md#logic-app-concepts) 
is a step in your workflow that follows the trigger or another action. 
For this example, the logic app starts with a [Recurrence trigger](../connectors/connectors-native-recurrence.md).

1. On the Logic App Designer, under the trigger, 
choose **New step** > **Add an action**.

   ![Choose "New step", "Add an action"](./media/connectors-create-api-sqlazure/add-action.png)

2. In the search box, enter "sql server" as your filter. 
From the actions list, select any SQL action that you want. 
This example uses this action: **SQL Server - Get row**

   ![Enter "sql server", select "SQL Server - Get row"](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. If you're prompted for connection information, 
   then [enter your connection details](#create-connection). 
   Or, if your connection already exists, 
   select the **Table name** that you want from the list, 
   and enter the **Row ID** for the row that you want.

   ![Enter the table name and row ID](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   This example returns a row from a table, 
   but to view the data in this row, 
   add another action that creates a file 
   with includes the fields from the table. 
   
   For example, you might create a file in a cloud storage account, 
   by adding a OneDrive action that uses the "FirstName" and "LastName" fields. 

4. On the designer toolbar, choose **Save**, 
which also automatically enables and makes your logic app live in Azure. 

## Process data in bulk

When you want to work with multiple records, 
you can iterate over items by using [until loops](../logic-apps/logic-apps-control-flow-loops.md#until-loop) 
in your logic app. However, [loops have their limits](../logic-apps/logic-apps-limits-and-config.md). 
Sometimes you might need to work with very large record sets, 
for example, thousands or millions of rows, but make as few database calls as possible. 
You can divide those records into smaller sets by using *pagination*. 
This technique helps you better control the resulting output and 
provide a cleaner structure for your result set. 

To implement this solution, create a stored procedure in your SQL instance, 
and then reference that procedure by using the SQL Server 
connector's **Execute stored procedure** action inside an until loop.

For solution details, see these articles:

* <a href="https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx" target="_blank">SQL Pagination for bulk data transfer with Logic Apps</a>

* [SELECT - ORDER BY Clause](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql) 

## Connector-specific details

For more information about the triggers, actions, 
and limits defined by the connector's Swagger, 
see the [connector's details](/connectors/sql/). 

## Next steps

* Learn about other [Logic Apps connectors](../connectors/apis-list.md).

