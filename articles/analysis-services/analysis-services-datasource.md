---
title: Data sources supported in Azure Analysis Services | Microsoft Docs
description: Describes data sources supported for data models in Azure Analysis Services.
services: analysis-services
documentationcenter: ''
author: minewiskan
manager: kfile
editor: ''
tags: ''

ms.assetid: 6ec63319-ff9b-4b01-a1cd-274481dc8995
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/26/2018
ms.author: owend

---
# Data sources supported in Azure Analysis Services
Tabular models in Azure Analysis Services can connect to data sources in the cloud and on-premises in your organization. The types of data sources you can connect to depend on many factors such as available data connectors, model compatibility level, authentication type, providers, and On-premises data gateway support. 

Data connectors shown in Get Data or Import Wizard in Visual Studio are shown for both Azure Analysis Services and SQL Server Analysis Services. However, not all data sources and data connectors shown are supported in Azure Analysis Services.

## Azure data sources

|Datasource  |In-memory  |DirectQuery  |
|---------|---------|---------|
|Azure SQL Database     |   Yes      |    Yes      |
|Azure SQL Data Warehouse     |   Yes      |   Yes       |
|Azure Blob Storage*     |   Yes       |    No      |
|Azure Table Storage*    |   Yes       |    No      |
|Azure Cosmos DB (Beta)*     |  Yes        |  No        |
|Azure Data Lake Store*     |   Yes       |    No      |
|Azure HDInsight HDFS*     |     Yes     |   No       |
|Azure HDInsight Spark (Beta)*     |   Yes       |   No       |
|Azure Database for MySQL (Preview)*     |   Yes       |   No      |
|Azure Database for PostgreSQL (Preview)*     | Yes         |  No       |
||||

\* Tabular 1400 models only.

**Provider**   
In-memory and DirectQuery models connecting to Azure data sources use .NET Framework Data Provider for SQL Server.

## On-premises data sources

Connecting to on-premises data sources require an On-premises gateway. When using a gateway, esure 64-bit providers are installed.

### In-memory and DirectQuery

|Datasource | In-memory provider | DirectQuery provider |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0 |.NET Framework Data Provider for SQL Server |
| SQL Server |Microsoft OLE DB Provider for SQL Server |.NET Framework Data Provider for SQL Server | |
| SQL Server |.NET Framework Data Provider for SQL Server |.NET Framework Data Provider for SQL Server | |
| Oracle |Microsoft OLE DB Provider for Oracle, Oracle Data Provider for .NET |Oracle Data Provider for .NET | |
| Teradata |OLE DB Provider for Teradata, Teradata Data Provider for .NET |Teradata Data Provider for .NET | |
| Analytics Platform System |.NET Framework Data Provider for SQL Server |.NET Framework Data Provider for SQL Server | |
| | | |

\* Tabular 1400 models only.

### In-memory only

|Datasource  |  Provider
|---------|---------|
|Access Database     |  ACE 14 OLE DB provider
|Active Directory*     |  **?**
|Analysis Services     | ASOLEDB 10
|Analytics Platform System     |  **?**
|Dynamics CRM*     |  **?**
|Excel workbook     | ACE 14 OLE DB provider
|Exchange*     |  **?**
|Folder*     | **?**
|JSON document*     |  **?**
|Lines from binary*     | **?**
|MySQL Database     | **?**
|OData Feed*     | **?**
|ODBC query     | **?**
|OLE DB     |  **?**
|Postgre SQL Database*    | **?**
|SAP HANA*    |  **?**   
|SAP Business Warehouse*    |  **?**
|SharePoint*     |   **?**
|Sybase Database     |  **?**
|XML table*    |  **?**
|||
 
\* Tabular 1400 models only.

## Specifying a different provider

When migrating an on-premises SQL Server Analysis Services tabular model to Azure Analysis Services, it may be necessary to change the provider.

**To specify a provider**

1. In SSDT > **Tabular Model Explorer** > **Data Sources**, right-click a data source connection, and then click **Edit Data Source**.
2. In **Edit Connection**, click **Advanced** to open the Advance properties window.
3. In **Set Advanced Properties** > **Providers**, then select the appropriate provider.

## Impersonation
In some cases, it may be necessary to specify a different impersonation account. Impersonation account can be specified in Visual Studio (SSDT) or SSMS.

For on-premises data sources:

* If using SQL authentication, impersonation should be Service Account.
* If using Windows authentication, set Windows user/password. For SQL Server, Windows authentication with a specific impersonation account is supported only for in-memory data models.

For cloud data sources:

* If using SQL authentication, impersonation should be Service Account.

## Next steps
If you have on-premises data sources, be sure to install the [On-premises gateway](analysis-services-gateway.md).   
To learn more about managing your server in SSDT or SSMS, see [Manage your server](analysis-services-manage.md).

