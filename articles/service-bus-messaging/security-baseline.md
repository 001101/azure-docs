---
title: Azure security baseline for Service Bus
description: The Service Bus security baseline provides procedural guidance and resources for implementing the security recommendations specified in the Azure Security Benchmark.
author: msmbaldwin
ms.service: service-bus-messaging
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: mbaldwin
ms.custom: security-benchmark

# Important: This content is machine generated; do not modify this topic directly. Contact mbaldwin for more information.

---

# Azure security baseline for Service Bus

The Azure Security Baseline for Service Bus contains recommendations that will help you improve the security posture of your deployment. The baseline for this service is drawn from the [Azure Security Benchmark version 1.0](../security/benchmarks/overview.md), which provides recommendations on how you can secure your cloud solutions on Azure with our best practices guidance. For more information, see [Azure Security Baselines overview](../security/benchmarks/security-baselines-overview.md).

>[!WARNING]
>This preview version of the article is for review only. **DO NOT MERGE INTO MASTER!**

## Network security

*For more information, see the [Azure Security Benchmark: Network security](/azure/security/benchmarks/security-control-network-security).*

### 1.1: The integration of Service Bus with virtual network service endpoints enables secure access to messaging capabilities from workloads such as virtual machines that are bound to virtual networks, with the network traffic path being secured on both ends.Once bound to at least one virtual network subnet service endpoint, the respective Service Bus namespace no longer accepts traffic from anywhere but authorized subnets in virtual networks. From the virtual network perspective, binding your Service Bus namespace to a service endpoint configures an isolated networking tunnel from the virtual network subnet to the messaging service.You can also create a private endpoint, which is a network interface that connects you privately and securely to Azure Service Bus service by using the Azure Private Link service. The private endpoint uses a private IP address from your VNet, effectively bringing the service into your VNet. All traffic to the service can be routed through the private endpoint, so no gateways, NAT devices, ExpressRoute or VPN connections, or public IP addresses are needed.You can also secure your Azure Service Bus namespace by using firewalls. Azure Service Bus supports IP-based access controls for inbound firewall support. You can set firewall rules by using the Azure portal, Azure Resource Manager templates, or through the Azure CLI or Azure PowerShell.Allow access to Azure Service Bus namespace from specific virtual networks service-bus-service-endpoints.md    Allow access to Azure Service Bus namespaces via private endpoints  private-link-service.md    Allow access to Azure Service Bus namespace from specific IP addresses or ranges  service-bus-ip-filtering.md   

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32074.).

**Guidance**: None.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 1.2: Monitor and log the configuration and traffic of virtual networks, subnets, and network interfaces

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32075.).

**Guidance**: Use Azure Security Center and follow network protection recommendations to help secure your Service Bus resources in Azure. If using Azure virtual machines to access your Service Bus entities, enable network security group (NSG) flow logs and send logs into a storage account for traffic audit.How to Enable NSG Flow Logs: /azure/network-watcher/network-watcher-nsg-flow-logging-portalUnderstanding Network Security provided by Azure Security Center: ../security-center/security-center-network-recommendations.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 1.3: Protect critical web applications

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32076.).

**Guidance**: Not applicable; this recommendation is intended for web applications running on Azure App Service or compute resources.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 1.4: Deny communications with known-malicious IP addresses

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32077.).

**Guidance**: Enable DDoS Protection Standard on the virtual networks associated with your Service Bus namespaces to guard against distributed denial-of-service (DDoS) attacks. Use Azure Security Center Integrated Threat Intelligence to deny communications with known malicious or unused Internet IP addresses.

- [How to configure DDoS protection](../virtual-network/manage-ddos-protection.md)

- [For more information about the Azure Security Center Integrated Threat Intelligence](/azure/security-center/security-center-alerts-service-layer)

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 1.5: Record network packets

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32078.).

**Guidance**: If using Azure virtual machines to access your Service Bus entities, enable network security group (NSG) flow logs and send logs into a storage account for traffic audit. You may also send NSG flow logs to a Log Analytics workspace and use Traffic Analytics to provide insights into traffic flow in your Azure cloud. Some advantages of Traffic Analytics are the ability to visualize network activity and identify hot spots, identify security threats, understand traffic flow patterns, and pinpoint network misconfigurations.If required for investigating anomalous activity, enable Network Watcher packet capture.How to Enable NSG Flow Logs: /azure/network-watcher/network-watcher-nsg-flow-logging-portalHow to Enable and use Traffic Analytics: /azure/network-watcher/traffic-analyticsHow to enable Network Watcher: ../network-watcher/network-watcher-create.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 1.6: Deploy network-based intrusion detection/intrusion prevention systems (IDS/IPS)

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32079.).

