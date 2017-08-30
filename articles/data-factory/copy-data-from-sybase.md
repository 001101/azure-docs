---
title: Copy data from Sybase using Azure Data Factory | Microsoft Docs
description: Learn how to copy data from Sybase to supported sink data stores by using a copy activity in an Azure Data Factory pipeline.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: jhubbard
editor: spelluru

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: jingwang

---
# Copy data from Sybase using Azure Data Factory

This article outlines how to use the Copy Activity in Azure Data Factory to copy data from a Sybase database. It builds on the [copy activity overview](copy-activity-overview.md) article that presents a general overview of copy activity.

## Supported scenarios

You can copy data from Sybase database to any supported sink data store. For a list of data stores supported as sources/sinks by the copy activity, see the [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats) table.

Specifically, this Sybase connector supports:

- Sybase **version 16 and above**.
- Copying data using **Basic** or **Windows** authentication.

## Prerequisites

To use this Sybase connector, you need to:

- Set up a self-hosted Integration Runtime. See [Self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) article to learn details.
- Install the [data provider for Sybase iAnywhere.Data.SQLAnywhere](http://go.microsoft.com/fwlink/?linkid=324846) 16 or above on the Integration Runtime machine.

## Getting started

***[place-holder] generic section across all connectors. To link to a tutorial/sample and more instructions***

You can create a pipeline with copy activity using .NET SDK, Python SDK, Azure PowerShell, REST API, or Azure Resource Manager template. See [Copy activity tutorial](quickstart-create-data-factory-dot-net.md) for step-by-step instructions to create a pipeline with a copy activity.

The following sections provide details about properties that are used to define Data Factory entities specific to Sybase connector.

## Linked service properties

The following properties are supported for Sybase linked service:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property must be set to: **Sybase** | Yes |
| server | Name of the Sybase server. |Yes |
| database | Name of the Sybase database. |Yes |
| schema | Name of the schema in the database. |No |
| authenticationType | Type of authentication used to connect to the Sybase database.<br/>Allowed values are: **Basic**, and **Windows**. |Yes |
| username | Specify user name to connect to the Sybase database. |Yes |
| password | Specify password for the user account you specified for the username. |Yes |

**Example:**

```json
{
    "name": "SybaseLinkedService",
    "properties": {
        "type": "Sybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## Dataset properties

For a full list of sections and properties available for defining datasets, see the [datasets](concepts-datasets.md) article. This section provides a list of properties supported by Sybase dataset.

To copy data from Sybase, set the type property of the dataset to **RelationalTable**. The following properties are supported:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property of the dataset must be set to: **RelationalTable** | Yes |
| tableName | Name of the table in the Sybase database. | No (if "query" in activity source is specified) |

**Example**

```json
{
    "name": "SybaseDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Sybase linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## Copy activity properties

For a full list of sections and properties available for defining activities, see the [Pipelines](concepts-pipelines-activities.md) article. This section provides a list of properties supported by Sybase source.

### Sybase as source

To copy data from Sybase, set the source type in the copy activity to **RelationalSource**. The following properties are supported in the copy activity **source** section:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property of the copy activity source must be set to: **RelationalSource** | Yes |
| query | Use the custom SQL query to read data. For example: "SELECT * FROM MyTable". | No (if "tableName" in dataset is specified) |

**Example:**

```json
"activities":[
    {
        "name": "CopyFromSybase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Sybase input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "RelationalSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## Data type mapping for Sybase

When copying data from Sybase, the following mappings are used from Sybase data types to Azure Data Factory interim data types. See [Schema and data type mappings](map-columns.md) to learn about how copy activity maps the source schema and data type to the sink.

Sybase supports T-SQL types. For a mapping table from SQL types to Azure Data Factory interim data types, see [Azure SQL Database Connector - data type mapping](copy-data-to-from-azure-sql-database#data-type-mapping-for-azure-sql-database) section.

