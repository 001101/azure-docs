---
title: Azure security baseline for Azure Load Balancer
description: The Azure Load Balancer security baseline provides procedural guidance and resources for implementing the security recommendations specified in the Azure Security Benchmark.
author: msmbaldwin
ms.service: load-balancer
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: mbaldwin
ms.custom: security-benchmark

# Important: This content is machine generated; do not modify this topic directly. Contact mbaldwin for more information.

---

# Azure security baseline for Azure Load Balancer

The Azure Security Baseline for Azure Load Balancer contains recommendations that will help you improve the security posture of your deployment. The baseline for this service is drawn from the [Azure Security Benchmark version 1.0](../security/benchmarks/overview.md), which provides recommendations on how you can secure your cloud solutions on Azure with our best practices guidance. For more information, see [Azure Security Baselines overview](../security/benchmarks/security-baselines-overview.md).

>[!WARNING]
>This preview version of the article is for review only. **DO NOT MERGE INTO MASTER!**

## Network security

*For more information, see the [Azure Security Benchmark: Network security](/azure/security/benchmarks/security-control-network-security).*

### 1.1: Protect Azure resources within virtual networks

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32349.).

**Guidance**: An Microsoft Azure Load Balancer operates
at layer four of the Open Systems Interconnection (OSI) model, evenly distributing
load (incoming network traffic) across a group of backend pool instances such
as Azure Virtual Machines or instances in a virtual machine scale set.
Based on how an Load Balancer is deployed, it can be external or internal . An Internal load balancer balances traffic within a VNET, whereas an external load balancers balances traffic to and from an internet connected
endpoint.Internal LBs are secure not
exposed to the internet and will only allow traffic from within the VNET or peered
VNETs of the backend resources. Public load balancers utilize Source Network
Access Translation (SNAT) to masquerade the IP of backend resources to protect
your resources from direct internet exposure. Azure offers two types of
Azure Load Balancer offerings, Standard and Basic. For all
production workloads Standard is recommend as it is secure by default. To
secure resources from the internet it is strongly recommended to use Network
Security Groups (NSGs) to explicitly open ports to specific sources.

Standard Load Balancer is built on the zero trust network security model
and is secure by default. This means the Standard LB is closed to inbound flows
unless opened by Network Security Groups (NSG) to explicitly permit allowed
traffic. If there is not an NSG on a subnet or NIC of your virtual machine
resource, traffic is not allowed to reach this resource.In addition to requiring
NSGs Standard LBs provide outbound rules, which you can use Load Balancer to
define outbound NAT from scratch. Without defining outbound rules to allow
internet traffic, the backend resources will not be able to access the internet
through the Load Balancer to ensure zero trust. You can also use outbound rules to tune the behavior of your outbound connections. 

A Basic Load Balancer is open to open connections to the internet by default, with an NSG as optional. Thus it is only recommended from prototyping and not recommended for production workload. 

- [Outbound connections in Azure](load-balancer-outbound-connections.md#outboundrule)

- [Upgrade Azure Public Load Balancer](upgrade-basic-standard.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 1.2: Monitor and log the configuration and traffic of virtual networks, subnets, and NICs

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32350.).

**Guidance**: The Azure Load Balancer is
a pass-through service and relies on the NSG rules you apply to your backend
resources and the outbound rules configured in your Load Balancer to control internet
access.
Review the outbound rules configured for your Standard LB through the Outbound Rules sub-blade of your LB and the Load Balancing Rules sub-blade where you may have Implicit outbound rules enabled.
You can monitor the count of your outbound connections to track how often your resources are reaching out to the internet. 

In addition use Azure
Security Center and follow the network protection recommendations to help
secure your Azure network resources.

Follow security recommendations for your backend
resources and enable network security group flow logs and send the logs to an
Azure Storage account for auditing.

You can also send the flow logs to a Log
Analytics workspace and then
use Traffic Analytics to provide insights into
traffic patterns in your Azure cloud. Some advantages of Traffic Analytics are
the ability to visualize network activity, identify hot spots and security
threats, understand traffic flow patterns, and pinpoint network
misconfigurations.

- [How to enable network security group flow logs](../network-watcher/network-watcher-nsg-flow-logging-portal.md)

- [How to enable and use Traffic Analytics](../network-watcher/traffic-analytics.md)

Understand network security provided by Azure
- [Security Center](/azure/security-center/security-center-network-recommendationsHow) do I check my outbound connection statistics? : load-balancer-standard-diagnostics.md#how-do-i-check-my-outbound-connection-statistics

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 1.3: Protect critical web applications

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32351.).