**Guidance**: If using Azure virtual machines to access your Service Bus entities, select an offer from the Azure Marketplace that supports IDS/IPS functionality with payload inspection capabilities. If intrusion detection and/or prevention based on payload inspection is not required for your organization, you may use Azure Service Bus's built-in firewall feature. You can limit access to your Service Bus namespace for a limited range of IP addresses, or a specific IP address by using Firewall rules.Azure Marketplace:https://azuremarketplace.microsoft.com/marketplace/?term=FirewallHow to add a firewall rule in Service Bus namespaces for a specified IP address:service-bus-ip-filtering.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 1.7: Manage traffic to web applications

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32080.).

**Guidance**: Not applicable; this recommendation is intended for web applications running on Azure App Service or compute resources.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 1.8: Minimize complexity and administrative overhead of network security rules

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32081.).

**Guidance**: Not applicable, this recommendation is intended for web applications running on Azure App Service or compute resources.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 1.9: Maintain standard security configurations for network devices

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32082.).

**Guidance**: Define and implement standard security configurations for network resources associated with your Azure Service Bus namespaces with Azure Policy. Use Azure Policy aliases in the "Microsoft.ServiceBus" and "Microsoft.Network" namespaces to create custom policies to audit or enforce the network configuration of your Service Bus namespaces. You may also make use of built-in policy definitions related to Azure Service Bus, such as:

Service Bus should use a virtual network service endpointDiagnostic logs in Service Bus should be enabled

- [How to configure and manage Azure Policy](../governance/policy/tutorials/create-and-manage.md)

- [Azure Built-in Policy for Service Bus namespace](policy-samples.md#azure-service-bus-messaging)

- [Azure Policy samples for networking](../governance/policy/samples/built-in-policies.md#network)

- [How to create an Azure Blueprint](../governance/blueprints/create-blueprint-portal.md)

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 1.10: Document traffic configuration rules

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32083.).

**Guidance**: Use tags for virtual networks and other resources related to network security and traffic flow that are associated with your Service Bus entities. 

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 1.11: Use automated tools to monitor network resource configurations and detect changes

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32084.).

**Guidance**: Use Azure Activity Log to monitor network resource configurations and detect changes for network resources related to Azure Event Hubs. Create alerts within Azure Monitor that will trigger when changes to critical network resources take place.How to view and retrieve Azure Activity Log events: /azure/azure-monitor/platform/activity-log-viewHow to create alerts in Azure Monitor: ../azure-monitor/platform/alerts-activity-log.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

## Logging and monitoring

*For more information, see the [Azure Security Benchmark: Logging and monitoring](/azure/security/benchmarks/security-control-logging-monitoring).*

### 2.2: Configure central security log management

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32086.).

**Guidance**: Within Azure Monitor, configure logs related to Service Bus entities within the Activity Log and Service Bus diagnostic settings to send logs into a Log Analytics workspace to be queried or into a storage account for long-term archival storage.How to configure Diagnostic Settings for Azure Service Bus: service-bus-diagnostic-logs.md
- [Understanding Azure Activity Log](../azure-monitor/platform/platform-logs-overview.md)

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 2.3: Enable audit logging for Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32087.).

**Guidance**: Enable Diagnostic settings for your Azure Service Bus namespace. Azure Service Bus currently supports activity and diagnostic logs. Activity logs have information about operations done on a job. Diagnostic logs provide richer information about operations and actions that are done against your namespace by using API, or through management clients using the language SDK. Specifically, these logs capture the operation type, including queue creation, resources used, and the status of the operation.How to enable Diagnostic Settings for Azure Service Bus: service-bus-diagnostic-logs.md
- [How to enable Diagnostic Settings for Azure Activity Log](/azure/azure-monitor/platform/diagnostic-settings-legacy)

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 2.4: Collect security logs from operating systems

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32088.).

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 2.5: Configure security log storage retention

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32089.).

**Guidance**: Within Azure Monitor, set your Log Analytics workspace retention period according to your organization's compliance regulations to capture and review Service Bus-related incidents.How to set log retention parameters for Log Analytics workspaces: ../azure-monitor/platform/manage-cost-storage.md#change-the-data-retention-period

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 2.6: Monitor and review logs

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32090.).

**Guidance**: Analyze and monitor logs for anomalous behavior and regularly review results related to your Service Bus entities. Use Azure Monitor's Log Analytics to review logs and perform queries on log data. Alternatively, you may enable and on-board data to Azure Sentinel or a third party SIEM.For more information about the Log Analytics workspace: /azure/azure-monitor/log-query/get-started-portalHow to perform custom queries in Azure Monitor: /azure/azure-monitor/log-query/get-started-queriesHow to onboard Azure Sentinel: ../sentinel/quickstart-onboard.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 2.7: Enable alerts for anomalous activities

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32091.).

