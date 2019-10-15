---
title: What is the Azure SQL Database Edge? | Microsoft Docs
description: 'Get an introduction to SQL Database Edge: technical details and capabilities.'
keywords: introduction to SQL Database Edge,what is sql database edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: stevestein
ms.author: sourabha
ms.reviewer: sstein
ms.date: 10/15/2019
---

# What is Azure SQL Database Edge?

Azure SQL Database Edge (preview) is an optimized relational database engine geared for IoT and Edge deployments. It provides capabilities to create an high-performance data storage and processing layer for IoT applications and solutions. Azure SQL Database Edge provides capabilities to stream, process and analyze relational and non-relational uch as JSON, graph and time-series data , which makes it the right choice for a variety of modern IoT applications.

Azure SQL Database Edge is built on the latest versions of the [Microsoft SQL Server Database Engine](/sql/sql-server/sql-server-technical-documentation?toc=/azure/sql-database-edge/toc.json), which provides industry leading performance, security and query processing capabilities. Since, Azure SQL Database Edge is built on the same engine as SQL Server and Azure SQL Database, it provides the same T-SQL programming surface area which makes development of applications or solutions easier and faster, and at the same makes application portability between Edge, data centers and cloud really straight forward.

## Deployment Models  

Azure SQL Database Edge is available on the Azure Marketplace and can be deployed as a module for [Azure IoT Edge](../iot-edge/about-iot-edge.md). For more information refer (Insert link to deployment document).  

:::image type="content" source="media/overview/sql-database-edge-overview.png" alt-text="SQL Database Edge overview diagram":::

## Pricing and Availability

Azure SQL Database is currently under public preview. For more information on the pricing and availability, see [Azure SQL Database Edge](https://azure.microsoft.com/services/sql-database-edge/).

> [!IMPORTANT]
> To understand the feature differences between Azure SQL Database Edge and SQL Server, as well as the differences among different Azure SQL Database Edge options, see [SQL DB Edge Database features](Add link to Database Features section).

## Streaming Capabilities  

Azure SQL Database Edge provides built in streaming capabilities for real-time analytics and complex event-processing. This streaming capabilities are built using constructs as [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) and provides similar capabilities as [Azure Stream Analytics on IoT Edge](../stream-analytics/stream-analytics-edge.md).

The streaming engine for Azure SQL Database Edge is designed for low-latency, resiliency, efficient use of bandwidth and compliance.

## Machine Learning and Artificial Intelligence Capabilities

Azure SQL Database Edge provides built-in machine learning and analytics capabilities by integrating the open format ONNX (Open Neural Network Exchange) runtime, which allows exchange of deep learning and neural network models between different frameworks. For more information on ONNX refer [here](https://onnx.ai/). ONNX runtime provides the flexibility to develop models in a language or tools of your choice, which can then be converted to the ONNX format for execution within SQL Database Edge. For more information refer (insert link to the section on Analytics).

## Working with Azure SQL Database Edge

Azure SQL Database Edge makes developing and maintaining applications easier and more productive. Users can use all the fimiliar tools and skills to build great apps and solutions for their IoT/Edge needs. User can develop in SQL Database Edge using tools like 

- [The Azure portal](https://portal.azure.com/):

  A web-based application for managing all Azure services.
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms/):

  A free, downloadable client application for managing any SQL infrastructure, from SQL Server to SQL Database.
- [SQL Server Data Tools in Visual Studio](/sql/ssdt/download-sql-server-data-tools-ssdt/):

  A free, downloadable client application for developing SQL Server relational databases, SQL databases, Integration Services packages, Analysis Services data models, and Reporting Services reports.
- [Azure Data Studio](/sql/azure-data-studio/what-is/):

  A free, downloadable, cross platform database tool for data professional using the Microsoft family of on-premises and cloud data platforms on Windows, MacOS and Linux.
- [Visual Studio Code](https://code.visualstudio.com/docs):

  A free, downloadable, open-source code editor for Windows, macOS, and Linux. It supports extensions, including the [mssql extension](https://aka.ms/mssql-marketplace) for querying Microsoft SQL Server, Azure SQL Database, and Azure SQL Data Warehouse.

## SQL Database Edge frequently asked questions

<< Add frequently asked questions>>

## Next steps

- See the [pricing page](Add link to pricing page) for pricing and availability related details
- [Whitelisting of subscriptions for Azure SQL Database Edge](Add link).
- For quickstarts to get started, see the following:
  - [Deploy SQL Database Edge through Azure Portal](Add link)
  - [Deploy a streaming job for Azure SQL Database Edge](Add Link)
  - [Machine learning and Analytics on the Edge with Azure SQL Database Edge](Add Link)
