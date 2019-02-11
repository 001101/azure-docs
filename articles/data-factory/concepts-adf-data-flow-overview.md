---
title: Azure Data Factory Mapping Data Flow Overview
description: An overview explanation of Mapping Data Flows in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/31/2019
--- 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

# What are Mapping Data Flows in Azure Data Factory?

Mapping Data Flows allow data engineers to develop graphical data transformation logic without writing code. The resulting data flows are executed as activities within ADF Pipelines using scaled-out Azure Databricks clusters.

The intent of ADF Data Flow is to provide a fully visual experience with no coding required. Your Data Flows will execute on your own execution cluster for scaled-out data processing. ADF handles all of the code translation, path optimization, and execution of your data flow jobs.

Start by creating data flows in Debug mode so that you can validate your transformation logic interactively. Next, add a Data Flow activity to your pipeline to execute and test your data flow in pipeline debug, or use "Trigger Now" in the pipeline to test your Data Flow from a pipeline Activity.

You will then schedule and monitor your data flow activities by using ADF pipelines that execute the Data Flow activity.

The Debug Mode toggle switch on the Data Flow design surface allows interactive building of data transformations. Debug Mode provides a data prep environment for data flow construction.
