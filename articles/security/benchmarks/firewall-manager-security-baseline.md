---
title: Azure security baseline for Azure Firewall Manager
description: The Azure Firewall Manager security baseline provides procedural guidance and resources for implementing the security recommendations specified in the Azure Security Benchmark.
author: msmbaldwin
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 01/01/2000
ms.author: mbaldwin
ms.custom: subject-security-benchmark

# Important: This content is machine generated; do not modify this topic directly. Contact mbaldwin for more information.

---

# Azure security baseline for Azure Firewall Manager

This security baseline applies guidance from the [Azure Security Benchmark version 2.0](overview.md) to Azure Firewall Manager. The Azure Security Benchmark provides recommendations on how you can secure your cloud solutions on Azure. The content is grouped by the **security controls** defined by the Azure Security Benchmark and the related guidance applicable to Azure Firewall Manager. **Controls** not applicable to Azure Firewall Manager have been excluded.

To see how Azure Firewall Manager completely maps to the Azure Security Benchmark, see the [full Azure Firewall Manager security baseline mapping file](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

>[!WARNING]
>This preview version of the article is for review only. **DO NOT MERGE INTO MASTER!**

## Network Security

*For more information, see the [Azure Security Benchmark: Network Security](/azure/security/benchmarks/security-controls-v2-network-security).*

### NS-1:   

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40024.).

**Guidance**: 

Not applicable; Azure firewall is a portal extension and is not a service. 

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### NS-2: Connect private networks together

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40025.).

**Guidance**: 

Not applicable; Firewall manager is a portal extension. Cannot be deployed to or accessed from a private network

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### NS-3: Establish private network access to Azure services

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40026.).

**Guidance**: 

Not applicable;  Azure Firewall Manager does not allow for its resources to be secured to a private network via the Private Link service or Service Endpoints.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### NS-4: 

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40027.).

**Guidance**: 

Not applicable: Azure Firewall manager is a portal extension and leverages underlying RP's like NRP/NFVRP.  It doesnt get deployed in VNET on it;s own.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### NS-5: Deploy intrusion detection/intrusion prevention systems (IDS/IPS)

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40028.).

**Guidance**: 

Not applicable: Azure Firewall manager is a portal extension and leverages underlying RP's like NRP/NFVRP.  It doesn't get deployed in VNET on it;s own.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### NS-6: Simplify network security rules

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40029.).

**Guidance**: 

Not applicable: Azure Firewall manager is a portal extension and leverages underlying RP's like NRP/NFVRP.  It doesnt get deployed in VNET on it;s own.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### NS-7: Secure Domain Name Service (DNS)

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40030.).

**Guidance**: 

Not applicable: Azure Firewall manager is a portal extension and leverages underlying RP's like NRP/NFVRP.  It doesnt get deployed in VNET on it;s own.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

## Identity Management

*For more information, see the [Azure Security Benchmark: Identity Management](/azure/security/benchmarks/security-controls-v2-identity-management).*

### IM-1: Standardize Azure Active Directory as the central identity and authentication system

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40010.).

**Guidance**: 

Use Azure Active Directory (Azure AD) as the central authentication and authorization system. Azure AD protects data by using strong encryption for data at rest and in transit. Azure AD also salts, hashes, and securely stores user credentials.

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### IM-2: Manage application identities securely and automatically

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40011.).

**Guidance**: 

Use Managed Identities to provide Azure services with an automatically managed identity in Azure AD. Managed Identities allows you to authenticate to any service that supports Azure AD authentication to Azure Resource Manager and can be used with API/Azure Portal/CLI/PowerShell.

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### IM-3: Use Azure AD single sign-on (SSO) for application access

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40012.).

**Guidance**: 

Wherever possible, use Azure Active Directory SSO instead of configuring individual stand-alone credentials per-service. Use Azure Security Center Identity and Access Management recommendations.

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### IM-4: Use strong authentication controls for all Azure Active Directory based access

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40013.).

**Guidance**: 

Enable Azure Active Directory multi-factor authentication(MFA) and follow Azure Security Center Identity and Access Management recommendations.

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### IM-5: Monitor and alert on account anomalies

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40014.).

**Guidance**: 

Azure Security Center can  alert on certain suspicious activities such as an excessive number of failed authentication attempts, and deprecated accounts in the subscription. 

Use custom RBAC policy to control access and view changes in activity logs. 

**Azure Security Center monitoring**: Yes

**Responsibility**: Customer

### IM-6: Restrict Azure resource access based on conditions

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40015.).

**Guidance**: 

 Use Conditional Access Named Locations to allow access from only specific logical groupings of IP address ranges or countries/regions.

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### IM-7: Eliminate unintended credential exposure

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40016.).

**Guidance**: 

Not applicable; Azure Firewall Manager doesn't allow customer to deploy any persisted data into the running environment.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

