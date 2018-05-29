---
title: Azure Analysis Services overview| Microsoft Docs
description: Get the big picture of Analysis Services in Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: overview
ms.date: 05/16/2018
ms.author: owend
ms.reviewer: minewiskan
#Customer intent: As a BI developer, I want to determine if Azure Analysis Services is the best data modeling platform for out organization.

---
# What is Azure Analysis Services?
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Azure Analysis Services provides enterprise-grade data models in the cloud. It's a fully managed platform as a service (PaaS), integrated with Azure data platform services. 

With Analysis Services, you can mashup and combine data from multiple data sources, define metrics, and secure your data in a single, trusted tabular semantic data model. The data model provides an easier and faster way for users to browse massive amounts of data with client applications like Power BI, Excel, Reporting Services,  third-party, and custom apps.

![Data sources](./media/analysis-services-overview/aas-overview-data-sources.png)

Check out [this video](https://sec.ch9.ms/ch9/d6dd/a1cda46b-ef03-4cea-8f11-68da23c5d6dd/AzureASoverview_high.mp4) to learn how Azure Analysis Services fits in with Microsoft's overall BI capabilities, and how you can benefit from getting your data models into the cloud.

## Built on SQL Server Analysis Services

Azure Analysis Services is compatible with many great features already in SQL Server Analysis Services Enterprise Edition. Azure Analysis Services supports tabular models at the 1200 and higher [compatibility levels](analysis-services-compat-level.md). Partitions*, perspectives*, row-level security, bi-directional relationships, and translations are all supported. 

Azure Analysis Services supports in-memory and DirectQuery modes. In-memory mode (default) tabular models support multiple, different data sources. Because data is cached in-memory (server), this mode provides extremely fast query response. It also provides the greatest flexibility for complex datasets and queries. Partitions, calculated tables, and all DAX functions are supported. Unlike DirectQuery models, in-memory models must be refreshed (processed) to update the cached data from data sources. For some very large data models, configuring refresh can add some complexity. 

DirectQuery mode* supports extremely large data sets in single SQL Server, SQL Server Data Warehouse, Azure SQL Database, Azure SQL Data Warehouse, Oracle, and Teradata data sources. DirectQuery has many benefits over in-memory tabular models. For example, datasets can exceed available memory resources for your server, and complex data model refresh scenarios aren't needed because queries are against the datasource. There are also restrictions, such as limted datasource types, DAX formula limitations. Before determining the best mode for you, see [Direct Query mode](https://docs.microsoft.com/sql/analysis-services/tabular-models/directquery-mode-ssas-tabular).

Multidimensional and PowerPivot for Sharepoint modes *are not* supported in Azure Analysis Services. Mode is configured during model development in Visual Studio (SSDT) or in SQL Server Management Studio. Compatibility level is specified when you create you tabular model project in Visual Studio. Models imported from Power BI Desktop are at 1400 compatibility level. Typically, you want your models on the latest compatibility level.

\* Feature availability depends on tier.

## Better with Azure

Azure Analysis Services integrates with many Azure services enabling you to build sophisticated analytics solutions. Integration with [Azure Active Directory](../active-directory/active-directory-whatis.md) provides secure, role-based access to your critical data. Integrate with [Azure Data Factory](../data-factory/introduction.md) pipelines by including an activity that loads data into the model. [Azure Automation](../automation/automation-intro.md) and [Azure Functions](../azure-functions/functions-overview.md) can be used for lightweight orchestration of models using custom code.

## Get up and running quickly

In Azure portal, you can [create a server](analysis-services-create-server.md) within minutes. And, with Azure Resource Manager [templates](../azure-resource-manager/resource-manager-create-first-template.md) and PowerShell, you can provision servers using a declarative template. With a single template, you can deploy multiple services along with other Azure components such as storage accounts and Azure Functions. 

## The right tier when you need it

Azure Analysis Services is available in Developer, Basic, and Standard tiers. Within each tier, plan costs vary according to processing power, QPUs, and memory size. When you create a server, you select a plan within a tier. You can change plans up or down within the same tier, or upgrade to a higher tier, but you cannot downgrade from a higher tier to a lower tier.

#### Developer tier
This tier is recommended for evaluation, development, and test scenarios. A single plan includes the same functionality of the standard tier, but is limited in processing power, QPUs, and memory size. Query replica scale-out is not available for this tier. This tier does not offer an SLA.

|Plan  |QPUs  |Memory (GB)  |
|---------|---------|---------|
|D1    |    20     |    3     |


#### Basic tier
The tier is recommended for production solutions with smaller tabular models, limited user concurrency, and simple data refresh requirements. Query replica scale-out *is not available* for this tier. Perspectives, multiple partitions, and DirectQuery tabular model features *are not supported* in this tier.  

|Plan  |QPUs  |Memory (GB)  |
|---------|---------|---------|
|B1    |    40     |    10     |
|B2    |    80     |    20     |

#### Standard tier
This tier is for mission-critical production applications that require elastic user-concurrency, and have rapidly growing data models. It has advanced data refresh capability for near real-time data model updates, and supports all advanced tabular modeling features.

|Plan  |QPUs  |Memory (GB)  |
|---------|---------|---------|
|S1    |    40     |    10     |
|S2    |    100     |    25     |
|S3    |    200     |    50     |
|S4    |    400     |    100     |
|S8*    |    320     |    200     |
|S9*    |    640    |    400     |

\* Not available in all regions.  

## Availability by region

### Americas
|Region  | Supported tiers | Query replicas |
|---------|---------|:---------:|
|Brazil South     |    B1, B2, S0, S1, S2, S4, D1     |     1    |
|Canada Central    |     B1, B2, S0, S1, S2, S4, D1    |     1    |
|East US     |     B1, B2, S0, S1, S2, S4, D1    |    1     |
|East US 2     |     B1, B2, S0, S1, S2, S4, S8, S9, D1     |    7     |
|North Central US     |     B1, B2, S0, S1, S2, S4, D1     |    1     |
|Central US     |    B1, B2, S0, S1, S2, S4, D1     |    3     |
|South Central US     |    B1, B2, S0, S1, S2, S4, D1     |    1     |
|West Central US   |     B1, B2, S0, S1, S2, S4, D1    |    7     |
|West US     |    B1, B2, S0, S1, S2, S4, S8, S9, D1     |    7     |
|West US2    |    B1, B2, S0, S1, S2, S4, S8, S9, D1     |    1     |

### Europe
|Region  | Supported tiers | Query replicas |
|---------|---------|:---------:|
|North Europe     |    B1, B2, S0, S1, S2, S4, D1      |    1     |
|UK South   |    B1, B2, S0, S1, S2, S4, D1      |     1    |
|West Europe     |    B1, B2, S0, S1, S2, S4, S8, S9, D1      |    7     |

### Asia Pacific 
|Region  | Supported tiers | Query replicas |
|---------|---------|:---------:|
|Australia Southeast     | B1, B2, S0, S1, S2, S4, D1       |    1     |
|Japan East  |   B1, B2, S0, S1, S2, S4, D1       |    1     |
|Southeast Asia     |     B1, B2, S0, S1, S2, S4, S8, S9, D1     |   3      |
|West India     |    B1, B2, S0, S1, S2, S4, D1     |    1     |


## Pricing

Total cost depends on a number of factors; for example, your chosen region, tier, query replicas, pausing and resuming your server resource, etc.. Use the [Azure Analysis Services Pricing](https://azure.microsoft.com/pricing/details/analysis-services/) calculator to determine typical pricing for your region. This tool calculates pricing for a single server instance for a single region. Keep in mind, each query replica server is billed at the same rate as the server. 

## Scale to your needs

### Scale up\down, pause and resume

Go up, down, or pause your server. Use the Azure portal or have total control on-the-fly by using PowerShell. You only pay for what you use. To learn more about the different plans and tiers, and use the pricing calculator to determine the right plan for you, see [Azure Analysis Services Pricing](https://azure.microsoft.com/pricing/details/analysis-services/).

### Scale-out resources for fast query responses

With Azure Analysis Services scale-out, client queries are distributed among multiple *query replicas* in a query pool. Query replicas have synchronized copies of your tabular models. By spreading the query workload, response times during high query workloads can be reduced. Model processing operations can be separated from the query pool, ensuring client queries are not adversely affected by processing operations. 

You can create a query pool with up to seven additional query replicas (eight total, including your server). The number of query replicas you can have in your pool depend on your chosen region. Query replicas cannot be spread outside your server's region. Query replicas are billed at the same rate as your server.

Just like with changing tiers, you can scale-out query replicas according to your needs. Configure scale-out in the portal or by using REST APIs. To learn more, see [Azure Analysis Services scale-out](analysis-services-scale-out.md).

## Migrate your existing tabular models
If you already have existing on-premises SQL Server Analysis Services model solutions, you can migrate to Azure Analysis Services without significant changes. To migrate, you can use SSDT or Deployment to deploy your model to your server. Or, in SSMS, you can use backup and restore or TMSL.

If you have on-premises data sources, you  need to install and configure an [On-premises data gateway](analysis-services-gateway.md). If you have roles and role members already configured, your roles migrate, but you have to readd role members by using SSMS or PowerShell.

## Use the tools you already know

![BI developer tools](./media/analysis-services-overview/aas-overview-dev-tools.png)

### SQL Server Data Tools (SSDT) for Visual Studio
Develop and deploy models with the free [SQL Server Data Tools (SSDT) for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). SSDT includes Analysis Services project templates that get you up and going quickly. SSDT now includes the modern Get Data datasource query and mashup functionality for tabular 1400 models. If you're familiar with Get Data in Power BI Desktop and Excel 2016, you already know how easy it is to create highly customized data source queries. For Visual Studio 2017, you can also 

Microsoft Analysis Services Projects are also available for Visual Studio as an installable package VSIX. [Download from Marketplace](https://marketplace.visualstudio.com/items?itemName=ProBITools.MicrosoftAnalysisServicesModelingProjects).

### Sql Server Management Studio
Manage your servers and model databases by using [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx). Connect to your servers in the cloud. Run TMSL scripts right from the XMLA query window, and automate tasks by using TMSL scripts and PowerShell. New features and functionality happen fast - SSMS is updated monthly.

### PowerShell
Server resource management tasks like creating server resources, suspending or resuming server operations, or changing the service level (tier) use Azure Resource Manager (AzureRM) cmdlets. Other tasks for managing databases such as adding or removing role members, processing, or running TMSL scripts use cmdlets in the SqlServer module. To learn more, see [Manage Azure Analysis Services with PowerShell](analysis-services-powershell.md).

### Pro BI developers

Tabular models offer rapid development and are highly customizable. 
For developers, tabular models include the Tabular Object Model (TOM) to describe model objects. TOM is exposed in JSON through the [Tabular Model Scripting Language (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference) and the AMO data definition language through the [Microsoft.AnalysisServices.Tabular](https://msdn.microsoft.com/library/microsoft.analysisservices.tabular.aspx) namespace. 

## Your data is secure
![Data visualizations](./media/analysis-services-overview/aas-overview-secure.png)

### Authentication
User authentication for Azure Analysis services is handled by [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md). When attempting to log in to an Azure Analysis Services database, users use an organization account identity with access to the database they are trying to access. These user identities must be members of the default Azure Active Directory for the subscription where the Azure Analysis Services server resides. To learn more, see [Authentication and user permissions](analysis-services-manage-users.md).

### Data security
Azure Analysis Services utilizes Azure Blob storage to persist storage and metadata for Analysis Services databases. Data files within Blob are encrypted using [Azure Blob Server Side Encryption (SSE)](../storage/common/storage-service-encryption.md). When using Direct Query mode, only metadata is stored. The actual data is accessed from the data source at query time.

Secure access to data residing on-premises in your organization is achieved by installing and configuring an [On-premises data gateway](analysis-services-gateway.md). Gateways provide access to data for both Direct Query and in-memory modes. When an Azure Analysis Services model connects to an on-premises data source, a query is created along with the encrypted credentials for the on-premises data source. The gateway cloud service analyzes the query and pushes the request to an Azure Service Bus. The on-premises gateway polls the Azure Service Bus for pending requests. The gateway then gets the query, decrypts the credentials, and connects to the data source for execution. The results are then sent from the data source, back to the gateway and then on to the Azure Analysis Services database.

### Firewall

Azure Analysis Services Firewall blocks all client connections other than those specified in rules. Configure rules specifying allowed IP addresses by individual client IPs or by range. Power BI (service) connections can also be allowed or blocked. Configure firewall and rules in the portal or by using PowerShell.


Azure Analysis Services is governed by the [Microsoft Online Services Terms](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) and the [Microsoft Online Services Privacy Statement](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
To learn more about Azure Security, see the [Microsoft Trust Center](https://www.microsoft.com/trustcenter/Security/AzureSecurity).



## Supports the latest client tools
![Data visualizations](./media/analysis-services-overview/aas-overview-clients.png)

Modern data exploration and visualization tools like Power BI, Excel, SQL Server 2017 Reporting Services, and third-party tools are all supported, providing users with highly interactive and visually rich insights into your model data. 


## Monitoring and diagnostics

Azure Analysis Services provides an extensive number of service specific metrics to help you monitor the performance and health of your servers. To learn more, see [Monitor server metrics](analysis-services-monitor.md). 

You can also setup Azure resource diagnostic logging.

## Get help

#### Documentation
Azure Analysis Services is simple to set up and to manage. You can find all the info you need to create and manage your server resources here. When creating a data model to deploy to your server, it's much the same as it is for creating a data model you deploy to an on-premises server. 


There's an extensive library of conceptual, procedural, tutorials, and reference articles at [SQL Server Analysis Services help](https://docs.microsoft.com/sql/analysis-services/analysis-services).

#### Videos
Check out helpful videos at [Azure Analysis Services on Channel 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

#### Blogs
Things are changing rapidly. You can always get the latest information on the [Analysis Services team blog](https://blogs.msdn.microsoft.com/analysisservices/) and [Azure blog](https://azure.microsoft.com/blog/).

#### Community
Analysis Services has a vibrant community of users. Join the conversation on [Azure Analysis Services forum](https://aka.ms/azureanalysisservicesforum).

## Feedback
Have suggestions or feature requests? Be sure to leave your comments on [Azure Analysis Services Feedback](https://aka.ms/azureanalysisservicesfeedback).

Have suggestions about the documentation? You can add comments using Livefyre at the bottom of each article.

## Next steps
Learn how to [create a server](analysis-services-create-server.md) in Azure.   
Once you have a server created, [add a sample model](analysis-services-create-sample-model.md).   
