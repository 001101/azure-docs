---
title: 'Internet Analyzer FAQ | Microsoft Docs'
description: The FAQ for Azure Internet Analyzer. 
services: internet-analyzer
author: megan-beatty

ms.service: internet-analyzer
ms.topic: guide
ms.date: 10/16/2019
ms.author: mebeatty
---
# Azure Internet Analyzer FAQ (Preview)

This is the FAQ for Azure Internet Analyzer- if you have additional questions, go to the [feedback forum](https://feedback.azure.com/forums/217313-networking) and post your question. When a question is frequently asked, we add it to this article so it can be found quickly and easily. 

## How do I participate in the preview? 
The preview is available to select customers. If you are interested in joining the preview,[request access](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR65LM6ELn65MggTwxb0d215UMkNIUDVGV1JGS0w5WFBHWklNMTNRTDQ4My4u) by providing us an active Azure subscription ID.  

## Do I need to embed the client to run a test? 
Yes, the Internet Analyzer client must be installed in your application to collect metrics specific to your users. [Learn how to install the client.](internet-analyzer-embed-client.md) 

## Do I get billed for participating in the preview? 
No, Azure Internet Analyzer is free to use in preview. There is no service-level agreement during preview. 

## What scenarios is Internet Analyzer designed to address? 

Internet Analyzer is designed to give you networking performance insights based on your user population. To help make the best performance decisions for your users, Internet Analyzer compares the performance of two Internet using your distinct user population. While Internet Analyzer can answer a multitude of questions, some of the most common are: 
* What is the impact of migrating to the cloud? 
    * *Suggested Test: Custom (your current on-premises infrastructure) vs. Azure (any preconfigured endpoint)*
* What is the best cloud for your end-user population in each region? 
    *  *Suggested Test: Custom (other cloud service) vs. Azure (any preconfigured endpoint)*
* What is the value of putting my data at the edge vs. in a data center? 
    *  *Suggested Test: Azure vs. Azure Front Door, Azure vs. Azure CDN from Microsoft*
* What is the performance benefit of Azure Front Door?
    *  *Suggested Test: Custom/ Azure/ CDN vs. Azure Front Door*
* What is the performance benefit of Azure CDN from Microsoft? 
    *  *Suggested Test: Custom/ Azure/ AFD vs. Azure CDN from Microsoft*
* How does Azure CDN from Microsoft stack up? 
    *  *Suggested Test: Custom (other CDN endpoint) vs. Azure CDN from Microsoft*

## Which tests can I run in preview? 

Each test you create in Internet Analyzer is composed of two endpoints—Endpoint A and Endpoint B. Any of the following combinations can be run as tests:  
* Two preconfigured endpoints, 
* One custom and one preconfigured endpoint, or 
* Two [custom endpoints](internet-analyzer-custom-endpoint.md) (one custom endpoint must reside in Azure). 

The following preconfigured endpoints are available during preview: 
* **Azure regions**
    * West US 
    * West US 2
    * East US
    * Central US
    * Brazil South
    * West Europe
    * North Europe 
    * UK West 
    * Japan West
    * Southeast Asia
    * Central India
    * UAE North
    * South Africa North
* **Multiple Azure region combinations** 
    * West US, West Europe 
    * East US, East Asia 
    * West US, East US 
    * West US, UAE North
    * East US, Brazil South 
    * West Europe, Southeast Asia 
    * West Europe, Brazil South
    * West Europe, UAE North
    * West US, West Europe, East Asia
    * West Europe, UAE North, Southeast Asia
    * West US, North Europe, Southeast Asia, UAE North, South Africa North 
* **Azure + Azure Front Door** - deployed on top any single or multiple Azure region combinations listed above
* **Azure + Azure CDN from Microsoft** - deployed on top any single Azure region combination listed above
* **Azure + Azure Traffic Manager** - deployed on any Multiple Azure region combination listed above

## How is Internet Analyzer different from other monitoring services provided by Azure? 

Internet Analyzer is designed to help you understand your end users' experience today and how to make decisions to improve your end users' experience. While other Azure monitoring tools provide insights into middle-mile and data center performance, Internet Analyzer focuses on measuring end-to-end Internet performance for your specific set of end users. 


## Next Steps 

To learn more, see our [Internet Analyzer Overview](internet-analyzer-overview.md).