**Guidance**: Within Azure Monitor, configure logs related to Azure Service Bus within the Activity Log, and Service Bus diagnostic settings to send logs into a Log Analytics workspace to be queried or into a storage account for long-term archival storage. Use Log Analytics workspace to create alerts for anomalous activity found in security logs and events.Alternatively, you may enable and on-board data to Azure Sentinel.Understand the Azure Activity Log: ../azure-monitor/platform/platform-logs-overview.mdHow to configure Diagnostic Settings for Azure Service Bus: service-bus-diagnostic-logs.mdHow to alert on Log Analytics workspace log data: /azure/azure-monitor/learn/tutorial-responseHow to onboard Azure Sentinel: ../sentinel/quickstart-onboard.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 2.8: Centralize anti-malware logging

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32092.).

**Guidance**: Not applicable; Event Hub does not process anti-malware logging.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 2.9: Enable DNS query logging

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32093.).

**Guidance**: Not applicable; Event Hubs does not process or produce DNS related logs.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 2.10: Enable command-line audit logging

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32094.).

**Guidance**: Not applicable; this guideline is intended for compute resources.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

## Identity and access control

*For more information, see the [Azure Security Benchmark: Identity and access control](/azure/security/benchmarks/security-control-identity-access-control).*

### 3.1: Maintain an inventory of administrative accounts

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32095.).

**Guidance**: Azure Active Directory (AD) has built-in roles that must be explicitly assigned and are queryable. Use the Azure AD PowerShell module to perform ad hoc queries to discover accounts that are members of administrative groups.Built-in roles for Azure Service Bus
authenticate-application.md#azure-built-in-roles-for-azure-service-bus

- [How to get a directory role in Azure AD with PowerShell](https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrole?view=azureadps-2.0How) to get members of a directory role in Azure AD with PowerShell: https://docs.microsoft.com/powershell/module/azuread/get-azureaddirectoryrolemember?view=azureadps-2.0

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 3.2: Change default passwords where applicable

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32096.).

**Guidance**: Control plane access to Service Bus is controlled through Azure Active Directory (AD). Azure AD does not have the concept of default passwords.Data plane access to Service Bus is controlled through Azure AD with Managed Identities or App registrations as well as shared access signatures. Shared access signatures are used by the clients connecting to your Service Bus namespace and can be regenerated at any time.Understand shared access signatures for Service Bus: service-bus-sas.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 3.3: Use dedicated administrative accounts

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32097.).

**Guidance**: Create standard operating procedures around the use of dedicated administrative accounts. Use Azure Security Center Identity and Access Management to monitor the number of administrative accounts.Additionally, to help you keep track of dedicated administrative accounts, you may use recommendations from Azure Security Center or built-in Azure Policies, such as:There should be more than one owner assigned to your subscriptionDeprecated accounts with owner permissions should be removed from your subscriptionExternal accounts with owner permissions should be removed from your subscriptionHow to use Azure Security Center to monitor identity and access (Preview): /azure/security-center/security-center-identity-accessHow to use Azure Policy: ../governance/policy/tutorials/create-and-manage.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 3.4: Use Azure Active Directory single sign-on (SSO)

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32098.).

**Guidance**: Microsoft Azure provides integrated access control management for resources and applications based on Azure Active Directory (AD). A key advantage of using Azure AD with Azure Service Bus is that you don't need to store your credentials in the code anymore. Instead, you can request an OAuth 2.0 access token from the Microsoft Identity platform. The resource name to request a token is https://servicebus.azure.net/. Azure AD authenticates the security principal (a user, group, or service principal) running the application. If the authentication succeeds, Azure AD returns an access token to the application, and the application can then use the access token to authorize request to Azure Service Bus resources.How to authenticate an application with Azure AD to access Service Bus resources: authenticate-application.mdUnderstanding SSO with Azure AD: ../active-directory/manage-apps/what-is-single-sign-on.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 3.5: Use multi-factor authentication for all Azure Active Directory-based access

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32099.).

**Guidance**: Enable Azure Active Directory Multi-Factor Authentication (MFA) and follow Azure Security Center Identity and access management recommendations to help protect your Service Bus-enabled resources.How to enable MFA in Azure: /azure/active-directory/authentication/howto-mfa-getstartedHow to monitor identity and access within Azure Security Center: ../security-center/security-center-identity-access.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 3.6: Use secure, Azure-managed workstations for administrative tasks

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32100.).