**Guidance**: Explicitly define internet connectivity and valid source IPs through outbound rules and Network Security Groups (NSG)s with your Load Balancer to leverage Microsoft's thread intelligence to protect your web applications.

- [Integrate the Azure Firewall](../firewall/integrate-lb.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 1.4: Deny communications with known malicious IP addresses

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32352.).

**Guidance**: Enable distributed denial of service (DDoS) Standard protection on your Azure Virtual Network to guard against DDoS attacks. 

Deploy Azure Firewall at each of the organization's network boundaries with threat intelligence-based filtering enabled and configured to "Alert and deny" for malicious network traffic.

 

Use Azure Security Center  threat protection to detect  communications with known malicious IP addresses. 

The Standard Load Balancer is secure by default and part of a virtual network which is a private and isolated network. This means the Standard Load Balancer is closed to inbound flows unless opened by Network Security Groups (NSG) to explicitly permit allowed traffic, and disallow known malicious IP addresses. If there is not an NSG on a subnet or NIC of your virtual machine resource behind the Load Balancer, traffic is not allowed to reach this resource. 

To prevent attacks from malicious IP addresses:

Deploy Azure Firewall at each of the organization's network boundaries with threat intelligence-based filtering enabled and configured to "Alert and deny" for malicious network traffic.

 

Follow guidance to integrate Azure Firewall with your Azure Load Balancer.

Use Azure Security Center threat protection to detect communications with known malicious IP addresses. 

Use Azure Security Center (Standard Tier) just-in-time virtual machine access, and configure allowed source IP addresses to allow access only from approved IP addresses/ranges.
 

Use Azure Security Center Adaptive Network Hardening to recommend network security group configurations that limit ports and source IPs based on actual traffic and threat intelligence.
 

