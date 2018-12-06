﻿---
title: Azure Time Series Insights overview | Microsoft Docs
description: Azure Time Series Insights overview
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.workload: big-data
ms.topic: overview
ms.date: 12/05/2018
---

# Azure Time Series Insights overview

Azure Time Series Insights is an end-to-end platform-as-a-service offering to ingest, process, store, and query highly contextualized, time-series-optimized IoT-scale data. Time Series Insights is ideal for ad-hoc data exploration and operational analysis. Time Series Insights is a uniquely extensible and customized service offering that meets the broad needs of industrial IoT deployments.

## Defining IoT data

IoT data is any "industrial" data available in asset-intensive organizations. IoT data is often highly unstructured because it's sent from assets that record fairly noisy measurements, such as temperature, motion, and humidity. These data streams are frequently characterized by significant gaps, corrupted messages, and false readings. Data from these streams must be cleaned up before any analysis can occur. IoT data is often meaningful only in the context of additional data inputs that come from first-party sources, such as CRM or ERP, or third-party data sources, such as weather or location.

As a result, only a fraction of the data gets used for operational and business purposes. Such data provides consistent, comprehensive, current, and correct information for business reporting and analysis. Turning collected IoT data into actionable insights requires a few key capabilities:

* Data processing to clean, filter, interpolate, transform, and prepare data for analysis
* Structure to navigate and understand the data, that is, to normalize and contextualize the data
* Cost-effective storage for long or infinite retention for decades' worth of processed, or derived, data and raw data

A typical IoT data flow is depicted in the following image.

  ![IoT data flow][1]

## Azure Time Series Insights for industrial IoT

The current IoT landscape is diverse. Customers span the manufacturing, automotive, energy, utilities, smart buildings, and consulting industries. Scenarios include ad-hoc data exploration where the shape of the data is unknown. Scenarios also include operational analysis over schematized, or explicitly modeled, data to drive operational efficiency. These scenarios typically exist side by side and support different use cases. Platform capabilities that are key to the success of industrial IoT enterprises and their digital revolution include:

- Multilayered storage, both warm and cold. 
- The ability to store decades' worth of time series data. 
- The ability to explicitly model and optimize queries for asset-based operational intelligence.

Azure Time Series Insights is a comprehensive end-to-end platform-as-a-service offering for IoT data exploration and operational insights. Time Series Insights offers a fully managed cloud service for analyzing IoT-scale time series data.

You can store raw data in a schema-less, in-memory store. You can then perform interactive ad-hoc queries through a distributed query engine and API. Make use of the rich user experience to visualize billions of events in seconds. Learn more about the [data exploration capabilities](./time-series-insights-overview.md).

Time Series Insights also offers operational insights capabilities currently in preview. Together with interactive data exploration and operational intelligence, you can use Time Series Insights to derive more value out of data collected from IoT assets. The preview offering supports the following key capabilities:

* A scalable, performance, and cost-optimized time series data store that enables a cloud-based IoT solution to trend years’ worth of time series data in seconds
* Semantic model support to describe the domain and metadata associated with the derived and non-derived signals from assets and devices
* An enhanced user experience that combines asset-based data insights with rich, ad-hoc data analytics to drive business and operational intelligence
* Integration with advanced machine learning and analytics tools like Azure Databricks, Apache Spark, Azure Machine Learning, Jupyter notebooks, and Power BI to help you tackle time series data challenges and drive operational efficiency

Together operational insights and data exploration are offered with a simple pay-as-you-go pricing model for data processing, storage, and query. This billing model is suited to your changing business needs.

This high-level data flow diagram depicts the updated capabilities.

  ![Key capabilities][2]

With the introduction of these key industrial IoT capabilities, Azure Time Series Insights provides the following key benefits.

| | |
| ---| ---|
| **Multi-layered storage for IoT-scale time series data** | With a common data processing pipeline for ingesting data, you can store data in warm storage for interactive queries or cold storage for large volumes of data. Take advantage of high-performing asset-based [queries](./time-series-insights-update-tsq.md). |
| **Time Series Model to contextualize raw telemetry and derive asset-based insights** | Contextualize raw telemetry data with the descriptive [Time Series Model](./time-series-insights-update-tsm.md). Derive rich operational intelligence with highly performance- and cost-optimized device-based queries. |
| **Seamless integration with other data solutions** | Because data in Time Series Insights is [stored](./time-series-insights-update-storage-ingress.md) in open-sourced Apache Parquet files, integration with other data solutions, whether first or third party, is easy for end-to-end scenarios. These scenarios include business intelligence, advanced machine learning, and predictive analytics. |
| **Near real-time data exploration** | The [Azure Time Series Insights explorer](./time-series-insights-update-explorer.md) user experience provides visualization for all data streaming through the ingestion pipeline. Shortly after connecting an event source, you can view, explore, and query event data to validate whether a device emits data as expected. You also can monitor an IoT asset for health, productivity, and overall effectiveness. |
| **Root-cause analysis and anomaly detection** | The [Azure Time Series Insights explorer](./time-series-insights-update-explorer.md) supports both pattern and perspective views to conduct and save multistep, root-cause analysis. In combination with Azure Stream Analytics, you can use Time Series Insights to detect alerts and anomalies in near real-time. |
| **Custom applications built on Time Series Insights platform** | Azure Time Series Insights supports the [JavaScript SDK](./tutorial-explore-js-client-lib.md). The SDK provides rich controls and simplified access to queries. Use the SDK to build custom IoT applications on top of Time Series Insights to suit your specific business needs. You also can use the Time Series Insights [Query APIs](./time-series-insights-update-tsq.md) directly to drive data into custom IoT applications. |

## Next steps

Get started with the Azure Time Series Insights Preview:

> [!div class="nextstepaction"]
> [Read the Quickstart guide](./time-series-insights-update-quickstart.md)

Learn about use cases:

> [!div class="nextstepaction"]
> [Azure Time Series Insights use cases](./time-series-insights-update-use-cases.md)

<!-- Images -->
[1]: media/v2-update-overview/overview_one.png
[2]: media/v2-update-overview/overview_two.png