**Guidance**: Use privileged access workstations (PAW) with Multi-Factor Authentication (MFA) configured to log into and configure Service Bus-enabled resources.Learn about Privileged Access Workstations: /windows-server/identity/securing-privileged-access/privileged-access-workstationsHow to enable MFA in Azure: ../active-directory/authentication/howto-mfa-getstarted.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 3.7: Log and alert on suspicious activities from administrative accounts

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32101.).

**Guidance**: Use Azure Active Directory (AD) Privileged Identity Management (PIM) for generation of logs and alerts when suspicious or unsafe activity occurs in the environment. Use Azure AD risk detections to view alerts and reports on risky user behavior. For additional logging, send Azure Security Center risk detection alerts into Azure Monitor and configure custom alerting/notifications using action groups.

- [How to deploy Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-deployment-plan.md)

- [Understand Azure AD risk detections](/azure/active-directory/reports-monitoring/concept-risk-events)

- [How to configure action groups for custom alerting and notification](../azure-monitor/platform/action-groups.md)

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 3.8: Manage Azure resources from only approved locations

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32102.).

**Guidance**: Use Conditional Access Named Locations to allow access from only specific logical groupings of IP address ranges or countries/regions.

- [How to configure Named Locations in Azure](../active-directory/reports-monitoring/quickstart-configure-named-locations.md)

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 3.9: Use Azure Active Directory

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32103.).

**Guidance**: Use Azure Active Directory (AD) as the central authentication and authorization system for Azure resources such as Service Bus. This allows for role-based access control (RBAC) to administrative sensitive resources.How to create and configure an Azure AD instance: /azure/active-directory/fundamentals/active-directory-access-create-new-tenantTo learn about how Azure Service Bus integrates with Azure Active Directory (AAD), see Authorize access to Service Bus resources using Azure Active Directory: authenticate-application.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 3.10: Regularly review and reconcile user access

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32104.).

**Guidance**: Azure Active Directory (AD) provides logs to help you discover stale accounts. In addition, use Azure Identity Access Reviews to efficiently manage group memberships, access to enterprise applications, and role assignments. User access can be reviewed on a regular basis to make sure only the right Users have continued access.In additional, regularly rotate your Service Bus namespace's shared access signature.Understand Azure AD reporting: /azure/active-directory/reports-monitoring/How to use Azure Identity Access Reviews: /azure/active-directory/governance/access-reviews-overviewUnderstanding shared access signatures for Service Bus namespace: service-bus-sas.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 3.11: Monitor attempts to access deactivated credentials

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32105.).

**Guidance**: You have access to Azure Active Directory (AD) sign-in activity, audit and risk event log sources, which allow you to integrate with any SIEM/Monitoring tool.You can streamline this process by creating diagnostic settings for Azure AD user accounts and sending the audit logs and sign-in logs to a Log Analytics workspace. You can configure desired log alerts within Log Analytics.How to integrate Azure Activity Logs into Azure Monitor: /azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analyticsAuthorize access to Service Bus resources using Azure Active Directory: authenticate-application.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 3.12: Alert on account sign-in behavior deviation

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32106.).

**Guidance**: Use Azure Active Directory's Identity Protection and risk detection features to configure automated responses to detected suspicious actions related to your Service Bus-enabled resources. You should enable automated responses through Azure Sentinel to implement your organization's security responses.How to view Azure AD risky sign-ins: /azure/active-directory/reports-monitoring/concept-risky-sign-insHow to configure and enable Identity Protection risk policies: /azure/active-directory/identity-protection/howto-identity-protection-configure-risk-policiesHow to onboard Azure Sentinel: ../sentinel/quickstart-onboard.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 3.13: Provide Microsoft with access to relevant customer data during support scenarios

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32107.).

**Guidance**: urrently not available; Customer Lockbox is not yet supported for Event Hubs.List of Customer Lockbox-supported services: ../security/fundamentals/customer-lockbox-overview.md#supported-services-and-scenarios-in-general-availability

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

## Data protection

*For more information, see the [Azure Security Benchmark: Data protection](/azure/security/benchmarks/security-control-data-protection).*

### 4.1: Maintain an inventory of sensitive Information

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32108.).

**Guidance**: Use tags on resources related to your Event Hubs to assist in tracking Azure resources that store or process sensitive information.How to create and use Tags: /azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 4.2: Isolate systems storing or processing sensitive information

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32109.).

