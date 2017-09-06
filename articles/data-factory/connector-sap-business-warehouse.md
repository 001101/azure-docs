---
title: Copy data from SAP BW using Azure Data Factory | Microsoft Docs
description: Learn how to copy data from SAP Business Warehouse to supported sink data stores by using a copy activity in an Azure Data Factory pipeline.
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
# Copy data from SAP Business Warehouse using Azure Data Factory

This article outlines how to use the Copy Activity in Azure Data Factory to copy data from an SAP Business Warehouse (BW). It builds on the [copy activity overview](copy-activity-overview.md) article that presents a general overview of copy activity.

## Supported scenarios

You can copy data from SAP Business Warehouse to any supported sink data store. For a list of data stores that are supported as sources/sinks by the copy activity, see the [Supported data stores](copy-activity-overview.md#supported-data-stores-and-formats) table.

Specifically, this SAP Business Warehouse connector supports:

- SAP Business Warehouse **version 7.x**.
- Copying data from **InfoCubes and QueryCubes** (including BEx queries) using MDX queries.
- Copying data using basic authentication.

## Prerequisites

To use this SAP Business Warehouse connector, you need to:

- Set up a self-hosted Integration Runtime. See [Self-hosted Integration Runtime](create-self-hosted-integration-runtime.md) article for details.
- Install the **SAP NetWeaver library** on the Integration Runtime machine. You can get the SAP Netweaver library from your SAP administrator, or directly from the [SAP Software Download Center](https://support.sap.com/swdc). Search for the **SAP Note #1025361** to get the download location for the most recent version. Make sure that the architecture for the SAP NetWeaver library (32-bit or 64-bit) matches your gateway installation. Then install all files included in the SAP NetWeaver RFC SDK according to the SAP Note. The SAP NetWeaver library is also included in the SAP Client Tools installation.

> [!TIP]
> Put the dlls extracted from the NetWeaver RFC SDK into system32 folder.

## Getting started

***[place-holder] generic section across all connectors. To link to a tutorial/sample and more instructions***

You can create a pipeline with copy activity using .NET SDK, Python SDK, Azure PowerShell, REST API, or Azure Resource Manager template. See [Copy activity tutorial](quickstart-create-data-factory-dot-net.md) for step-by-step instructions to create a pipeline with a copy activity.

The following sections provide details about properties that are used to define Data Factory entities specific to SAP Business Warehouse connector.

## Linked service properties

The following properties are supported for SAP Business Warehouse (BW) linked service:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property must be set to: **SapBw** | Yes |
| server | Name of the server on which the SAP BW instance resides. | string | Yes |
| systemNumber | System number of the SAP BW system. | Two-digit decimal number represented as a string. | Yes |
| clientId | Client ID of the client in the SAP W system. | Three-digit decimal number represented as a string. | Yes |
| username | Name of the user who has access to the SAP server | string | Yes |
| password | Password for the user. | string | Yes |

**Example:**

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

For a full list of sections and properties available for defining datasets, see the [datasets](concepts-datasets-linked-services.md) article. This section provides a list of properties supported by SAP BW dataset.

To copy data from SAP BW, set the type property of the dataset to **RelationalTable**. While there are no type-specific properties supported for the SAP BW dataset of type RelationalTable.

**Example:**

```json
{
    "name": "SAPBWDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## Copy activity properties

For a full list of sections and properties available for defining activities, see the [Pipelines](concepts-pipelines-activities.md) article. This section provides a list of properties supported by SAP BW source.

### SAP BW as source

To copy data from SAP BW, set the source type in the copy activity to **RelationalSource**. The following properties are supported in the copy activity **source** section:

| Property | Description | Required |
|:--- |:--- |:--- |
| type | The type property of the copy activity source must be set to: **RelationalSource** | Yes |
| query | Specifies the MDX query to read data from the SAP BW instance. | Yes |

**Example:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## Data type mapping for SAP BW

When copying data from SAP BW, the following mappings are used from SAP BW data types to Azure Data Factory interim data types. See [Schema and data type mappings](map-columns.md) to learn about how copy activity maps the source schema and data type to the sink.

| SAP BW data type | Data factory interim data type |
|:--- |:--- |
| ACCP | Int |
| CHAR | String |
| CLNT | String |
| CURR | Decimal |
| CUKY | String |
| DEC | Decimal |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | Int |
| LANG | String |
| LCHR | String |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | String |
| UNIT | String |
| DATS | String |
| NUMC | String |
| TIMS | String |

