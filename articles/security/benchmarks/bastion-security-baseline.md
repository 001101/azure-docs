---
title: Azure security baseline for Azure Bastion
description: The Azure Bastion security baseline provides procedural guidance and resources for implementing the security recommendations specified in the Azure Security Benchmark.
author: msmbaldwin
ms.service: azure-bastion
ms.topic: conceptual
ms.date: 01/01/2000
ms.author: mbaldwin
ms.custom: subject-security-benchmark

# Important: This content is machine generated; do not modify this topic directly. Contact mbaldwin for more information.

---

# Azure security baseline for Azure Bastion

This security baseline applies guidance from the [Azure Security Benchmark version 2.0](overview.md) to Azure Bastion. The Azure Security Benchmark provides recommendations on how you can secure your cloud solutions on Azure. The content is grouped by the **security controls** defined by the Azure Security Benchmark and the related guidance applicable to Azure Bastion. **Controls** not applicable to Azure Bastion have been excluded.

To see how Azure Bastion completely maps to the Azure Security Benchmark, see the [full Azure Bastion security baseline mapping file](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

>[!WARNING]
>This preview version of the article is for review only. **DO NOT MERGE INTO MASTER!**

## Network Security

*For more information, see the [Azure Security Benchmark: Network Security](/azure/security/benchmarks/security-controls-v2-network-security).*

### NS-1: Implement security for internal traffic

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39744.).

**Guidance**: 

For the network security groups (NSGs) associated with your Azure Bastion subnets, you must allow

Ingress Traffic : 

a) Ingress Traffic from public internet: The Azure Bastion will create a public IP that needs port 443 enabled on the public IP for ingress traffic. Port 3389/22 are NOT required to be opened on the AzureBastionSubnet. 

b) Ingress Traffic from Azure Bastion control plane: For control plane connectivity, enable port 443 inbound from GatewayManager service tag. This enables the control plane, that is, Gateway Manager to be able to communicate with Azure Bastion.

Egress Traffic:

a) Egress Traffic to target VMs: Azure Bastion will reach the target VMs over private IP. The NSGs need to allow egress traffic to other target VM subnets for port 3389 and 22.

b) Egress Traffic to other public endpoints in Azure: Azure Bastion needs to be able to connect to various public endpoints within Azure (for example, for storing diagnostics logs and metering logs). For this reason, Azure Bastion needs outbound to 443 to AzureCloud service tag.

Connectivity to Gateway Manager and Azure service tag is protected (locked down) by Azure certificates. External entities, including the consumers of those resources, can't communicate on these endpoints. You can learn more about Bastion NSG requirement here 

../../bastion/bastion-nsg.md

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### NS-2: Connect private networks together

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39745.).

**Guidance**: 

Azure Bastion does not expose any endpoints that can be accessed via a private network. This control is intended for describing how private networks can be connected together to provide access to the offering or its resources.

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### NS-3: Establish private network access to Azure services

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39746.).

**Guidance**: 

Azure Bastion is a fully managed PaaS service that provides secure and seamless RDP and SSH access to your virtual machines directly through the Azure Portal

Azure Bastion does not not allow for its management endpoints to be secure to a private network with the Private Link service.

Azure Bastion does not not provide the capability to configure Service Endpoints.

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### NS-4: Protect applications and services from external network attacks

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39747.).

**Guidance**: 

Protect your Azure Bastion resources against attacks from external networks, including distributed denial of service (DDoS) Attacks, application specific attacks, and unsolicited and potentially malicious internet traffic. Protect your assets against DDoS attacks by enabling DDoS standard protection on your Azure virtual networks. Use Azure Security Center to detect misconfiguration risks related to your network related resources.

Azure Bastion is a fully managed PaaS service that provides secure and seamless RDP and SSH access to your virtual machines and is not intended to run web applications, and does not require you to configure any additional settings or deploy any extra network services to protect it from external network attacks targeting web applications.

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### NS-5: Deploy intrusion detection/intrusion prevention systems (IDS/IPS)

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39748.).

**Guidance**: 

Not applicable; Azure Bastion is PaaS service and cannot be configured with an IDS or IPS solution for detecting or preventing threats on the network.

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### NS-6: Simplify network security rules

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39749.).