**Guidance**: Implement separate subscriptions and/or management groups for development, test, and production. Service Bus namespaces should be separated by virtual network with service endpoints enabled and tagged appropriately.You may also secure your Azure Service Bus namespace by using firewalls. Azure Service Bus supports IP-based access controls for inbound firewall support. You can set firewall rules by using the Azure portal, Azure Resource Manager templates, or through the Azure CLI or Azure PowerShell.How to create additional Azure subscriptions: /azure/billing/billing-create-subscriptionHow to create Management Groups: /azure/governance/management-groups/createConfigure IP firewall rules for Azure Service Bus namespaces: service-bus-ip-filtering.md
- [How to create and utilize tags](/azure/azure-resource-manager/resource-group-using-tagsHow) to create a Virtual Network: ../virtual-network/quick-create-portal.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 4.3: Monitor and block unauthorized transfer of sensitive information

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32110.).

**Guidance**: When using virtual machines to access your Service Bus entities, make use of virtual networks, service endpoints, Service Bus firewall, network security groups, and service tags to mitigate the possibility of data exfiltration.Microsoft manages the underlying infrastructure for Azure Service Bus and has implemented strict controls to prevent the loss or exposure of customer data.Configure IP firewall rules for Azure Service Bus namespaces: service-bus-ip-filtering.mdAllow access to Azure Service Bus namespace from specific virtual networks
service-bus-service-endpoints.md

Allow access to Azure Service Bus namespaces via private endpoints

- [private-link-service.mdUnderstand Network Security Groups and Service Tags](/azure/virtual-network/security-overviewUnderstand) customer data protection in Azure: ../security/fundamentals/protection-customer-data.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Shared

### 4.4: Encrypt all sensitive information in transit

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32111.).

**Guidance**: Azure Service Bus enforces TLS-encrypted communications by default. TLS versions 1.0, 1.1 and 1.2 are currently supported. However, TLS 1.0 and 1.1 are on a path to deprecation industry-wide, so use TLS 1.2 if at all possible.To understand security features of Service Bus, see Network security: network-security.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Shared

### 4.5: Use an active discovery tool to identify sensitive data

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32112.).

**Guidance**: Data identification, classification, and loss prevention features are not yet available for Azure Service Bus. Implement third-party solution if required for compliance purposes.For the underlying platform which is managed by Microsoft, Microsoft treats all customer content as sensitive and goes to great lengths to guard against customer data loss and exposure. To ensure customer data within Azure remains secure, Microsoft has implemented and maintains a suite of robust data protection controls and capabilities.Understand customer data protection in Azure: ../security/fundamentals/protection-customer-data.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Shared

### 4.6: Use Role-based access control to control access to resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32113.).

**Guidance**: Azure Service Bus supports using Azure Active Directory (AD) to authorize requests to Service Bus entities. With Azure AD, you can use role-based access control (RBAC) to grant permissions to a security principal, which may be a user, or an application service principal.Understand Azure AD RBAC and available roles for Azure Service Bus: authenticate-application.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 4.7: Use host-based data loss prevention to enforce access control

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32114.).

**Guidance**: Not applicable; this guideline is intended for compute resources.Microsoft manages the underlying infrastructure for Service Bus and has implemented strict controls to prevent the loss or exposure of customer data.Understand customer data protection in Azure: ../security/fundamentals/protection-customer-data.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Shared

### 4.8: Encrypt sensitive information at rest

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32115.).

**Guidance**: Azure Service Bus supports the option of encrypting data at rest with either Microsoft-managed keys or customer-managed keys. This feature enables you to create, rotate, disable, and revoke access to the customer-managed keys that are used for encrypting Azure Service Bus data at rest.How to configure customer-managed keys for encrypting Azure Service Bus : configure-customer-managed-key.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 4.9: Log and alert on changes to critical Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32116.).

**Guidance**: Use Azure Monitor with the Azure Activity log to create alerts for when changes take place to production instances of Azure Service Bus and other critical or related resources.How to create alerts for Azure Activity Log events: ../azure-monitor/platform/alerts-activity-log.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

## Vulnerability management

*For more information, see the [Azure Security Benchmark: Vulnerability management](/azure/security/benchmarks/security-control-vulnerability-management).*

### 5.1: Run automated vulnerability scanning tools

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32117.).

**Guidance**: Not applicable; Microsoft performs vulnerability management on the underlying systems that support Service Bus.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 5.2: Deploy automated operating system patch management solution

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32118.).

**Guidance**: Not applicable; Microsoft performs patch management on the underlying systems that support Service Bus.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 5.3: Deploy automated patch management solution for third-party software titles

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32119.).

**Guidance**: Not applicable; benchmark is intended for compute resources.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 5.4: Compare back-to-back vulnerability scans

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32120.).

