---
title: Data normalization in Azure Sentinel | Microsoft Docs
description: This article explains how Azure Sentinel normalizes data from many different sources and details the normalization schema.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin

ms.assetid:
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: yelevin

---

# Normalization in Azure Sentinel

## What is normalization

Working with various data types and tables together presents challenges. You must become familiar with  many different data types and schemas, having to write and use a unique set of analytics rules, workbooks, and hunting queries for each, even for those that share commonalities (e.g., all pertain to firewall devices).  Correlation between the different data types, necessary for investigation and hunting, is also difficult. Azure Sentinel provides a seamless experience for handling data from various sources in uniform, normalized views.

The Sentinel **common information model** consist of 3 aspects:

- **Normalized schemas** cover common sets of predictable event types (tables) that are easy to work with and to build unified capabilities on (for example, networking table). The schema also includes a normalized columns convention, and definitions for value and format standardization (standard consistent representation of data such as IP addresses).

- **Parsers** map existing data of different types to the normalized schema. According to the model, data can be parsed to the normalized schema in query time (using functions) or ingestion time. At this time only query-time parsing is supported.

- **Content for each normalized schema** includes analytics rules, interactive workbooks, hunting queries (?), and additional content.

### Current release

With this release, the normalized network connections and sessions schema (v1.0.0) is available for public preview, which describes network connections or sessions such as those logged by firewalls, Wire Data, NSG, Netflow, proxy systems and web security gateways. The schema is available along with selected query-time parsers and analytics rules.

The schema is currently available only via query-time parsers (see parsers section). 
*In the future we’ll also offer that as built-in native Log Analytics table, where data can be parsed to in ingestion time.*

You can parse data to additional representations and use the [OSSEM entities naming model]() to create columns that will be consistent with existing and future normalized tables.

## Normalized schema and parsing

### How our normalized schemas are defined

