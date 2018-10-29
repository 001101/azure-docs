---
title: Manage access to Azure billing | Microsoft Docs
description: 
services: ''
documentationcenter: ''
author: vikramdesai01
manager: vikdesai
editor: ''
tags: billing

ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: cwatson

---
# Manage access to billing information for Azure

For most subscriptions, you can give billing information access to members of your team from **Subscriptions** in the Azure portal. If you're an Azure customer with an Enterprise Agreement (EA customer) and are the Enterprise Administrator, you can give permissions to the Department Admins and Account Owners from **Cost Management + Billing** in the Azure portal.

## Give access to billing for most subscriptions

All except EA customers can grant access to Azure billing information by assigning one of the following user roles to members of your team:

- Account Administrator
- Service Administrator
- Co-administrator
- Owner
- Contributor
- Reader
- Billing Reader

To assign roles, see [Manage access using RBAC and the Azure portal](../role-based-access-control/role-assignments-portal.md).

Those roles have access to billing information in the [Azure portal](https://portal.azure.com/). People that are assigned those roles can also use the [Billing APIs](billing-usage-rate-card-overview.md) to programmatically get invoices and usage details. For more information, see [Roles in Azure RBAC](../role-based-access-control/built-in-roles.md).

### <a name="opt-in"></a> Allow users to download invoices

After you assign the appropriate roles to members of your team, the Account Administrator must turn on  assess to download invoices in the Azure portal. Invoices older than December 2016 are available only to the Account Administrator.

1. Sign in to the [Azure portal](https://portal.azure.com/).
1. As the Account Administrator, select your subscription from the [Subscriptions blade](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in Azure portal.

1. Select **Invoices** and then **Access to invoices**.

    ![Screenshot shows how to delegate access to invoices](./media/billing-manage-access/AA-optin.png)

1. Select **On** and save.

    ![Screenshot shows on-off to delegate access to invoice](./media/billing-manage-access/AA-optinAllow.png)

The Account Administrator can also configure to have invoices sent via email. To learn more, see [Get your invoice in email](billing-download-azure-invoice-daily-usage-date.md).

## Give read-only access to billing information

Assign the Billing Reader role to someone that needs read-only access to the subscription billing information but not the ability to manage or create Azure services. This role is appropriate for users in an organization who only perform financial and cost management for Azure subscriptions.

If you're an EA customer, an Account Owner or Department Admin can assign the Billing Reader role to team members. But for that Billing Reader to be able to view billing information for the department or account, the Enterprise Administrator must enable  **Account Owner can view charges** or **Department Admin can view charges** policies.

The Billing Reader feature is in preview, and does not yet support non-global clouds.

1. Sign in to the [Azure portal](https://portal.azure.com/).
1. Select your subscription from the [Subscriptions blade](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) in Azure portal.

1. Select **Access control (IAM)** and then click **Add**.

    ![Screenshot shows IAM in the subscription blade](./media/billing-manage-access/select-iam.PNG)

1. Choose **Billing Reader** as the **Role**.
1. In the **Select** textbox, type the name or email for the user you add.
1. Select **Save**.
1. The Billing Reader receives an email with a link to sign in.

    ![Screenshot that shows what the Billing Reader can see in Azure portal](./media/billing-manage-access/billing-reader-view.png)

## Allow Department Admin or Account Owner billing access for EA customers

The Enterprise Admin can allow the Department Admins and Account Owners to view usage details and costs associated to the Departments and Accounts that they manage.

1. As the Enterprise Admin, sign in to the [Azure portal](https://portal.azure.com/).
1. Search on **Cost Management + Billing**.

    ![Screenshot that shows Azure portal search](./media/billing-manage-access/portal-cm-billing-search.png)
   
1. On **Overview**, select the organization.
1. Select the account that you want to manage.
1. Select **Policies**.
1. Select **Account owners can view charges** or **Department admins can view charges** or both.

    ![Screenshot that shows view charges policies](./media/billing-manage-access/billing-ea-view-charges-policy.png)

1. After you select these policies, the Department Admin and/or Account Owners can:

   - View the Usage Summary dashboard in the EA portal for their specific department or account.
   - Download reports from **Download Usage** in the EA portal. The reports include the Usage Detail CSV and Marketplace Charges CSV for their specific department or account.
   - Access the enterprise’s custom pricing in Azure Calculator.
   - See forecasts at EA price rates in the Azure portal.

For more information, see Understand Azure Enterprise Agreement administrative roles.

## Only Account Administrators can access the Account Center

The Account Administrator is the legal owner of the subscription. By default, the person who signed up for or bought the Azure subscription is the Account Administrator, unless the [subscription ownership was transferred](billing-subscription-transfer.md) to somebody else. The Account Administrator can create subscriptions, cancel subscriptions, change the billing address for a subscription, and manage access policies for the subscription from the [Account Center](https://account.azure.com/Subscriptions).

## Next steps

- Users in other roles, such as Owner or Contributor, can access not just billing information, but Azure services as well. To manage these roles, see [Manage access using RBAC and the Azure portal](../role-based-access-control/role-assignments-portal.md).
- For more information about roles, see [Built-in roles for Azure resources](../role-based-access-control/built-in-roles.md).

## Need help? Contact support.

If you still have further questions, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to get your issue resolved quickly.