**Guidance**: Not applicable; Microsoft performs vulnerability management on the underlying systems that support Service Bus.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 5.5: Use a risk-rating process to prioritize the remediation of discovered vulnerabilities

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32121.).

**Guidance**: Not applicable; Microsoft performs vulnerability management on the underlying systems that support Service Bus.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

## Inventory and asset management

*For more information, see the [Azure Security Benchmark: Inventory and asset management](/azure/security/benchmarks/security-control-inventory-asset-management).*

### 6.1: Use automated asset discovery solution

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32122.).

**Guidance**: Use Azure Resource Graph to query and discover all resources (including Azure Service Bus namespaces) within your subscription(s). Ensure you have appropriate (read) permissions in your tenant and are able to enumerate all Azure subscriptions as well as resources within your subscriptions.How to create queries with Azure Resource Graph: /azure/governance/resource-graph/first-query-portalHow to view your Azure Subscriptions: https://docs.microsoft.com/powershell/module/az.accounts/get-azsubscription?view=azps-3.0.0Understand Azure RBAC: ../role-based-access-control/overview.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 6.2: Maintain asset metadata

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32123.).

**Guidance**: Apply tags to Azure resources giving metadata to logically organize them into a taxonomy.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 6.3: Delete unauthorized Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32124.).

**Guidance**: Use tagging, management groups, and separate subscriptions, where appropriate, to organize and track Azure Service Bus namespaces and related resources. Reconcile inventory on a regular basis and ensure unauthorized resources are deleted from the subscription in a timely manner.How to create additional Azure subscriptions: /azure/billing/billing-create-subscriptionHow to create Management Groups: /azure/governance/management-groups/createHow to create and use Tags: /azure/azure-resource-manager/resource-group-using-tags

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 6.4: Define and maintain inventory of approved Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32125.).

**Guidance**: Not applicable; this recommendation is intended for compute resources and Azure as a whole.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 6.5: Monitor for unapproved Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32126.).

**Guidance**: Use Azure Policy to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:Not allowed resource typesAllowed resource typesIn addition, use Azure Resource Graph to query/discover resources within the subscription(s).How to configure and manage Azure Policy: ../governance/policy/tutorials/create-and-manage.mdHow to create queries with Azure Graph: ../governance/resource-graph/first-query-portal.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 6.6: Monitor for unapproved software applications within compute resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32127.).

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 6.7: Remove unapproved Azure resources and software applications

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32128.).

**Guidance**: Not applicable; this recommendation is intended for compute resources and Azure as a whole.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 6.8: Use only approved applications

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32129.).

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 6.9: Use only approved Azure services

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32130.).

**Guidance**: Use Azure Policy to put restrictions on the type of resources that can be created in customer subscription(s) using the following built-in policy definitions:Not allowed resource typesAllowed resource typesHow to configure and manage Azure Policy: ../governance/policy/tutorials/create-and-manage.mdHow to deny a specific resource type with Azure Policy: /azure/governance/policy/samples/not-allowed-resource-types

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 6.10: Maintain an inventory of approved software titles

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32131.).

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 6.11: Limit users' ability to interact with Azure Resource Manager

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32132.).

**Guidance**: Configure Azure Conditional Access to limit users' ability to interact with Azure Resource Manager by configuring "Block access" for the "Microsoft Azure Management" App.How to configure Conditional Access to block access to Azure Resource Manager: ../role-based-access-control/conditional-access-azure-management.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 6.12: Limit users' ability to execute scripts within compute resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32133.).

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 6.13: Physically or logically segregate high risk applications

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32134.).

**Guidance**:  Not applicable; this recommendation is intended for web applications running on Azure App Service or compute resources.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

## Secure configuration

*For more information, see the [Azure Security Benchmark: Secure configuration](/azure/security/benchmarks/security-control-secure-configuration).*

### 7.1: Establish secure configurations for all Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32135.).

**Guidance**: Define and implement standard security configurations for your Azure Service Bus deployments. Use Azure Policy aliases in the "Microsoft.ServiceBus" namespace to create custom policies to audit or enforce configurations. You may also make use of built-in policy definitions for Azure Service Bus such as:

Diagnostic logs in Service Bus should be enabledService Bus should use a virtual network service endpoint

- [Azure Built-in policies for Service Bus ](policy-samples.md)

- [How to view available Azure Policy aliases](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [How to configure and manage Azure Policy](../governance/policy/tutorials/create-and-manage.md)

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 7.2: Establish secure operating system configurations

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32136.).

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 7.3: Maintain secure Azure resource configurations

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32137.).