**Guidance**: 

Not applicable; this recommendation is intended for offerings that can be deployed into Azure Virtual Networks, or have the capability to define groupings of allowed IP ranges for efficient management. Azure Bastion does not currently support service tags.

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### NS-7: Secure Domain Name Service (DNS)

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39750.).

**Guidance**: 

Not applicable; Azure Bastion does not expose its underlying DNS configurations, these settings are maintained by Microsoft.

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

## Identity Management

*For more information, see the [Azure Security Benchmark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management).*

### IM-1: Standardize Azure Active Directory as the central identity and authentication system

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39730.).

**Guidance**: 

Not applicable; Azure Bastion doesn't allow access to virtual machine over RDP/SSH using AAD credentials. User can access Azure portal using AAD authentication to launch Azure Bastion service to connect to  virtual machine over RDP/SSH. 

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### IM-2: Manage application identities securely and automatically

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39731.).

**Guidance**: 

Not applicable; Azure Bastion doesn't use any identities or manage any secrets for identities.

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### IM-3: Use Azure AD single sign-on (SSO) for application access

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39732.).

**Guidance**: 

Not applicable; Azure Bastion doesn't support SSO for authentication to its resources.

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### IM-4: Use strong authentication controls for all Azure Active Directory based access

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39733.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### IM-5: Monitor and alert on account anomalies

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39734.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### IM-6: Restrict Azure resource access based on conditions

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39735.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### IM-7: Eliminate unintended credential exposure

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39736.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### IM-8: Secure user access to legacy applications

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39772.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

## Privileged Access

*For more information, see the [Azure Security Benchmark: Privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### PA-1: Protect and limit highly privileged users

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39737.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PA-2: Restrict administrative access to business-critical systems

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39738.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PA-3: Review and reconcile user access regularly

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39739.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PA-4: Set up emergency access in Azure AD

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39740.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PA-5: Automate entitlement management 

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39741.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PA-6: Use privileged access workstations

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39742.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PA-7: Follow just enough administration (least privilege principle) 

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39743.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

## Data Protection

*For more information, see the [Azure Security Benchmark: Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection).*

### DP-1: Discovery, classify and label sensitive data

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39751.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### DP-2: Protect sensitive data

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39752.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### DP-3: Monitor for unauthorized transfer of sensitive data

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39753.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### DP-4: Encrypt sensitive information in transit

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39754.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### DP-5: Encrypt sensitive data at rest

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39755.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

## Asset Management

*For more information, see the [Azure Security Benchmark: Asset Management](/azure/security/benchmarks/security-controls-v2-asset-management).*

### AM-1: Ensure security team has visibility into risks for assets

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39773.).

**Guidance**: &lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### AM-2: Ensure security team has access to asset inventory and metadata

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39774.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### AM-3: Use only approved Azure services

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39775.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### AM-4: Ensure security of asset lifecycle management

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39776.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### AM-5: Limit users' ability to interact with Azure Resource Manager

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39777.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### AM-6: Use only approved applications in compute resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39778.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

## Logging and Threat Detection

*For more information, see the [Azure Security Benchmark: Logging and Threat Detection](/azure/security/benchmarks/security-controls-v2-logging-threat-protection).*

### LT-1: Enable threat detection for Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39756.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### LT-2: Enable threat detection for Azure identity and access management

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39757.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### LT-3: Enable logging for Azure network activities

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39758.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### LT-4: Enable logging for Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39759.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### LT-5: Centralize security log management and analysis

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39760.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### LT-6: Configure log storage retention

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39761.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### LT-7: Use approved time synchronization sources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39762.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

## Incident Response

*For more information, see the [Azure Security Benchmark: Incident Response](/azure/security/benchmarks/security-controls-v2-incident-response).*

### IR-1: Preparation – update incident response process for Azure

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39763.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### IR-2: Preparation – setup incident notification

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39764.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### IR-3: Detection and analysis – create incidents based on high quality alerts

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39765.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### IR-4: Detection and analysis – investigate an incident

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39766.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### IR-5: Detection and analysis – prioritize incidents

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39767.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### IR-6: Containment, eradication and recovery – automate the incident handling

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39768.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

## Posture and Vulnerability Management

