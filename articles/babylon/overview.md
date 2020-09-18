---
title: Introduction to Azure Babylon
description: This article provides an overview of Azure Babylon, including its features and the problems it addresses. Azure Babylon enables any user to register, discover, understand, and consume data sources.
author: hophan
ms.author: hophan
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: overview
ms.date: 09/10/2020
---

# What is Azure Babylon?

Azure Babylon is a new cloud service for use by data users. You use Azure Babylon to centrally manage data governance across your data estate, spanning both cloud and on-prem environments. This service enables business analysts to search for relevant data by using meaningful business terms.

Technical users can view metadata and the lineage of data assets in a central catalog by using the UI or Apache Atlas API.

Subject-matter experts and owners can annotate data assets to share their tribal knowledge. They do so with the UI or at scale through automated classification, based on metadata and content inspection.

The Azure Babylon preview provides customers early access to preview releases in their Azure subscriptions for evaluation. You get product support and can provide feedback directly to the product team.

## Transition from ADC Gen 2 to Babylon

The service ADC Gen 2 is being rolled into a broader data governance offering code named Babylon.

Based on the feedback from our customers who used the ADC Gen 2 private preview, we're making the following changes.

### Enhancements to the catalog UX

* Business and technical users can find relevant data more easily.
* Data stewards can manage the governance of data assets more effectively.

### Enhancements to the data map platform powering the catalog UX

* Powered additional data governance scenarios for data officers and consumers.
* Made the catalog more ambient by seamless integration with Azure data sources such as Synapse.
* Added classifications and increased the coverage of data sources that can be scanned.

We released the first version of Babylon under a private preview in April to a handful of customers. These customers successfully provisioned Babylon and used its cataloging capability. The Babylon April release is at functional parity with the latest release of ADC Gen 2.

## Discovery challenges for data consumers

Traditionally, discovering enterprise data sources has been an organic process based on tribal knowledge. For companies that want the most value from their information assets, this approach presents many challenges:

* Because a central location to register data sources doesn't exist, users might not know about a data source unless they come into contact with it as part of another process.
* Unless users know the location of a data source, they can't connect to the data by using a client application. Data-consumption experiences require users to know the connection string or path.
* The intended use of the data is hidden to users unless they know the location of a data source's documentation. Data sources and documentation might live in several places and be consumed through different kinds of experiences.
* If users have questions about an information asset, they must locate the expert or team that's responsible for the data and engage them offline. There's no explicit connection between data and the experts that have perspectives on its use.
* Unless users understand the process for requesting access to the data source, discovering the data source and its documentation won't help them access the data.

## Discovery challenges for data producers

Although data consumers face the previously mentioned challenges, users who are responsible for producing and maintaining information assets face challenges of their own:

* Annotating data sources with descriptive metadata is often a lost effort. Client applications typically ignore descriptions that are stored in the data source.
* Creating documentation for data sources can be difficult and it's an ongoing responsibility to keep documentation in sync with data sources. Users might not trust documentation that's perceived as being out of date.
* Creating and maintaining documentation for data sources is complex and time-consuming. Making that documentation readily available to everyone who uses the data source can be even more so.
* Restricting access to data sources and ensuring that data consumers know how to request access is an ongoing challenge.

When such challenges are combined, they present a significant barrier for companies that want to encourage and promote the use and understanding of enterprise data.

## Azure Babylon advantages

Azure Babylon is designed to address the issues mentioned in the previous section and to help enterprises get the most value from their existing information assets. The catalog makes data sources easily discoverable and understandable by the users who manage the data.

Azure Babylon provides a cloud-based service into which a data source can be registered. The data remains in its existing location, but a copy of its metadata is added to Azure Babylon, along with a reference to the data-source location. The metadata is also indexed to make each data source easily discoverable via search and understandable to the users who discover it.

After you register a data source, you can then enrich its metadata. The metadata can be added either by the user who registered it or by other users in the enterprise. Any user can annotate a data source by providing descriptions, tags, or other metadata, such as documentation and processes, for requesting data source access. This descriptive metadata supplements the structural metadata, such as column names and data types, that's registered from the data source.

Discovering and understanding data sources and their use is the primary purpose of registering the sources. Enterprise users might need data for business intelligence, application development, data science, or any other task where the right data is required. They use the data catalog discovery experience to quickly find data that matches their needs, understand the data to evaluate its fitness for the purpose, and consume the data by opening the data source in their tool of choice.

At the same time, users can contribute to the catalog by tagging, documenting, and annotating data sources that have already been registered. They can also register new data sources, which are then discovered, understood, and consumed by the community of catalog users.

## Next steps

To get started with Azure Babylon:
[Create a Babylon account](create-catalog-portal.md)