**Guidance**: Use Azure Policy [deny] and [deploy if not exist] to enforce secure settings across your Service Bus-enabled resources or applications.How to configure and manage Azure Policy: ../governance/policy/tutorials/create-and-manage.mdFor more information about the Azure Policy Effects: ../governance/policy/concepts/effects.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 7.4: Maintain secure operating system configurations

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32138.).

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Shared

### 7.5: Securely store configuration of Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32139.).

**Guidance**: If using custom Azure Policy definitions for Service Bus or related resources, use Azure Repos to securely store and manage your code.How to store code in Azure DevOps: https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devopsAzure Repos Documentation: https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 7.6: Securely store custom operating system images

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32140.).

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 7.7: Deploy configuration management tools for Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32141.).

**Guidance**: Use Azure Policy aliases in the "Microsoft.ServiceBus" namespace to create custom policies to alert, audit, and enforce system configurations. Additionally, develop a process and pipeline for managing policy exceptions.How to configure and manage Azure Policy: ../governance/policy/tutorials/create-and-manage.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 7.8: Deploy configuration management tools for operating systems

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32142.).

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 7.9: Implement automated configuration monitoring for Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32143.).

**Guidance**: Use Azure Policy aliases in the "Microsoft.ServiceBus" namespace to create custom policies to alert, audit, and enforce system configurations. Use Azure Policy [audit], [deny], and [deploy if not exist] to automatically enforce configurations for your Azure Event Hubs deployments and related resources.How to configure and manage Azure Policy: ../governance/policy/tutorials/create-and-manage.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 7.10: Implement automated configuration monitoring for operating systems

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32144.).

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 7.11: Manage Azure secrets securely

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32145.).

**Guidance**:  For Azure virtual machines or web applications running on Azure App Service being used to access your Service Bus entities, use Managed Service Identity in conjunction with Azure Key Vault to simplify and secure shared access signature management for your Azure Service Bus deployments. Ensure Key Vault soft-delete is enabled.Authenticate a managed identity with Azure Active Directory to access Service Bus resources: /azure/service-bus-messaging/service-bus-managed-service-identityConfigure customer-managed keys for Service Bus: configure-customer-managed-key.mdHow to create a Key Vault: /azure/key-vault/quick-create-portal

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 7.12: Manage identities securely and automatically

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32146.).

**Guidance**: For Azure virtual machines or web applications running on Azure App Service being used to access your Service Bus entities, use Managed Service Identity in conjunction with Azure Key Vault to simplify and secure Azure Service Bus. Ensure Key Vault soft-delete is enabled.Use Managed Identities to provide Azure services with an automatically managed identity in Azure Active Directory (AD). Managed Identities allows you to authenticate to any service that supports Azure AD authentication, including Azure Key Vault, without any credentials in your code.Authenticate a managed identity with Azure Active Directory to access Service Bus Resources: /azure/service-bus-messaging/service-bus-managed-service-identityConfigure customer-managed keys for Service Bus: configure-customer-managed-key.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 7.13: Eliminate unintended credential exposure

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32147.).

**Guidance**: Implement Credential Scanner to identify credentials within code. Credential Scanner will also encourage moving discovered credentials to more secure locations such as Azure Key Vault.How to setup Credential Scanner: https://secdevtools.azurewebsites.net/helpcredscan.html

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

## Malware defense

*For more information, see the [Azure Security Benchmark: Malware defense](/azure/security/benchmarks/security-control-malware-defense).*

### 8.1: Use centrally-managed anti-malware software

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32148.).

**Guidance**: Not applicable; this recommendation is intended for compute resources.Microsoft anti-malware is enabled on the underlying host that supports Azure services (for example, Azure App Service), however it does not run on customer content.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 8.2: Pre-scan files to be uploaded to non-compute Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32149.).

**Guidance**: Pre-scan any content being uploaded to non-compute Azure resources, such as Azure Service Bus, App Service, Data Lake Storage, Blob Storage, Azure Database for PostgreSQL, etc. Microsoft cannot access your data in these instances.Microsoft anti-malware is enabled on the underlying host that supports Azure services (for example, Azure Cache for Redis), however it does not run on customer content.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 8.3: Ensure anti-malware software and signatures are updated

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32150.).

**Guidance**: Not applicable; this recommendation is intended for compute resources.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

## Data recovery

*For more information, see the [Azure Security Benchmark: Data recovery](/azure/security/benchmarks/security-control-data-recovery).*

### 9.1: Ensure regular automated back-ups

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32151.).

**Guidance**: Configure geo-disaster recovery for Azure Service Bus. When entire Azure regions or datacenters (if no availability zones are used) experience downtime, it is critical for data processing to continue to operate in a different region or datacenter. As such, Geo-disaster recovery and Geo-replication are important features for any enterprise. Azure Service Bus supports both geo-disaster recovery and geo-replication, at the namespace level.Understand geo-disaster recovery for Azure Service Bus: service-bus-geo-dr.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 9.2: Perform complete system backups and backup any customer-managed keys

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32152.).