*For more information, see the [Azure Security Benchmark: Posture and Vulnerability Management](/azure/security/benchmarks/security-controls-v2-vulnerability-management).*

### PV-1: Establish secure configurations for Azure services 

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39783.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PV-2: Sustain secure configurations for Azure services

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39784.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PV-3: Establish secure configurations for compute resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39785.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PV-4: Sustain secure configurations for compute resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39786.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PV-5: Securely store custom operating system and container images

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39787.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PV-6: Perform software vulnerability assessments

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39788.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PV-7: Rapidly and automatically remediate software vulnerabilities

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39789.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PV-8: Conduct regular attack simulation

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39790.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

## Endpoint Security

*For more information, see the [Azure Security Benchmark: Endpoint Security](/azure/security/benchmarks/security-controls-v2-endpoint-security).*

### ES-1: Use Endpoint Detection and Response (EDR)

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39769.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### ES-2: Use centrally managed modern anti-malware software

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39770.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### ES-3: Ensure anti-malware software and signatures are updated

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39771.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

## Backup and Recovery

*For more information, see the [Azure Security Benchmark: Backup and Recovery](/azure/security/benchmarks/security-controls-v2-backup-recovery).*

### BR-1: Ensure regular automated backups

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39779.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### BR-2: Encrypt backup data

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39780.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### BR-3: Validate all backups including customer-managed keys

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39781.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.

 

Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### BR-4: Mitigate risk of lost keys

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39782.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

## Governance and Strategy

*For more information, see the [Azure Security Benchmark: Governance and Strategy](/azure/security/benchmarks/security-controls-v2-governance-strategy).*

### GS-1: Define asset management and data protection strategy 

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39791.).

**Guidance**: 

Ensure you document and communicate a clear strategy for continuous monitoring and protection of systems and data. Prioritize discovery, assessment, protection, and monitoring of business-critical data and systems. 

This strategy should include documented guidance, policy, and standards for the following elements: 

-	Data classification standard in accordance with the business risks

-	Security organization visibility into risks and asset inventory 

-	Security organization approval of Azure services for use 

-	Security of assets through their lifecycle

-	Required access control strategy in accordance with organizational data classification

-	Use of Azure native and third party data protection capabilities

-	Data encryption requirements for in-transit and at-rest use cases

-	Appropriate cryptographic standards

For more information, see the following references:

- [Azure Security Architecture Recommendation - Storage, data, and encryption](https://docs.microsoft.com/azure/architecture/framework/security/storage-data-encryption?toc=/security/compass/toc.json&amp;bc=/security/compass/breadcrumb/toc.json)

- [Azure Security Fundamentals - Azure Data security, encryption, and storage](../fundamentals/encryption-overview.md)

- [Cloud Adoption Framework - Azure data security and encryption best practices](https://docs.microsoft.com/azure/security/fundamentals/data-encryption-best-practices?toc=/azure/cloud-adoption-framework/toc.json&amp;bc=/azure/cloud-adoption-framework/_bread/toc.json)

- [Azure Security Benchmark - Asset management](/azure/security/benchmarks/security-benchmark-v2-asset-management)

- [Azure Security Benchmark - Data Protection](/azure/security/benchmarks/security-benchmark-v2-data-protection)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### GS-2: Define enterprise segmentation strategy 

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39798.).

**Guidance**: 

Establish an enterprise-wide strategy to segmenting access to assets using a combination of identity, network, application, subscription, management group, and other controls.

Carefully balance the need for security separation with the need to enable daily operation of the systems that need to communicate with each other and access data.

Ensure that the segmentation strategy is implemented consistently across control types including network security, identity and access models, and application permission/access models, and human process controls.

- [Guidance on segmentation strategy in Azure (video)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Guidance on segmentation strategy in Azure (document)](/security/compass/governance#enterprise-segmentation-strategy)

- [Align network segmentation with enterprise segmentation strategy](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### GS-3: Define security posture management strategy

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39792.).

**Guidance**: 

Continuously measure and mitigate risks to your individual assets and the environment they are hosted in. Prioritize high value assets and highly-exposed attack surfaces, such as published applications, network ingress and egress points, user and administrator endpoints, etc.

- [Azure Security Benchmark - Posture and vulnerability management](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### GS-4: Align organization roles, responsibilities, and accountabilities

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39793.).

**Guidance**: 

Ensure you document and communicate a clear strategy for roles and responsibilities in your security organization. Prioritize providing clear accountability for security decisions, educating everyone on the shared responsibility model, and educate technical teams on technology to secure the cloud.

- [Azure Security Best Practice 1 – People: Educate Teams on Cloud Security Journey](https://aka.ms/AzSec1)

- [Azure Security Best Practice 2 - People: Educate Teams on Cloud Security Technology](https://aka.ms/AzSec2)

- [Azure Security Best Practice 3 - Process: Assign Accountability for Cloud Security Decisions](https://aka.ms/AzSec3)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### GS-5: Define network security strategy

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39794.).

**Guidance**: 

Establish an Azure network security approach as part of your organization’s overall security access control strategy.  

This strategy should include documented guidance, policy, and standards for the following elements: 

-	Centralized network management and security responsibility

-	Virtual network segmentation model aligned with the enterprise segmentation strategy

-	Remediation strategy in different threat and attack scenarios

-	Internet edge and ingress and egress strategy

-	Hybrid cloud and on-premises interconnectivity strategy

-	Up-to-date network security artifacts (e.g. network diagrams, reference network architecture)

For more information, see the following references:

- [Azure Security Best Practice 11 - Architecture. Single unified security strategy](https://aka.ms/AzSec11)

- [Azure Security Benchmark - Network Security](/azure/security/benchmarks/security-benchmark-v2-network-security)

- [Azure network security overview](../fundamentals/network-overview.md)

- [Enterprise network architecture strategy](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### GS-6: Define identity and privileged access strategy

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39795.).

**Guidance**: 

Establish an Azure identity and privileged access approaches as part of your organization’s overall security access control strategy.  

This strategy should include documented guidance, policy, and standards for the following elements: 

-	A centralized identity and authentication system and its interconnectivity with other internal and external identity systems

-	Strong authentication methods in different use cases and conditions

-	Protection of highly privileged users

-	Anomaly user activities monitoring and handling  

-	User identity and access review and reconciliation process

For more information, see the following references:

- [Azure Security Benchmark - Identity management](/azure/security/benchmarks/security-benchmark-v2-identity-management)

- [Azure Security Benchmark - Privileged access](/azure/security/benchmarks/security-benchmark-v2-privileged-access)

- [Azure Security Best Practice 11 - Architecture. Single unified security strategy](https://aka.ms/AzSec11)

- [Azure identity management security overview](../fundamentals/identity-management-overview.md) 

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### GS-7: Define logging and threat response strategy

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/39796.).

**Guidance**: 

Establish a logging and threat response strategy to rapidly detect and remediate threats while meeting compliance requirements. Prioritize providing analysts with high quality alerts and seamless experiences so that they can focus on threats rather than integration and manual steps. 

This strategy should include documented guidance, policy, and standards for the following elements: 

-	The security operations (SecOps) organization’s role and responsibilities 

-	A well-defined incident response process aligning with NIST or another industry framework 

-	Log capture and retention to support threat detection, incident response, and compliance needs

-	Centralized visibility of and correlation information about threats, using SIEM, native Azure capabilities, and other sources 

-	Communication and notification plan with your customers, suppliers, and public parties of interest

-	Use of Azure native and third-party platforms for incident handling, such as logging and threat detection, forensics, and attack remediation and eradication

-	Processes for handling incidents and post-incident activities, such as lessons learned and evidence retention

For more information, see the following references:

- [Azure Security Benchmark - Logging and threat detection](/azure/security/benchmarks/security-benchmark-v2-logging-threat-detection)

- [Azure Security Benchmark - Incident response](/azure/security/benchmarks/security-benchmark-v2-incident-response)

- [Azure Security Best Practice 4 - Process. Update Incident Response Processes for Cloud](https://aka.ms/AzSec11)

- [Azure Adoption Framework, logging, and reporting decision guide](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Azure enterprise scale, management, and monitoring](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

## Next steps

- See the [Azure Security Benchmark V2 overview](/azure/security/benchmarks/overview)
- Learn more about [Azure security baselines](/azure/security/benchmarks/security-baselines-overview)