- [Manage Azure DDoS Protection Standard using the Azure Portal](/azure/virtual-network/manage-ddos`protection)

- [Azure Firewall threat intelligence-based filtering](../firewall/threat-intel.md)

- [Threat protection in Azure Security Center](../security-center/threat-protection.md)

- [Secure your management ports with just-in-time access](../security-center/security-center-just-in-time.md)

- [Adaptive Network Hardening in Azure Security Center](../security-center/security-center-adaptive-network-hardening.md)

- [Integrate Azure Firewall with your Load Balancer](../firewall/overview.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 1.5: Record network packets

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32353.).

**Guidance**: Enable Network Watcher packet capture to investigate anomalous activities.

- [How to create a Network Watcher instance](../network-watcher/network-watcher-create.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 1.6: Deploy network-based intrusion detection/intrusion prevention systems (IDS/IPS)

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32354.).

**Guidance**: Implement an offer from the Azure Marketplace that supports IDS/IPS functionality with payload inspection capabilities to the environment of your Load Balancer. 

When payload inspection is not a requirement, Azure Firewall threat intelligence can be used.

Azure Firewall threat intelligence-based filtering is used to alert on and/or block traffic to and from known malicious IP addresses and domains. The IP addresses and domains are sourced from the Microsoft Threat Intelligence feed.

Deploy the firewall solution of your choice at each of your organization's network boundaries to detect and/or block malicious traffic.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

- [How to deploy Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

- [How to configure alerts with Azure Firewall](../firewall/threat-intel.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 1.7: Manage traffic to web applications

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32355.).

**Guidance**: Explicitly define internet connectivity and valid source IPs through outbound rules and Network Security Groups (NSG)s with your Load Balancer to leverage Microsoft's thread intelligence to protect your web applications.

- [Integrate the Azure Firewall](../firewall/integrate-lb.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 1.8: Minimize complexity and administrative overhead of network security rules

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32356.).

**Guidance**: You can use service tags in place of specific IP addresses when creating security rules. By specifying the service tag name in the source or destination field of a rule, you can allow or deny the traffic for the corresponding service. Microsoft manages the address prefixes encompassed by the service tag and automatically updates the service tag as addresses change. 

By default, every network security group (NSG) includes the service tag AzureLoadBalancer to permit health probe traffic. 

Refer to Azure documentation for all the service tags available for use in network security group rules: 

- [Available service tags](../virtual-network/service-tags-overview.md#available-service-tags)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 1.9: Maintain standard security configurations for network devices

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32357.).

**Guidance**: Define and implement standard security configurations for network resources with Azure Policy.

You can also use Azure Blueprints to simplify large scale Azure deployments by packaging key environment artifacts, such as Azure Resources Manager templates, RBAC controls, and policies, in a single blueprint definition. You can apply the blueprint to new subscriptions, and fine-tune control and management through versioning.

- [How to configure and manage Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Policy samples for networking](../governance/policy/samples/built-in-policies.md#network)

- [How to create an Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 1.10: Document traffic configuration rules

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32358.).

**Guidance**: Use resource tags for network security groups and other resources related to network security and traffic flow. 

For individual network security group rules, use the "Description" field to document the rules that allow traffic to/from a network.

Use any of the built-in Azure Policy definitions related to tagging, such as "Require tag and its value" to ensure that all resources are created with tags and to notify you of existing untagged resources.

You can use Azure PowerShell or Azure CLI to look-up or perform actions on resources based on their tags.

- [How to create and use tags](/azure/azure-resource-manager/resource-group-using-tags)

- [How to create an Azure Virtual Network](../virtual-network/quick-create-portal.md)

- [How to filter network traffic with network security group rules](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 1.11: Use automated tools to monitor network resource configurations and detect changes

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32359.).

**Guidance**: Use Azure Activity log to monitor resource configurations and detect changes to your Azure resources. 

Create alerts in Azure Monitor to notify you when critical resources are changed.

- [How to view and retrieve Azure Activity log events](/azure/azure-monitor/platform/activity-log-view)

- [How to create alerts in Azure Monitor](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

## Logging and monitoring

*For more information, see the [Azure Security Benchmark: Logging and monitoring](/azure/security/benchmarks/security-control-logging-monitoring).*

### 2.2: Configure central security log management

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32361.).

**Guidance**: You can review changes to your outbound rules and NSGs relevant to your Load Balancers by viewing the Activity Log for your subscription and the resource types to Load Balancer and Network Security Group. View the internal host logs to ensure your backend resources are secure.

You can view and export these logs to Log Analytics or another storage platform. In Azure Monitor, use Log Analytics workspaces to query and perform analytics, and use Azure Storage accounts for long term and archival storage.

Alternatively, you can enable and on-board this data to Azure Sentinel or a third-party SIEM.

- [How to onboard Azure Sentinel](../sentinel/quickstart-onboard.md)

- [How to collect platform logs and metrics with Azure Monitor](../azure-monitor/platform/diagnostic-settings.md)

- [How to collect Azure Virtual Machine internal host logs with Azure Monitor](../azure-monitor/learn/quick-collect-azurevm.md)

- [How to get started with Azure Monitor and third-party SIEM integration](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-)

- [Platform Activity logs](../azure-monitor/platform/activity-log.md)) to tools/

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 2.3: Enable audit logging for Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32362.).

**Guidance**: For the Basic Azure Load Balancer, the Control and Management Plane logging and audit information can be captured with Activity logs, which are enabled by default.

You can use Activity logs to monitor actions on resources to view all activity and their status. Through activity logs, you can determine:
what operations were taken on the resources in your subscriptionwho started the operationwhen the operation occurredthe status of the operationthe values of other properties that might help you research the operation

You can  retrieve information from the activity log through Azure PowerShell, the Azure Command Line Interface (CLI), the Azure REST API, or the Azure portal. 

- [It is recommended to read through this article with step-by-step instructions for each method detailed in the Audit operations with Resource Manager article](../azure-resource-manager/management/view-activity-logs.md)

With Power BI, you can use the Azure Audit Logs content pack for Power BI and analyze your data with pre-configured dashboards, or you can customize views to suit your requirements.

The Standard Load Balancer provides multi-dimensional diagnostic capabilities through Azure Monitor for standard load balancer configurations. Available metrics for security include information on Source Network Address Translation (SNAT) connections, ports. Additionally metrics on SYN (synchronize) packets and packet counters are also available. You can also retrieve multi-dimensional metrics programmatically via APIs and they can be written to a storage account via the 'All Metrics' option.

These logs can be streamed to an event hub or a Log Analytics workspace. They can also be extracted from Azure blob storage, and viewed in different tools, such as Excel and Power BI. You can enable and on-board data to Azure Sentinel or a third-party SIEM.

- [Azure Monitor logs for public Basic Load Balancer](load-balancer-monitor-log.md)

- [View activity logs to monitor actions on resources](../azure-resource-manager/management/view-activity-logs.md)

- [Retrieve multi-dimensional metrics programmatically via APIs](load-balancer-standard-diagnostics.md#retrieve-multi-dimensional-metrics-programmatically-via-apis)

- [How to get started with Azure Monitor and third-party SIEM integration](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 2.5: Configure security log storage retention

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32364.).

**Guidance**: The Activity log is enabled by default and is preserved for 90 days in Azure's Event Logs store. Within Azure Monitor, you set your Log Analytics workspace retention period according to your organization's compliance regulations. It is recommended to use Azure Storage accounts for long-term and archival storage.

- [View activity logs to monitor actions on resources article](/azure/azure-resource-manager/resource-group-audit)

- [Change the data retention period in Log Analytics](../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period)

- [How to configure retention policy for Azure Storage account logs](../storage/common/storage-monitor-storage-account.md#configure-logging)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 2.6: Monitor and review Logs

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32365.).

**Guidance**: Both Standard Azure Load Balancer  public and internal, expose per endpoint and backend endpoint health probe status as multi-dimensional metrics through Azure Monitor.  You can monitor, manage, and troubleshoot your standard load balancer resources. The Load Balancer page in the Azure portal and the Resource Health page under  Azure Monitor expose the Resource Health section for Standard Load Balancer

Basic Public Load Balancer exposes health probe status summarized per backend pool via Azure Monitor logs which can be used to check on the public load balancer probe health status and probe count. Azure Monitor logs are not available for internal Basic Load Balancers. 

You can use different types of logs in Azure to manage and troubleshoot Basic Load Balancers. Some of these logs can be accessed through the portal. 

You can view activity logs to monitor actions on resources to view all activity being submitted to your Azure subscription(s), and their status. Activity logs are enabled by default, and can be viewed in the Azure portal.

You can use this log to view alerts raised by the load balancer. The status for the load balancer is collected every five minutes. This log is only written if a load balancer alert event is raised.

You can use this log to view problems detected by your health probe, such as the number of instances in your backend-pool that are not receiving requests from the load balancer because of health probe failures. This log is written to when there is a change in the health probe status.

With Power BI, you can use the Azure Audit Logs content pack for Power BI and analyze your data with pre-configured dashboards, or you can customize views to suit your requirements. Logging can be used with Azure Operational Insights to provide statistics about load balancer health status. 

The Standard Load Balancer provides multi-dimensional diagnostic capabilities through Azure Monitor for standard load balancer configurations. Available metrics for security include information on Source Network Address Translation (SNAT) connections, ports. Additionally metrics on SYN (synchronize) packets and packet counters are also available. You can also retrieve multi-dimensional metrics programmatically via APIs and they can be written to a storage account via the 'All Metrics' option.

These logs can be streamed to an event hub or a Log Analytics workspace. They can also be extracted from Azure blob storage, and viewed in different tools, such as Excel and Power BI. You can enable and on-board data to Azure Sentinel or a third-party SIEM.

- [Load Balancer health probes](load-balancer-custom-probe-overview.md)

- [Azure Monitor REST API](/rest/api/monitor/)

- [How to retrieve metrics via REST API](/rest/api/monitor/metrics/list)

- [Standard Load Balancer diagnostics with metrics, alerts and resource health](load-balancer-standard-diagnostics.md)

- [Azure Monitor logs for public Basic Load Balancer](load-balancer-monitor-log.md)

- [View your load balancer metrics in the Azure portal](load-balancer-standard-diagnostics.md#view-your-load-balancer-metrics-in-the-azure-portal)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 2.7: Enable alerts for anomalous activities

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32366.).

**Guidance**: Use Azure Security Center with Log Analytics workspace for monitoring and alerting on anomalous activity related to Azure Load Balancer as found in security logs and events.

Alternatively, you can enable and on-board data to Azure Sentinel.

- [How to onboard Azure Sentinel](../sentinel/quickstart-onboard.md)

- [How to manage alerts in Azure Security Center](../security-center/security-center-managing-and-responding-alerts.md)

- [How to alert on log analytics log data](../azure-monitor/learn/tutorial-response.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 2.8: Centralize anti-malware logging

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32367.).

**Guidance**: Not applicable to Azure Load Balancer as this recommendation applies to IaaS compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 2.9: Enable DNS query logging

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32368.).

**Guidance**: 
Not applicable as Azure Load Balancer is a core networking service that does not make DNS queries

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 2.10: Enable command-line audit logging

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32369.).

**Guidance**: Not applicable to Azure Load Balancer as this recommendation applies to IaaS compute resources.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

## Identity and access control

*For more information, see the [Azure Security Benchmark: Identity and access control](/azure/security/benchmarks/security-control-identity-access-control).*

### 3.1: Maintain an inventory of administrative accounts

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32370.).

**Guidance**: Azure role-based access control (RBAC) allows you to manage access to Azure resources such as your Azure Load Balancer through role assignments.

You can assign these roles to users, groups service principals and managed identities. 

There are pre-defined built-in roles for certain resources, and these roles can be inventoried or queried through tools such as Azure CLI, Azure PowerShell or the Azure portal.

- [How to get a directory role in Azure AD with PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0)

- [How to get members of a directory role in Azure AD with PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

## Data protection

*For more information, see the [Azure Security Benchmark: Data protection](/azure/security/benchmarks/security-control-data-protection).*

### 4.6: Use Azure RBAC to manage access to resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32388.).

**Guidance**: Use Azure AD (AD) RBAC to control access to your Azure Load Balancer resources.

- [How to configure RBAC in Azure](../role-based-access-control/role-assignments-portal.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 4.7: Use host-based data loss prevention to enforce access control

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32389.).

**Guidance**: Azure Load Balancer is a pass through service which does not store customer data. It is a part of the underlying platform which is managed by Microsoft, Microsoft treats all customer content as sensitive and goes to great lengths to guard against customer data loss and exposure. 

To ensure customer data in Azure remains secure, Microsoft has implemented and maintains a suite of robust data protection controls and capabilities. 

- [Understand customer data protection in Azure](../security/fundamentals/protection-customer-data.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Shared

### 4.9: Log and alert on changes to critical Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32391.).

**Guidance**: Use Azure Monitor with the Azure Activity log to create alerts when changes take place to critical Azure resources such as Azure Load Balancers used for important production workloads.

- [How to create alerts for Azure Activity log events](../azure-monitor/platform/alerts-activity-log.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

## Inventory and asset management

*For more information, see the [Azure Security Benchmark: Inventory and asset management](/azure/security/benchmarks/security-control-inventory-asset-management).*

### 6.1: Use automated asset discovery solution

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32397.).

**Guidance**: Use Azure Resource Graph to query for and discover all resources (such as compute, storage, network, ports, and protocols etc.) in your subscriptions.  

Ensure appropriate (read) permissions in your tenant and enumerate all Azure subscriptions as well as resources in your subscriptions.

Although classic Azure resources may be discovered via Azure Resource Graph Explorer, it is highly recommended to create and use Azure Resource Manager resources going forward.

- [How to create queries with Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

- [How to view your Azure subscriptions](https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0)

- [Understand Azure RBAC](../role-based-access-control/overview.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 6.2: Maintain asset metadata

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32398.).

**Guidance**: 
Apply tags to Azure resources giving metadata to logically organize them into a taxonomy.How to create and use Tags

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 6.3: Delete unauthorized Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32399.).

**Guidance**: Use tagging, management groups, and separate subscriptions where appropriate, to organize and track assets. 

Reconcile inventory on a regular basis and ensure unauthorized resources are deleted from the subscription in a timely manner.

- [How to create additional Azure subscriptions](/azure/billing/billing-create-subscription)

- [How to create management groups](../governance/management-groups/create.md)

- [How to create and use tags](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 6.4: Define and maintain an inventory of approved Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32400.).

**Guidance**: Create an inventory of approved Azure resources such as your Azure Load Balancer and approved software for compute resources as per your organizational needs.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 6.5: Monitor for unapproved Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32401.).

**Guidance**: Use Azure Policy to put restrictions on the type of resources that can be created in your subscriptions.

Use Azure Resource Graph to query for and discover resources within their subscriptions.  Ensure that all Azure resources present in the environment are approved.

- [How to configure and manage Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [How to create queries with Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 6.11: Limit users' ability to interact with Azure Resource Manager

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32407.).

**Guidance**: Use Azure AD Conditional Access to limit users' ability to interact with Azure Resource Manager by configuring "Block access" for the "Microsoft Azure Management" App.

- [How to configure Conditional Access to block access to Azure Resources Manager](../role-based-access-control/conditional-access-azure-management.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 6.13: Physically or logically segregate high risk applications

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32409.).

**Guidance**: Software that is required for business operations, but may incur higher risk for the organization, should be isolated within its own virtual machine and/or virtual network and sufficiently secured with either an Azure Firewall or a network security group.

- [How to create a virtual network](../virtual-network/quick-create-portal.md)

- [How to create an network security group with a security config](../virtual-network/tutorial-filter-network-traffic.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

## Secure configuration

*For more information, see the [Azure Security Benchmark: Secure configuration](/azure/security/benchmarks/security-control-secure-configuration).*

### 7.1: Establish secure configurations for all Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32410.).

**Guidance**: Use Azure Policy aliases to create custom policies to audit or enforce the configuration of your Azure resources. You may also use built-in Azure Policy definitions.

Azure Resource Manager has the ability to export the template in Java Script Object Notation (JSON), which should be reviewed to ensure that the configurations meet the security requirements for your organization.

You can also use the recommendations from Azure Security Center as a secure configuration baseline for your Azure resources.

- [How to view available Azure Policy aliases](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Tutorial: Create and manage policies to enforce compliance](../governance/policy/tutorials/create-and-manage.md)

- [Single and multi-resource export to a template in Azure portal](../azure-resource-manager/templates/export-template-portal.md)

- [Security recommendations - a reference guide](../security-center/recommendations-reference.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### 7.3: Maintain secure Azure resource configurations

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32412.).

**Guidance**: Use Azure Policy [deny] and [deploy if not exist] to enforce secure settings across your Azure resources. As the LB is secure by default this requires you to explicitly define outbound rules and NSG rules to enable internet connectivity.

In addition, you can use Azure Resource Manager templates to maintain the security configuration of your Azure resources required by your organization. 

- [Understand Azure Policy effects](../governance/policy/concepts/effects.md)

- [Create and manage policies to enforce compliance](../governance/policy/tutorials/create-and-manage.md)

- [Azure Resource Manager templates overview](../azure-resource-manager/templates/overview.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 7.5: Securely store configuration of Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32414.).

**Guidance**: Use Azure DevOps to securely store and manage your code like custom Azure Policy definitions, Azure Resource Manager templates and desired state configuration scripts. To access the resources you manage in Azure DevOps, you can grant or deny permissions to specific users, built-in security groups, or groups defined in Azure Active Directory (Azure AD) if integrated with Azure DevOps, or Active Directory if integrated with TFS.

- [How to store code in Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [About permissions and groups in Azure DevOps](/azure/devops/organizations/security/about-permissions)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 7.7: Deploy configuration management tools for Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32416.).

**Guidance**: Define and implement standard security configurations for Azure resources using Azure Policy. 

Use Azure Policy aliases to create custom policies to audit or enforce the network configuration of your Azure resources. 

You can also make use of built-in policy definitions related to your specific resources.  

Additionally, you can use Azure Automation to deploy configuration changes.

- [How to configure and manage Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [How to use aliases](../governance/policy/concepts/definition-structure.md#aliases)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 7.9: Implement automated configuration monitoring for Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32418.).

**Guidance**: Use Azure Security Center to perform baseline scans for your Azure Resources.  

Additionally, use Azure Policy to alert and audit Azure resource configurations.

- [How to remediate recommendations in Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

## Incident response

*For more information, see the [Azure Security Benchmark: Incident response](/azure/security/benchmarks/security-control-incident-response).*

### 10.2: Create an incident scoring and prioritization procedure

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32431.).

**Guidance**: Azure Security Center assigns a severity to each alert to help you prioritize which alerts should be investigated first. 

The severity is based on how confident Security Center is in the finding or the analytic used to issue the alert as well as the confidence level that there was malicious intent behind the activity that led to the alert.

Additionally, mark subscriptions using tags and create a naming system to identify and categorize Azure resources, especially those processing sensitive data.  

It's your responsibility to prioritize the remediation of alerts based on the criticality of the Azure resources and environment where the incident occurred.

- [Security alerts in Azure Security Center](../security-center/security-center-alerts-overview.md)

- [Use tags to organize your Azure resources](/azure/azure-resource-manager/resource-group-using-tags)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 10.5: Incorporate security alerts into your incident response system

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32433.).

**Guidance**: Export your Azure Security Center alerts and recommendations using the continuous export feature to help identify risks to Azure resources. 

Continuous export allows you to export alerts and recommendations either manually or in an ongoing, continuous fashion. 

You can use the Azure Security Center data connector to stream the alerts to Azure Sentinel.

- [How to configure continuous export](../security-center/continuous-export.md)

- [How to stream alerts into Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### 10.6: Automate the response to security alerts

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32434.).

**Guidance**: Use workflow automation feature Azure Security Center to automatically trigger responses to security alerts and recommendations to protect your Azure resources.

- [How to configure workflow automation in Security Center](../security-center/workflow-automation.md)

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

## Next steps

- See the [Azure security benchmark](/azure/security/benchmarks/overview)
- Learn more about [Azure security baselines](/azure/security/benchmarks/security-baselines-overview)