**Guidance**: Azure Service Bus provides encryption of data at rest with Azure Storage Service Encryption (Azure SSE). Service Bus relies on Azure Storage to store the data and by default, all the data that is stored with Azure Storage is encrypted using Microsoft-managed keys. If you use Azure Key Vault for storing customer-managed keys, ensure regular automated backups of your Keys.Ensure regular automated backups of your Key Vault Secrets with the following PowerShell command: Backup-AzKeyVaultSecretHow to configure customer-managed keys for encrypting Azure Service Bus data at rest: configure-customer-managed-key.md
- [How to backup Key Vault Secrets](/powershell/module/azurerm.keyvault/backup-azurekeyvaultsecret)

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 9.3: Validate all backups including customer-managed keys

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32153.).

**Guidance**: Test restoration of backed up customer managed keys.How to restore key vault keys in Azure: https://docs.microsoft.com/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey?view=azurermps-6.13.0

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 9.4: Ensure protection of backups and customer-managed keys

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32154.).

**Guidance**: Enable soft-delete in Key Vault to protect keys against accidental or malicious deletion. Azure Service Bus requires customer-managed keys to have Soft Delete and Do Not Purge configured.How to enable soft-delete in Key Vault: https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete?tabs=azure-portal
- [Set up a key vault with keys](../event-hubs/configure-customer-managed-key.md)

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

## Incident response

*For more information, see the [Azure Security Benchmark: Incident response](/azure/security/benchmarks/security-control-incident-response).*

### 10.1: Create an incident response guide

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32155.).

**Guidance**: Ensure that there are written incident response plans that defines roles of personnel as well as phases of incident handling/management.How to configure Workflow Automations within Azure Security Center: ../security-center/security-center-planning-and-operations-guide.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 10.2: Create an incident scoring and prioritization procedure

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32156.).

**Guidance**: ecurity Center assigns a severity to alerts, to help you prioritize the order in which you attend to each alert, so that when a resource is compromised, you can get to it right away. The severity is based on how confident Security Center is in the finding or the analytic used to issue the alert as well as the confidence level that there was malicious intent behind the activity that led to the alert.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 10.3: Test security response procedures

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32161.).

**Guidance**: Conduct exercises to test your systems' incident response capabilities on a regular cadence. Identify weak points and gaps and revise plan as needed.Refer to NIST's publication: Guide to Test, Training, and Exercise Programs for IT Plans and Capabilities: https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 10.4: Provide security incident contact details and configure alert notifications for security incidents

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32157.).

**Guidance**: Security incident contact information will be used by Microsoft to contact you if the Microsoft Security Response Center (MSRC) discovers that the customer's data has been accessed by an unlawful or unauthorized party. Review incidents after the fact to ensure that issues are resolved.How to set the Azure Security Center Security Contact: ../security-center/security-center-provide-security-contact-details.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 10.5: Incorporate security alerts into your incident response system

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32158.).

**Guidance**: Export your Azure Security Center alerts and recommendations using the Continuous Export feature. Continuous Export allows you to export alerts and recommendations either manually or in an ongoing, continuous fashion. You may use the Azure Security Center data connector to stream the alerts Sentinel.How to configure continuous export: /azure/security-center/continuous-exportHow to stream alerts into Azure Sentinel: ../sentinel/connect-azure-security-center.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

### 10.6: Automate the response to security alerts

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32159.).

**Guidance**: Use the Workflow Automation feature in Azure Security Center to automatically trigger responses via "Logic Apps" on security alerts and recommendations.How to configure Workflow Automation and Logic Apps: ../security-center/workflow-automation.md

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Customer

## Penetration tests and red team exercises

*For more information, see the [Azure Security Benchmark: Penetration tests and red team exercises](/azure/security/benchmarks/security-control-penetration-tests-red-team-exercises).*

### 11.1: Conduct regular penetration testing of your Azure resources and ensure remediation of all critical security findings

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/32160.).

**Guidance**: Please follow the Microsoft Rules of Engagement to ensure your Penetration Tests are not in violation of Microsoft policies: https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1. You can find more information on Microsoft's strategy and execution of Red Teaming and live site penetration testing against Microsoft managed cloud infrastructure, services and applications, here: https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Shared

## Next steps

- See the [Azure security benchmark](/azure/security/benchmarks/overview)
- Learn more about [Azure security baselines](/azure/security/benchmarks/security-baselines-overview)