### IM-8: Secure user access to legacy applications

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40052.).

**Guidance**: 

Not applicable; Azure Firewall Manager doesn't access any legacy applications.

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Not applicable

## Privileged Access

*For more information, see the [Azure Security Benchmark: Privileged Access](/azure/security/benchmarks/security-controls-v2-privileged-access).*

### PA-1: Protect and limit highly privileged users

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40017.).

**Guidance**: 

Azure firewall manager can be accessed by AD built-in accounts and custom RBAC accounts. 

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### PA-2: Restrict administrative access to business-critical systems

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40018.).

**Guidance**: 

Azure Firewall Manager uses Azure RBAC to isolate access to business-critical systems by restricting which accounts are granted privileged access to the subscriptions and management groups they are in.

Ensure that you also restrict access to the management, identity, and security systems that have administrative access to your business critical access such as Active Directory Domain Controllers (DCs), security tools, and system management tools with agents installed on business critical systems. Attackers who compromise these management and security systems can immediately weaponize them to compromise business critical assets.

All types of access controls should be aligned to your enterprise segmentation strategy to ensure consistent access control.

Azure Components and Reference model /security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151 

Management Group Access ../../governance/management-groups/overview.md#management-group-access 

Azure subscription administrators ../../cost-management-billing/manage/add-change-subscription-administrator.md 

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### PA-3: Review and reconcile user access regularly

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40019.).

**Guidance**: 

Azure Firewall Manager uses Azure Active Directory (AAD) accounts to manage its resources, review user accounts and access assignment regularly to ensure the accounts and their access are valid. You can use Azure AD access reviews to review group memberships, access to enterprise applications, and role assignments. Azure AD reporting can provide logs to help discover stale accounts. You can also use Azure AD Privileged Identity Management to create access review report workflow to facilitate the review process.

In addition, Azure Privileged Identity Management can also be configured to alert when an excessive number of administrator accounts are created, and to identify administrator accounts that are stale or improperly configured.

Note: Some Azure services support local users and roles which not managed through Azure AD. You will need to manage these users separately.

- [Create an access review of Azure resource roles in Privileged Identity Management(PIM)](../../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md) 

- [How to use Azure AD identity and access reviews](../../active-directory/governance/access-reviews-overview.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### PA-4: Set up emergency access in Azure AD

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40020.).

**Guidance**: 

Azure Firewall Manager uses Azure Active Directory to manage its resources. To prevent being accidentally locked out of your Azure AD organization, set up an emergency access account for access when normal administrative accounts cannot be used. Emergency access accounts are usually highly privileged, and they should not be assigned to specific individuals. Emergency access accounts are limited to emergency or "break glass"' scenarios where normal administrative accounts can't be used.
You should ensure that the credentials (such as password, certificate, or smart card) for emergency access accounts are kept secure and known only to individuals who are authorized to use them only in an emergency.
- [Manage emergency access accounts in Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### PA-5: Automate entitlement management 

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40021.).

**Guidance**: 

Azure Firewall Manager is integrated with Azure Active Directory to manage its resources. Use Azure AD entitlement management features to automate access request workflows, including access assignments, reviews, and expiration. Dual or multi-stage approval is also supported.
- [What are Azure AD access reviews](../../active-directory/governance/access-reviews-overview.md) 
- [What is Azure AD entitlement management](../../active-directory/governance/entitlement-management-overview.md)

**Azure Security Center monitoring**: Currently not available

**Responsibility**: Customer

### PA-6: Use privileged access workstations

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40022.).

**Guidance**: 

Secured, isolated workstations are critically important for the security of sensitive roles like administrators, developers, and critical service operators. Use highly secured user workstations and/or Azure Bastion for administrative tasks. Use Azure Active Directory, Microsoft Defender Advanced Threat Protection (ATP), and/or Microsoft Intune to deploy a secure and managed user workstation for administrative tasks. The secured workstations can be centrally managed to enforce secured configuration, including strong authentication, software and hardware baselines, and restricted logical and network access. 

- [Understand privileged access workstations](../../active-directory/devices/concept-azure-managed-workstation.md)

- [Deploy a privileged access workstation](../../active-directory/devices/howto-azure-managed-workstation.md)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### PA-7: Follow just enough administration (least privilege principle) 

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40023.).

**Guidance**: 

Azure Firewall Manager is integrated with Azure role-based access control (RBAC) to manage its resources. Azure RBAC allows you to manage Azure resource access through role assignments. You can assign these roles to users, groups service principals and managed identities. There are pre-defined built-in roles for certain resources, and these roles can be inventoried or queried through tools such as Azure CLI, Azure PowerShell or the Azure portal. The privileges you assign to resources through the Azure RBAC should be always limited to what is required by the roles. This complements the just in time (JIT) approach of Azure AD Privileged Identity Management (PIM) and should be reviewed periodically.

