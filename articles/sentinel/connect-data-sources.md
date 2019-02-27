---
title: Connect data sources to Azure Sentinel Preview?| Microsoft Docs
description: Learn how to connect data sources to Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''

ms.assetid: a3b63cfa-b5fe-4aff-b105-b22b424c418a
ms.service: sentinel
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin

---
# Connect data sources

> [!IMPORTANT]
> Azure Sentinel is currently in public preview.
> This preview version is provided without a service level agreement, and it's not recommended for production workloads. Certain features might not be supported or might have constrained capabilities. 
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).



To on-board Azure Sentinel, you first need to connect to your data sources. Azure Sentinel comes with a number of connectors for Microsoft solutions, available out of the box and providing real-time integration, including Microsoft Threat Protection solutions, and Microsoft 365 sources, including Office 365, Azure AD, Azure ATP, and Microsoft Cloud App Security, and more. In addition, there are built-in connectors to the broader security ecosystem for non-Microsoft solutions. You can also use common event format, Syslog or REST-API to connect your data sources with Azure Sentinel as well.  

## Data collection methods

The following data collection methods are supported by Azure Sentinel:

- **Microsoft services**:<br> Microsoft services are connected natively, leveraging the Azure foundation for out-of-the box integration, the following solutions can be connected in a few clicks:
    - [Office 365](connect-office-365.md)
    - [Azure AD audit logs and sign-ins](connect-azure-active-directory.md)
    - [Azure Activity](connect-azure-activity.md)
    - [Azure AD Identity Protection](connect-azure-ad-Identity-protection.md)
    - [Azure Security Center](connect-azure-security-center.md)
    - [Azure Information Protection](connect-azure-information-protection.md)
    - [Azure Advanced Threat Protection](connect-azure-atp.md)
    - [Cloud App Security](connect-cloud-app-security.md)

- **External solutions via API**: Some data sources are connected using APIs that are provided by the connected data source. Typically, most security technologies provide a set of APIs through which event logs can be retrieved.The APIs connect to Azure Sentinel and gather specific data types and send them to Azure Log Analytics. Appliances connected via API include:
    - [Barracuda](connect-barracuda.md)
    - Symantec
- **External soultions via agent**: Azure Sentinel can be connected to all other data sources that can perform real-time log streaming using the Syslog protocol, via an agent. <br>Most appliances use the Syslog protocol to send event messages that include the log itself and data about the log. The format of the logs varies, but most appliances support the Common Event Format (CEF) standard. <br>The Azure Sentinel agent, which is based on the OMS agent, converts CEF formatted logs into a format that can be ingested by Log Analytics. Depending on the appliance type, the agent is installed either directly on the appliance, or on a dedicated Linux server. The agent for Linux receives events from the Syslog daemon over UDP, but in cases where a Linux machine is expected to collect a high volume of Syslog events, they are sent over TCP from the Syslog daemon to the agent and from there to Log Analytics.
    - Firewalls
        - [F5](connect-f5.md)
        - [Check Point](connect-checkpoint.md)
        - [Cisco ASA](connect-cisco.md)
        - [Fortinet](connect-fortinet.md)
        - [Palo Alto](connect-paloalto.md)
    - Proxy servers
        - [Other CEF appliances](connect-cef.md)
        - [Other Syslog appliances](connect-syslog.md)
    - DLP solutions
    - [Threat intelligence providers](connect-threat-intelligence.md)
    - [DNS machines](connect-dns.md) - agent installed directly on the DNS machine
    
    - 
    - - Windows Servers 
    - Linux servers
    - Windows Event Forwarding
    - Servers and endpoints

- **Integration with other clouds**:
    - [AWS](connect-aws.md)

- **Integration with cloud and on-prem data from**:
    - 


## Next steps

- To get started with Azure Sentinel, you need a subscription to Microsoft Azure. If you do not have a subscription, you can sign up for a [free trial](https://azure.microsoft.com/free/).
- Learn how to [onboard your data to Azure Sentinel](quickstart-onboard.md), and [get visibility into your data, and potential threats](quickstart-get-visibility.md).