Sentinel is aligning with the [Open Source Security Events Metadata (OSSEM)](https://ossemproject.com/intro.html) common information model, allowing for predictable entities’ correlation across normalized tables. OSSEM is a community-led project that focuses primarily on the documentation and standardization of security event logs from diverse data sources and operating systems. In addition, the project provides a common information model (CIM) that can be used for data engineers during data normalization procedures to allow security analysts to query and analyze data across diverse data sources.

The [OSSEM CIM](https://ossemproject.com/cdm/intro.html) defines a set of entities (for example: file, host, IP, process), and defines a set of attributes for each such entity. In addition the CIM defines a set of tables (for example, [network session](https://ossemproject.com/cdm/tables/network_session.html) table) that uses relevant attributes from these entities, allowing for seamless and predictable correlation. Note that entities can be nested (for example, Source entity can contain a File entity that will have a name attribute).

To learn more about OSSEM entity structure, visit the [official OSSEM reference](https://ossemproject.com/cdm/guidelines/entity_structure.html).

### How the normalized schemas are implemented in Sentinel

In the implementation of the OSSEM CIM in Sentinel, we are projecting the OSSEM representation to a Log Analytics tabular representation, whether this representation is a built-in table or was created using query-time parsers or functions that map existing data to this representation. For the tabular representation, we are chaining OSSEM entity names and attribute names to a column name. For example, a Source entity containing a File entity containing a Hash entity containing an md5 attribute, will be implemented as the following Log Analytics column: SrcFileHashMd5 (note – OSSEM uses default casing of *snake_case*, while in Sentinel and Log Analytics *PascalCase* is used)

Note that additional columns may exist in the Sentinel implementation due to Log Analytics platform requirements and use cases that are specific to Sentinel customers.

### Schema reference

Please see the [schema reference document](./normalization-schema.md), as well the official [OSSEM project documentation](https://ossemproject.com/cdm/intro.html) to learn more.

Note that the schema reference also includes value and format standardization. The source fields, original or parsed, might not be in a standard format or use the schema’s standard list of values, and would therefore need to be converted to the schema standard representation. 

## Parsers

### What is parsing

With a base set of defined normalized tables available, you will need to transform (parse/map) your data into those tables. That is, you will extract specific data from their raw form into well-known columns. Parsing in Azure Sentinel happens at **query time** - parsers are built as Log Analytics user functions (using Kusto Query Language - KQL) that transform data in existing tables (e.g. CommonSecurityLog, custom logs tables, syslog) into the normalized tables schema.

The other kind of parsing, not yet supported in Azure Sentinel, is at **ingestion time** - allowing to collect data directly into the normalized table(s) as it is ingested from its data sources. Ingestion time parsing provides improved performance as the data model is queried directly without the need to use functions.

### Using query time parsers

#### Installing a parser

The available query time parsers are available in the Azure Sentinel official GitHub repository. Each parser is versioned to allow customers to use and monitor for future updates easily. To install a parser:

1. Copy the relevant parser content from each relevant KQL file in the above Github link to your clipboard

1. In the Azure Sentinel portal, open the Logs page, and paste the content of the KQL file to the logs screen, and click **Save**.

    :::image type="content" source="./media/normalization/install-new-parser.png" alt-text="Install a new parser":::

1. In the save dialog, populate the fields as follows:
    1. **Name**: use the **Alias name** field in the description of the parser (in the example above, *CheckPoint_Network_NormalizedParser*)
    
    1. **Save as**: select **Function**

    1. **Function Alias**: should be the same as the **Name** field

    1. **Category**: you can select an existing category or create new category (e.g. *NormalizedNetworkSessionsParsers*)
    
        :::image type="content" source="./media/normalization/save-new-parser.png" alt-text="Save the parser":::

In order to properly use the parsers, you must also install the Empty network schema parser (which creates an empty tabular view of all the network sessions schema’ fields) and the Network meta-parser (the meta parser `unions together all enabled parsers to create a single view of data from various sources in the networking schema). Installing these two  parsers is done in a similar manner to the aforementioned steps.

#### Using the parsers

Once enabled, you can use the meta-parser to query a unified view on all currently enabled parsers. To do so, go to the Sentinel logs page and query the meta parser:

:::image type="content" source="./media/normalization/query-parser.png" alt-text="Query the parser":::
 
You can also access the meta-parser or the individual parsers using query explorer in the Sentinel logs page, by clicking on ‘Query explorer’:

:::image type="content" source="./media/normalization/query-explorer.png" alt-text="Query explorer":::

On the pane on the right, Expand “Saved queries” section and find the ‘NormalizedNetworkParsers’ folder (or the category name you’ve chosen when creating the parsers):

:::image type="content" source="./media/normalization/find-parser.png" alt-text="Find your parser":::

You can click on each individual parser and see the underlying function it uses, and run it (or access it directly by its alias, as described above).

#### Customizing parsers

You can repeat the above steps (finding the parser in query explorer), click on the relevant parser and see its function implementation.
For example, you can decide to edit the meta parser to add/remove individual parsers.

:::image type="content" source="./media/normalization/customize-parser.png" alt-text="Customize your parser":::
 
Once the function is altered, click “Save” again and use the same name, alias and category. An override dialog will be opened – press “OK”:

:::image type="content" source="./media/normalization/are-you-sure.png" alt-text="Are you sure":::

#### Automating parser installation

Work in progress – not sure if will land in time for Ignite

#### Optimizing parsers with parametrized functions

Work in progress – not sure if will land in time for Ignite

#### Additional information

To learn more about saved queries (the query time parsers implementation) in Log Analytics, visit:
https://docs.microsoft.com/azure/azure-monitor/log-query/saved-queries

## Next steps

In this document, you learned about Azure Sentinel's normalization schema. For the reference schema itself, see [Azure Sentinel data normalization schema reference](./normalization-schema.md).

* [Azure Sentinel Blog](https://aka.ms/azuresentinelblog). Find blog posts about Azure security and compliance.
