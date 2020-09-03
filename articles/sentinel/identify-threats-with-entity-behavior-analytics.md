---
title: Identify internal threats with Azure Sentinel's entity behavior analytics | Microsoft Docs
description:  Create behavioral baselines for entities (users, hostnames, IP addresses) and use them to detect anomalous behavior and identify security threats.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''

ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/19/2020
ms.author: yelevin

---
# Identify internal threats with Azure Sentinel's entity behavior analytics

## What is entity behavior analytics?

### The concept

Identifying internal threat sources in your organization, and their potential impact, has always been a labor-intensive process. Sifting through alerts, connecting the dots, and active hunting all add up to massive amounts of effort expended with minimal returns, and the possibility of many internal threats simply evading discovery.

Azure Sentinel’s entity behavioral analytics capability eliminates the drudgery from your analysts’ workloads and the uncertainty from their efforts, and delivers high-fidelity, actionable intelligence, so they can focus on investigation and remediation.

As Azure Sentinel collects logs and alerts from all of its connected data sources, it analyzes them and builds baseline behavioral profiles of your organization’s entities (users, hosts, IP addresses, applications etc.) across time and peer group horizon. Using a variety of techniques and machine learning capabilities, Sentinel can then identify anomalous activity and help you determine if an asset has been compromised. Not only that, but it can also figure out the relative sensitivity of particular assets, identify peer groups of assets, and evaluate the potential impact of any given compromised asset (its “blast radius”). Armed with this information, you can effectively prioritize your investigation and incident handling. 

### Entity pages

When you select any entity (user, host, URL, IP address…) in an alert or an investigation, you will land on an **entity page**, which is basically a portfolio of relevant information about that entity. Each type of entity has its own unique page, since information about users and about hosts will not be of the same type. On the user entity page, you will find basic information about the user, a list of recent events involving the user, summary counts of various types of alerts, and …

### Architecture overview

:::image type="content" source="media/identify-threats-with-entity-behavior-analytics/entity-behavior-analytics-architecture.png" alt-text="Entity behavior analytics architecture":::

### Security-driven analytics

Inspired by Gartner’s paradigm for User and Entity Behavior Analytics (UEBA) solutions, Azure Sentinel provides an "outside-in" approach, based on three layers of consideration:

- **Use cases:** By prioritizing for relevant attack vectors and scenarios based on security research aligned with the MITRE ATT&CK framework of tactics, techniques, and sub-techniques that puts various entities as victims, perpetrators, or pivot points in the kill chain; Azure Sentinel focuses specifically on the most valuable logs each data source can provide.

- **Data Sources:** While first and foremost supporting Azure data sources, Azure Sentinel thoughtfully selects third-party data sources to provide data that matches our threat scenarios.

- **Analytics:** Using various machine learning (ML) algorithms, Azure Sentinel identifies anomalous activities and presents evidence clearly and concisely in the form of contextual enrichments, some examples of which appear below.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/behavior-analytics-top-down.png" alt-text="Behavior analytics top-down approach":::

Azure Sentinel presents artifacts that help your security analysts get a clear understanding of anomalous activities in context, and in comparison with the user's baseline profile. Actions performed by a user (or a host, or an address) are evaluated contextually:

- across geographical locations, devices, and environments.
- across time and frequency horizons (compared to user's own history).
- compared to peers' behavior.
- compared to organization's behavior.

    :::image type="content" source="media/identify-threats-with-entity-behavior-analytics/context.png" alt-text="Entity context":::

A "true" outcome indicates an identified anomaly.

## Data schema

### Behavior analytics table



| Field                     | Description                                                         |
|---------------------------|---------------------------------------------------------------------|
| TenantId                  | unique ID number of the tenant                                      |
| SourceRecordId            | unique ID number of the EBA event                                   |
| TimeGenerated             | timestamp of the activity's occurrence                              |
| TimeProcessed             | timestamp of the activity's processing by the EBA engine            |
| ActivityType              | high-level category of the activity                                 |
| ActionType                | normalized name of the activity                                     |
| UserName                  | username of the user that initiated the activity                    |
| UserPrincipalName         | full username of the user that initiated the activity               |
| EventSource               | data source that provided the original event                        |
| SourceIPAddress           | IP address from which activity was initiated                        |
| SourceIPLocation          | country from which activity was initiated, enriched from IP address |
| SourceDevice              | hostname of the device that initiated the activity                  |
| DestinationIPAddress      | IP address of the target of the activity                            |
| DestinationIPLocation     | country of the target of the activity, enriched from IP address     |
| DestinationDevice         | name of the target device                                           |
| **UsersInsights**         | contextual enrichments of involved users                            |
| **DevicesInsights**       | contextual enrichments of involved devices                          |
| **ActivityInsights**      | contextual analysis of activity based on our profiling              |
| **InvestigationPriority** | anomaly score, between 0-10 (0=benign, 10=highly anomalous)         |
|

### Scoring

Each activity is scored with “Investigation Priority Score” – which determine the probability of a specific user performing a specific activity, based on behavioral learning of the user and their peers. Activities identified as the most abnormal receive the highest scores (on a scale of 0-10).

See how behavior analytics is used in [Microsoft Cloud App Security](https://techcommunity.microsoft.com/t5/microsoft-security-and/prioritize-user-investigations-in-cloud-app-security/ba-p/700136) for an example of how this works.

Using [KQL](https://docs.microsoft.com/azure/data-explorer/kusto/query/), we can query the Behavioral Analytics Table.

For example – in case we’d like to find all the users that failed to login to an Azure, while it was the first attempt to connect from a certain country and is even uncommon for their peers, we can use the following query:

```Kusto
BehaviorAnalytics
| where ActivityType == "FailedLogOn"
| where FirstTimeUserConnectedFromCountry == True
| where CountryUncommonlyConnectedFromAmongPeers == True
```

### User peers metadata - table and notebook

User peers' metadata provides important context in threat detections, in investigating an incident, and in hunting for a potential threat. Security analysts can observe the normal activities of a user's peers to determine if the user's activities are unusual as compared to those of his/her peers.

The **UserPeerAnalytics** table provides a ranked list of a user's peers, based on the user’s group membership in Azure Active Directory. For example, if the user is Guy Malul, Peer Analytics calculates all of Guy’s peers based on his mailing list, security groups, et cetera, and provides all his peers in the top 20 ranking in the table. The screenshot below shows the schema of UserPeerAnalytics table and an example of a row in the table. One of the peers of Guy is Pini. His peer rank is 18. The TF-IDF algorithm is used to normalize the weighing for calculating the rank: the smaller the group, the higher the weight. 

### Permission analytics - table and notebook

The **UserAccessAnalytics** table provides the list of direct and transitive access rights to Azure resources for a given user. For example, if the user under investigation is Jane Smith, Access Analytics calculates all the Azure subscriptions that she can access either directly or transitively via groups or service principals. It also lists all the Azure Active Directory security groups of which Jane is a member. The screenshot below shows a sample row in the UserAccessAnalytics table. **Source entity** is the user or service principal account and **target entity** is the resource that the source entity has access to. The values of **access level** and **access type** depend on the access-control model of the target entity. You can see Jane has Contributor access to Azure subscription *Contoso Azure Production 1*. The access control model of the subscription is RBAC.   

### Hunting queries and exploration queries

Azure Sentinel provides out-of-the-box a set of hunting queries, exploration queries, and a workbook, based on the BehaviorAnalytics table. These tools present enriched data, focused on specific use cases, that indicate anomalous behavior. 

Learn more about [hunting and the investigation graph](./hunting.md) in Azure Sentinel.


## Next steps
In this document, you learned about Azure Sentinel's entity behavior analytics capabilities. For practical guidance on implementation, see the following articles:

- [Enable entity behavior analytics](./aaa.md) in Azure Sentinel.
- [Work with entity pages](./bbb.md).