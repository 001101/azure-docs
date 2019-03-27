---
title: Azure Delegated Resource Management
description: Managed services offers allow service providers to sell resource management offers to customers in Azure Marketplace.
author: JnHs
ms.service: service-provider-toolkit
ms.author: jenhayes
ms.date: 04/03/2019
ms.topic: overview
manager: carmonm
---
# Azure Delegated Resource Management

Azure Delegated Resource Management is one of the key components of the Microsoft Azure Service Provider Management Toolkit. With Azure Delegated Resource Management, service providers can  simplify customer engagement and onboarding experiences, while managing delegated resources at scale with agility and precision.

> [!IMPORTANT]
> Azure Delegated Resource Management is currently in limited public preview. The info in this topic may change before general availability.

Azure Delegated Resource Management lets service providers manage their customers’ Azure resources from the service provider’s own Azure Active Directory (Azure AD) tenant. This means that customers don’t need to enable global admin to their tenant in order to have a service provider handle administration tasks.

> [!NOTE]
> Azure Delegated Resource Management can also be used within an enterprise which has multiple Azure AD tenants of its own to simplify cross-tenant administration.

As a service provider, you can define user groups in your own tenant for the roles that are required to perform tasks for your customers. Your users can then manage the customers’ resources using their own account credentials. They can also [view and manage all delegated customer subscriptions in the new **My customers** page](../how-to/view-manage-customers.md) in the Azure portal. The [cross-tenant management experience](cross-tenant-management-experience.md) helps you work more efficiently with Azure management services like Azure Policy, Azure Security Center, and more. All service provider activity is tracked in Azure Monitor logs in the customer's tenant, so both the customer and service provider can easily identify the user associated with any changes.

When you onboard a customer to Azure Delegated Resource Management, they’ll have access to the new **Service providers** page in the Azure portal, where they can [confirm and manage their offers, service providers, and delegated resources](../how-to/view-manage-service-providers.md). If the customer ever wants to revoke access for a service provider, they can do so here at any time.

You can [publish the new Managed Services offer type to Azure Marketplace](../how-to/publish-managed-services-offers.md) to easily onboard customers to Azure Delegated Resource Management. Alternatively, you can [complete the onboarding process by deploying Azure Resource Manager templates](../how-to/onboard-customer.md).
