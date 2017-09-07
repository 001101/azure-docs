---
title: Azure Government Identity | Microsoft Docs
description: Provides Planning Guidance for Identity in Azure Government
services: azure-government
cloud: gov
documentationcenter: ''
author: beellis
manager: zakramer

ms.assetid: 1f222624-872b-4fe7-9c65-796deae03306
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 09/06/2017
ms.author: beellis

---
# Planning Identity for Azure Government Applications
##Overview
Microsoft Azure Government provides the same ways to build applications and manage identities as Azure Commercial. Azure Government customers may already have an Azure Active Directory (AAD) Commercial tenant or may create a tenant in AAD Government. This article provides guidance on identity decisions based on the application and location of your identity.

##Identity Models
Before determining the identity approach for your application, you need to know what identity types are available to you. There are three types: On-Premises Identity, Cloud Identity, and Hybrid Identity.


|On-Premises Identity|Cloud Identity|Hybrid Identity
|---|---|---|
|On-Premises Identities belong to on-premises Active Directory environments that most customers use today.|Cloud identities are born, only exist in, and are managed in Azure AD.|Hybrid identities originate as on-premises identities but become hybrid through directory synchronization to Azure AD; after which they then exist both on-premises and in the cloud|

[!NOTE]
Hybrid comes with deployment options (Synchronized Identity, Federated Identity, etc.) that all rely on directory synchronization and mostly define how identities are authenticated as discussed in <a href="https://docs.microsoft.com/en-us/azure/active-directory/choose-hybrid-identity-solution"> Choose a Hybrid Identity Solution. </a>.

##Selecting Identity for an Azure Government Application
When building any Azure application, a developer must first decide on the authentication technology:

-Applications Using Modern Authentication – These are applications using OAuth, OpenID Connect, and/or other modern authentication protocols supported by Azure Active Directory.  An example is a newly developed application built using PaaS technologies (e.g. Web Sites, Cloud Database as a Service, etc.)
-Apps Using Legacy (Kerberos/NTLM) Authentication Protocols – These are typically applications migrated from on-premises (e.g. Lift-n-Shift).

Based on this decision there are different considerations when building in Azure Government.

###Applications using Modern Authentication in Azure Government
<a href="https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-integrating-applications"> Integrating Applications with Azure Active Directory</a> shows how you can use Azure AD to provide secure sign in and authorization to your applications.  This process is the same for Azure Commercial and Azure Government once you identify your identity authority.

####Choosing your Identity Authority
Azure Government applications can use AAD Government identities, but can you use AAD Commercial identities to authenticate to an application hosted in Azure Government?  Yes!  Since you can use either identity authority, you will need to choose which to use:

-	**AAD Commercial** – Commonly used if your organization already has an AAD Commercial tenant to support Office 365 (Commercial or GCC Moderate) or another application.
-	**AAD Government** - Commonly used if your organization already has an AAD Government tenant to support Office 365 (GCC High or DoD) or are creating a new tenant in AAD Government.

Once decided, the special consideration is where you perform your app registration. Leveraging AAD Commercial identities for your Azure Government application requires the application to be registered in your AAD Commercial tenant. Otherwise, if you perform the app registration in the directory the subscription trusts (Azure Government) the intended set of users cannot authenticate.

[!NOTE]
 Applications registered with Azure AD only allow sign-in from users in the Azure AD tenant the application was registered in. If you have multiple Azure AD Commercial tenants, it’s important to know which is intended to allow sign-ins from. If you intend to allow users to authenticate to the application from multiple Azure AD tenants the application must be registered in each tenant.

The other consideration will be the identity authority URL.  You will need to use the correct URL based on your chosen authority:

-	**AAD Commercial** = login.microsoftonline.com
-	**AAD Government** = login.microsoftonline.us

###Applications Using Legacy (Kerberos/NTLM) Authentication Protocols
Supporting IaaS cloud-based applications dependent on NTLM/Kerberos authentication requires On-Premises Identity. This is enabled by extending the on-premises Active Directory footprint to Azure by adding domain controllers as virtual machines (As shown below) to support logins for line-of-business applications/other apps that require Windows Integrated authentication.

![alt text](./media/documentation-government-plan-identity-extending-ad-to-azure-iaas.png)

The figure above is a very simple connectivity example, using site-to-site VPN. This could easily, and more preferably, be an Azure ExpressRoute connection. The type of domain controller to place in Azure is also a consideration based on application requirements for directory access. If applications require directory write access, deploy a standard domain controller with a writable copy of the Active Directory database. If applications only require directory read access, we recommend deploying a RODC (Read Only Domain Controller) to Azure instead. Specifically, for RODCs we recommend following the guidance available at <a href="https://msdn.microsoft.com/en-us/library/azure/jj156090.aspx#BKMK_RODC">Deployment Decisions and Factors for Read-Only DCs</a>.

We have documentation covering the guidelines for deploying AD Domain Controllers and ADFS (AD Federation Services) at these links:

 -<a href="https://msdn.microsoft.com/en-us/library/azure/jj156090.aspx"> Guidelines for Deploying Windows Server Active Directory on Azure Virtual Machines</a>
  -Answers questions such as:
   -Is it safe to virtualize Windows Server Active Directory Domain Controllers?
   -Why deploy AD to Azure Virtual Machines?
   -Can you deploy ADFS to Azure Virtual Machines?
 -<a href="https://docs.microsoft.com/en-us/azure/active-directory/connect/active-directory-aadconnect-azure-adfs">Deploying Active Directory Federation Services in Azure</a>
  -Provides guidance on how to deploy ADFS in Azure.

##Subscription Administration & Identities in Azure Government
Subscription administration must be performed by an identity from the directory that the subscription depends on. Therefore, a separate identity is required to manage an Azure Commercial subscription than an Azure Government subscription.

##Frequently Asked Questions
1)	How to Identify an Azure Government Tenant?

    **Answer**: Here’s a way to find out using your browser of choice:

       a.	Obtain your tenant name (e.g. contoso.onmicrosoft.com) or a domain name registered to your Azure AD tenant (e.g. contoso.gov).

       b.	Navigate to https://login.microsoftonline.com/<domainname>/.well-known/openid-configuration
	     - <domainname> can either be the tenant name or domain name you gathered in step 1.
	     - e.g. https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

       c.	The result should be posted back to the page in attribute/value pairs using JSON (Java Script Object Notation) format that resembles the following:

![alt text](./media/documentation-government-plan-identity-openid-config-json.png)

       d. If the **tenant_region_scope** attribute’s value is **USG**, you have yourself an Azure Government tenant.

   -	**Note**: The result is a JSON file that’s automatically recognized and displayed by more modern browsers such as Microsoft Edge, Mozilla Firefox, and Google Chrome. Internet Explorer doesn’t understand the JSON format so instead prompts you to open or save the file. If you must use Internet Explorer choose the save option and open it with another browser or plain text reader.
   -	**Note**: The tenant_region_scope property is exactly how it sounds, regional. If you have a tenant in Azure Commercial in North America, the value would be **NA**.

2)	If I’m an Office 365 GCC Moderate customer and want to build solutions in Azure Government do I need to have two tenants?

    **Answer**: Yes, the AAD Government tenant is only required for your Azure Government Subscription administration.

3)	If I’m an Office 365 GCC Moderate customer that has built workloads in Azure Government, where should I authenticate from, Commercial or Government?

    **Answer**:  Please see “Choosing your Identity Authority” earlier in this article.