Use built-in roles to allocate permission and only create custom role when required.

What is Azure role-based access control (Azure RBAC) ../../role-based-access-control/overview.md 

- [How to configure RBAC in Azure](../../role-based-access-control/role-assignments-portal.md) 

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

## Data Protection

*For more information, see the [Azure Security Benchmark: Data Protection](/azure/security/benchmarks/security-controls-v2-data-protection).*

### DP-1: Discovery, classify and label sensitive data

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40031.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### DP-2: Protect sensitive data

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40032.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### DP-3: Monitor for unauthorized transfer of sensitive data

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40033.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### DP-4: Encrypt sensitive information in transit

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40034.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### DP-5: Encrypt sensitive data at rest

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40035.).

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
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40053.).

**Guidance**: &lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Not applicable

### AM-2: Ensure security team has access to asset inventory and metadata

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40054.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### AM-3: Use only approved Azure services

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40055.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### AM-4: Ensure security of asset lifecycle management

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40056.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### AM-5: Limit users' ability to interact with Azure Resource Manager

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40057.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### AM-6: Use only approved applications in compute resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40058.).

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
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40036.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### LT-2: Enable threat detection for Azure identity and access management

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40037.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### LT-3: Enable logging for Azure network activities

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40038.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### LT-4: Enable logging for Azure resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40039.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### LT-5: Centralize security log management and analysis

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40040.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### LT-6: Configure log storage retention

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40041.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### LT-7: Use approved time synchronization sources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40042.).

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
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40043.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### IR-2: Preparation – setup incident notification

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40044.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### IR-3: Detection and analysis – create incidents based on high quality alerts

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40045.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### IR-4: Detection and analysis – investigate an incident

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40046.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### IR-5: Detection and analysis – prioritize incidents

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40047.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### IR-6: Containment, eradication and recovery – automate the incident handling

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40048.).

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
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40063.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PV-2: Sustain secure configurations for Azure services

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40064.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PV-3: Establish secure configurations for compute resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40065.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PV-4: Sustain secure configurations for compute resources

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40066.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PV-5: Securely store custom operating system and container images

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40067.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PV-6: Perform software vulnerability assessments

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40068.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PV-7: Rapidly and automatically remediate software vulnerabilities

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40069.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### PV-8: Conduct regular attack simulation

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40070.).

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
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40049.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### ES-2: Use centrally managed modern anti-malware software

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40050.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### ES-3: Ensure anti-malware software and signatures are updated

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40051.).

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
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40059.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### BR-2: Encrypt backup data

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40060.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.
 
Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### BR-3: Validate all backups including customer-managed keys

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40061.).

**Guidance**: 

&lt;This placeholder text gives initial instructions, please remove all of the text in this 'ASCB Customer Guidelines' text box and replace it with your customer guidance&gt;

Please provide customer guidance for this control specific to your offering. More detail can be found on what guidance to include for this control in the self-service wiki guide.

 

Mark this control work item as 'Submitted for Review' when ready for the benchmark team to review.

**Azure Security Center monitoring**: Unset. Please provide a value in the work item.

**Responsibility**: Unset. Please provide a value in the work item.

### BR-4: Mitigate risk of lost keys

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40062.).

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
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40071.).

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
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40078.).

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
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40072.).

**Guidance**: 

Continuously measure and mitigate risks to your individual assets and the environment they are hosted in. Prioritize high value assets and highly-exposed attack surfaces, such as published applications, network ingress and egress points, user and administrator endpoints, etc.

- [Azure Security Benchmark - Posture and vulnerability management](/azure/security/benchmarks/security-benchmark-v2-posture-vulnerability-management)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### GS-4: Align organization roles, responsibilities, and accountabilities

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40073.).

**Guidance**: 

Ensure you document and communicate a clear strategy for roles and responsibilities in your security organization. Prioritize providing clear accountability for security decisions, educating everyone on the shared responsibility model, and educate technical teams on technology to secure the cloud.

- [Azure Security Best Practice 1 – People: Educate Teams on Cloud Security Journey](https://aka.ms/AzSec1)

- [Azure Security Best Practice 2 - People: Educate Teams on Cloud Security Technology](https://aka.ms/AzSec2)

- [Azure Security Best Practice 3 - Process: Assign Accountability for Cloud Security Decisions](https://aka.ms/AzSec3)

**Azure Security Center monitoring**: Not applicable

**Responsibility**: Customer

### GS-5: Define network security strategy

>[!NOTE]
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40074.).

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
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40075.).

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
> To revise the text in this section, update the [underlying Work Item](https://dev.azure.com/AzureSecurityControlsBenchmark/AzureSecurityControlsBenchmarkContent/_workitems/edit/40076.).

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
