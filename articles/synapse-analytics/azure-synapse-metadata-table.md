---
title: Azure Synapse Analytics' shared Meta Data Tables 
description: Azure Synapse Analytics provides a shared meta data model where creating a table in Spark will make it accessible from its SQL Analytics and SQL Pool engines without duplicating the data. 
services: sql-data-warehouse 
author: MikeRys 
ms.service: sql-data-warehouse 
ms.topic: overview 
ms.subservice: 
ms.date: 10/23/2019 
ms.author: mrys 
ms.reviewer: jrasnick
---

# Azure Synapse Analytics' shared Meta Data Tables


Azure Synapse Analytics allows the different computational engines of a workspace to share databases and tables between its Apache Spark Pools, SQL Analytics On-Demand engine and SQL Pools. 

In the current public preview, the sharing is designed to support the so-called modern data warehouse pattern and gives the workspace's SQL engines access to databases and tables created with Spark while also allowing the SQL engines to create their own objects that are not being shared with the other engines.

## Supporting the Modern Data Warehouse Pattern with Shared Meta Data

The shared meta data model supports the modern data warehouse pattern as shown in Figure 1 below:

1. Data from the data lake is being prepared and structured efficiently with Spark by storing the prepared data in (possibly partitioned) tables contained in a database.

2. The Spark created databases and their tables will become visible in any of the Synapse workspace's Spark Pool instances and can be used from any of the Spark jobs, subject to the [permissions](#security-model-at-a-glance). 

3. The Spark created databases and their tables will also become visible in the SQL Analytics On-Demand engine. Databases [ADD LINK] are being created automatically in the SQL Analytics On-Demand meta data, and both the external and managed tables [ADD LINK] created by a Spark job will be made accessible as external tables in the SQL Analytics On-Demand meta data in the `dbo` schema of the corresponding database. For more details, see [ADD LINK].

4. If there are SQL Analytics Pools in the workspace that have their meta data synchronization enabled [ADD LINK] or if a new SQL Pool is being created with the meta data synchronization enabled, the Spark created databases and their tables will be mapped automatically into the SQL Pool's database as follows: The databases generated in Spark are mapped to special schemas inside the SQL Pool's database. Each such schema is named after the Spark database name with an additional `$` prefix. Both the external and managed tables in the Spark generated database are exposed as external tables in the corresponding special schema. For more details, see [ADD LINK].

[INSERT PICTURE]

__Figure 1 -__ Supporting the Modern Data Warehouse Pattern with Shared Meta Data

These shared meta data objects can now be queried (but not updated or changed) by the SQL engines that have access to them. 

## Security model at a glance

In the current public preview, the Spark databases and tables, as well as their synchronized representations in the SQL engines will be secured at the underlying storage level. When the table is being queried by any of the engines that the query submitter has the right to use, the query submitter's security principal is being passed through, down to the underlying files, and permissions are checked at the file system level.

For more details, see [ADD LINK].

## Change maintenance

Change maintenance.

<!---Required:
The introductory paragraph helps customers quickly determine whether an article is relevant.
Describe in customer-friendly terms what the service is and does, and why the customer should care. Keep it short for the intro.
You can go into more detail later in the article. Many services add artwork or videos below the introduction.
--->

<!---Avoid notes, tips, and important boxes. Readers tend to skip over them. Better to put that info directly into the article text.--->

<!---Screenshots and videos can add another way to show and tell the overview story. But don’t overdo them. Make sure that they offer value for the overview.
If users access your product/service via a web browser, the first screenshot should always include the full browser window in Chrome or Safari. This is to show users that the portal is browser-based - OS and browser agnostic.


--->

## <article body>
<!---
After the intro, you can develop your overview by discussing the features that answer the "Why should I care" question with a bit more depth.
Be sure to call out any basic requirements and dependencies, as well as limitations or overhead.
Don't catalog every feature, and some may only need to be mentioned as available, without any discussion.
--->

## <Top task>
<!---Suggested:
An effective way to structure you overview article is to create an H2 for the top customer tasks identified in milestone one of the [APEX content model](contribute-get-started-mvc.md) and describe how the product/service helps customers with that task.
Create a new H2 for each task you list.


--->

## Next steps

<!---Some context for the following links goes here--->
<!--- [link to next logical step for the customer](quickstart-view-occupancy.md)--->

<!--- Required:
In Overview articles, provide at least one next step and no more than three.
Next steps in overview articles will often link to a quickstart.
Use regular links; do not use a blue box link. What you link to will depend on what is really a next step for the customer.
Do not use a "More info section" or a "Resources section" or a "See also section".


--->
