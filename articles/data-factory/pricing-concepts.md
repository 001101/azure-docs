---
title: Understanding data factory pricing concepts | Microsoft Docs
description: This article helps breakdown the data factory pricing model
documentationcenter: ''
author: shlo
manager: craigg

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/22/2018
ms.author: shlo

---
# Understanding Data Factory Pricing through examples

## Scenario #1: Copy data from AWS S3 to Azure Blob Storage on an hourly schedule.

To accomplish the scenario, you need to create a pipeline that has a copy activity with an input dataset for the data to be copied from AWS S3, an output dataset for the data on Azure Storage and a schedule trigger to execute the pipeline every hour.


 ![Scenario1](media/pricing-concepts/scenario1.png)

| **Operations** | **Types and Units** |
| --- | --- |
| Create Linked Service | 2 Read/Write entity  |
| Create Datasets | 4 Read/Write entities (2 for datasets create, 2 for linked service references) |
| Create Pipeline | 3 Read/Write entities (1 for pipeline create, 2 for datasets references) |
| Get Pipeline | 1 Read/Write entity |
| Run Pipeline | 2 Activity runs (1 for trigger run, 1 for activity runs) |
| Copy Data Assumption: execution time = 10 min | 10 \* 4 Azure Integration Runtime (default DIU setting = 4) For more information on data integration units and optimizing copy performance, click [here](https://docs.microsoft.com/en-us/azure/data-factory/copy-activity-performance) |
| Monitor PipelineAssumption: Only 1 run occurred | 2 Montoring run records retried (1 for pipeline run, 1 for activity run) |

**Total Scenario pricing: $0.16811**

- Data Factory Operations = **$0.0001**
  - Read/Write = 10\*00001 = $0.0001 [1 R/W = $0.50/50000 = 0.00001]
  - Monitoring  = 2\*000005 = $0.00001 [1 Monitoring = $0.25/50000 = 0.000005]
- Pipeline Orchestration &amp; Execution = **$0.168**
  - Activity Runs = 001\*2 = 0.002 [1 run = $1/1000 = 0.001]
  - Data Movement Activities = $0.166 (Prorated for 10 minutes execution time. $0.25/hour on Azure Integration Runtime)

## Scenario #2: Copy data from AWS S3 to Azure Blob Storage and transform with Azure Databricks on an hourly schedule

To accomplish the scenario, you need to create a pipeline with:

1. One copy activity with an input dataset for the data to be copied from AWS S3, an output dataset for the data on Azure Storage
2. One Azure Databricks activity for the data transformation
3. One schedule trigger to execute the pipeline every hour

![Scenario2](media/pricing-concepts/scenario2.png)

| **Operations** | **Types and Units** |
| --- | --- |
| Create Linked Service | 3 Read/Write entity  |
| Create Datasets | 4 Read/Write entities (2 for datasets create, 2 for linked service references) |
| Create Pipeline | 3 Read/Write entities (1 for pipeline create, 2 for datasets references) |
| Get Pipeline | 1 Read/Write entity |
| Run Pipeline | 3 Activity runs (1 for trigger run, 2 for activity runs) |
| Copy Data Assumption: execution time = 10 min | 10 \* 4 Azure Integration Runtime (default DIU setting = 4) For more information on data integration units and optimizing copy performance, click [here](https://docs.microsoft.com/en-us/azure/data-factory/copy-activity-performance) |
| Monitor PipelineAssumption: Only 1 run occurred | 3 Monitoring run records retried (1 for pipeline run, 2 for activity run) |
| Execute Databricks activityAssumption: execution time = 10 min | 10 min External Pipeline Activity Execution |

**Total Scenario pricing: $0.16916**

- Data Factory Operations = **$0.00012**
  - Read/Write = 11\*00001 = $0.00011 [1 R/W = $0.50/50000 = 0.00001]
  - Monitoring  = 3\*000005 = $0.00001 [1 Monitoring = $0.25/50000 = 0.000005]
- Pipeline Orchestration &amp; Execution = **$0.16904**
  - Activity Runs = 001\*3 = 0.003 [1 run = $1/1000 = 0.001]
  - Data Movement Activities = $0.166 (Prorated for 10 minutes execution time. $0.25/hour on Azure Integration Runtime)
  - External Pipeline Activity = $0.000041 (Prorated for 10 minutes execution time. $0.00025/hour on Azure Integration Runtime)

## Scenario #3: Copy data from AWS S3 to Azure Blob Storage and transform with Azure Databricks (with dynamic parameters to the script) on an hourly schedule

To accomplish the scenario, you need to create a pipeline with

1. One copy activity with an input dataset for the data to be copied from AWS S3, an output dataset for the data on Azure Storage
2. One Lookup activity for passing parameters dynamically to the transformation script
3. One Azure Databricks activity for the data transformation
4. One schedule trigger to execute the pipeline every hour

![Scenario3](media/pricing-concepts/scenario3.png)

| **Operations** | **Types and Units** |
| --- | --- |
| Create Linked Service | 3 Read/Write entity  |
| Create Datasets | 4 Read/Write entities (2 for datasets create, 2 for linked service references) |
| Create Pipeline | 3 Read/Write entities (1 for pipeline create, 2 for datasets references) |
| Get Pipeline | 1 Read/Write entity |
| Run Pipeline | 4 Activity runs (1 for trigger run, 3 for activity runs) |
| Copy Data Assumption: execution time = 10 min | 10 \* 4 Azure Integration Runtime (default DIU setting = 4) For more information on data integration units and optimizing copy performance, click [here](https://docs.microsoft.com/en-us/azure/data-factory/copy-activity-performance) |
| Monitor PipelineAssumption: Only 1 run occurred | 4 Monitoring run records retried (1 for pipeline run, 3 for activity run) |
| Execute Lookup activityAssumption: execution time = 1 min | 1 min Pipeline Activity execution |
| Execute Databricks activityAssumption: execution time = 10 min | 10 min External Pipeline Activity execution |



**Total Scenario pricing: $0.17020**

- Data Factory Operations = **$0.00013**
  - Read/Write = 11\*00001 = $0.00011 [1 R/W = $0.50/50000 = 0.00001]
  - Monitoring  = 4\*000005 = $0.00002 [1 Monitoring = $0.25/50000 = 0.000005]
- Pipeline Orchestration &amp; Execution = **$0.17007**
  - Activity Runs = 001\*4 = 0.004 [1 run = $1/1000 = 0.001]
  - Data Movement Activities = $0.166 (Prorated for 10 minutes execution time. $0.25/hour on Azure Integration Runtime)
  - Pipeline Activity = $0.00003 (Prorated for 1 minute execution time. $0.002/hour on Azure Integration Runtime)
  - External Pipeline Activity = $0.000041 (Prorated for 10 minutes execution time. $0.00025/hour on Azure Integration Runtime)
