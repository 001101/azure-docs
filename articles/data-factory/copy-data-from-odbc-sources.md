---
title: Copy data from ODBC sources using Azure Data Factory | Microsoft Docs
description: Learn how to copy data from OData sources to supported sink data stores by using a copy activity in an Azure Data Factory pipeline.
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
ms.date: 08/30/2017
ms.author: jingwang

---
# Copy data from and to ODBC data stores using Azure Data Factory

This article outlines how to use the Copy Activity in Azure Data Factory to copy data from and to an ODBC data store. It builds on the [copy activity overview](copy-activity-overview.md) article that presents a general overview of copy activity.

## Supported scenarios

You can copy data from ODBC source to any supported sink data store, or copy from any supported source data store to ODBC sink. For a list of data stores supported as sources/sinks by the copy activity, see the [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats) table.

Specifically, this ODBC connector supports copying data from/to **any ODBC-compatible data stores** using **Basic** or **Anonymous** authentication.

## Prerequisites

To use this ODBC connector, you need to:

- Set up a self-hosted Integration Runtime. See [Self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) article for details.
- Install the ODBC driver for the data store on the Integration Runtime machine.

## Getting started

***[place-holder] generic section across all connectors. To link to a tutorial/sample and more instructions***

You can create a pipeline with copy activity using .NET SDK, Python SDK, Azure PowerShell, REST API, or Azure Resource Manager template. See [Copy activity tutorial](quickstart-create-data-factory-dot-net.md) for step-by-step instructions to create a pipeline with a copy activity.

The following sections provide details about properties that are used to define Data Factory entities specific to ODBC connector.

## Linked service properties

The following properties are supported for ODBC linked service:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property must be set to: **Odbc** | Yes |
| connectionString | The connection string excluding the credential portion. See examples in the next section. | Yes |
| authenticationType | Type of authentication used to connect to the ODBC data store.<br/>Allowed values are: **Basic** and **Anonymous**. | Yes |
| username | Specify user name if you are using Basic authentication. | No |
| password | Specify password for the user account you specified for the username. | No |
| credential | The access credential portion of the connection string specified in driver-specific property-value format. Example: `"RefreshToken=<secret refresh token>;"`. | No |

**Example 1: using Basic authentication**

```json
{
    "name": "ODBCLinkedService",
    "properties":
    {
        "type": "Odbc",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
            "userName": "<username>",
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

**Example 2: using Anonymous authentication**

```json
{
    "name": "ODBCLinkedService",
    "properties":
    {
        "type": "Odbc",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
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

For a full list of sections and properties available for defining datasets, see the [datasets](concepts-datasets.md) article. This section provides a list of properties supported by ODBC dataset.

To copy data from/to ODBC-compatible data store, set the type property of the dataset to **RelationalTable**. The following properties are supported:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property of the dataset must be set to: **RelationalTable** | Yes |
| tableName | Name of the table in the ODBC data store. | No for source (if "query" in activity source is specified);<br/>Yes for sink |

**Example**

```json
{
    "name": "ODBCDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "tableName": "<table name>"
        }
    }
}
```

## Copy activity properties

For a full list of sections and properties available for defining activities, see the [Pipelines](concepts-pipelines-activities.md) article. This section provides a list of properties supported by ODBC source.

### ODBC as source

To copy data from ODBC-compatible data store, set the source type in the copy activity to **RelationalSource**. The following properties are supported in the copy activity **source** section:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property of the copy activity source must be set to: **RelationalSource** | Yes |
| query | Use the custom SQL query to read data. For example: `"SELECT * FROM MyTable"`. | No (if "tableName" in dataset is specified) |

**Example:**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
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

### ODBC as sink

To copy data to ODBC-compatible data store, set the sink type in the copy activity to **OdbcSink**. The following properties are supported in the copy activity **sink** section:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property of the copy activity source must be set to: **OdbcSink** | Yes |
| writeBatchTimeout |Wait time for the batch insert operation to complete before it times out.<br/>Allowed values are: timespan. Example: “00:30:00” (30 minutes). |No |
| writeBatchSize |Inserts data into the SQL table when the buffer size reaches writeBatchSize.<br/>Allowed values are: integer (number of rows). |No (default is 0 - auto detected) |
| preCopyScript |Specify a SQL query for Copy Activity to execute before writing data into data store in each run. You can use this property to clean up the pre-loaded data. |No |

> [!NOTE]
> For "writeBatchSize", if it's not set (auto-detected), copy activity first detects whether the driver supports batch operations, and set it to 10000 if it does, or set it to 1 if it doesn’t. If you explicitly set the value other than 0, copy activity honors the value and fails at runtime if the driver doesn’t support batch operations.

**Example:**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## Troubleshoot connectivity issues

To troubleshoot connection issues, use the **Diagnostics** tab of **Integration Runtime Configuration Manager**.

1. Launch **Integration Runtime Configuration Manager**.
2. Switch to the **Diagnostics** tab.
3. Under the "Test Connection" section, select the **type** of data store (linked service).
4. Specify the **connection string** that is used to connect to the data store, choose the **authentication** and enter **user name**, **password**, and/or **credentials**.
5. Click **Test connection** to test the connection to the data store.
