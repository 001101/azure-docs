---
title: Track Azure credit balance for your billing profile | Microsoft Docs
description: Learn to check Azure credit balance for your billing profile.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: banders
tags: billing

ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/17/2019
ms.author: amberb

---
# Track Azure credit balance for your billing profile

You can check the Azure credit balance for your billing profile in the Azure portal. You use credits to pay for products that are covered by the credits.

You are charged when you use products that aren't covered by the credits or your usage exceeds your credit balance. For more information, see [Products that aren't covered by Azure credits.](#products-that-arent-covered-by-azure-credits)

This article applies to a billing account for a Microsoft Customer Agreement. [Check if you have access to a Microsoft Customer Agreement](#check-your-access-to-a-billing-account-for-microsoft-customer-agreement).

## Check credit balance in the Azure portal

1. Sign in to the [Azure portal]( http://portal.azure.com).

2. Search on **Cost Management + Billing**.

   ![Screenshot that shows search in portal for cost management + billing](./media/billing-mca-check-azure-credits-balance/billing-search-costmanagement+billing.png)

3. Go to the billing profile. Depending on your access, you may need to select a billing account. From the billing account, select **Billing profiles** and then a billing profile.
   <!--- TODO - Add screenshots -->

4. Select **Azure credits**.

5. The Azure credits page shows the following information:

   ![Screenshot of credit balance and transactions for a Billing profile](./media/billing-mca-check-azure-credits-balance/credits-overview.PNG)
   <!--- TODO - Update the screenshot -->
   | Term               | Definition                           |
   |--------------------|--------------------------------------------------------|
   | Estimated balance  | Estimated amount of credits you have after considering all billed and pending transactions |
   | Current balance    | Amount of credits as of your last invoice. It doesn't include any pending transactions |
   | Transactions       | All billing transactions that are applied to your Azure credits |

   When your estimated balance drops to 0, you are charged for all your usage, including for products that are covered by credits.

6. Select **Credits list** to view list of credits for the billing profile. The credits list provides the following information:

   ![Screenshot of credits lists for a Billing profile](./media/billing-mca-check-azure-credits-balance/credits-list.PNG)
   <!--- TODO - Update the screenshot -->

   | Term                 | Definition                           |
   |----------------------|--------------------------------------------------------|
   | Source               | The acquisition source of the credit |
   | Start date           | The date when you acquired the credit |
   | Expiration date      | The date when the credit expires |
   | Remaining amount     | The balance as of your last invoice |
   | Original amount      | The original amount of credit |
   | Status               | The current status of credit. Status can be active, used, expired, or expiring |

## How credits are used in Microsoft Customer Agreement

In a billing account for a Microsoft customer agreement, you use billing profiles to customize how you pay your invoices. A billing profile contains information like billing address and payment methods. A monthly invoice is generated for each billing profile and you use the payment methods to pay the invoice.

Azure credits are one of the payment methods. You add credit or Microsoft provides you credit. You assign the credit to a billing profile. When an invoice is generated for the billing profile, credits are automatically applied to the total billed amount to calculate the amount that you need to pay. You pay the remaining amount with another payment method like check or wire transfer.

## Products that aren't covered by Azure credits

 The following products aren't covered by your Azure credits. You're charged for using these products regardless of your credit balance:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop 
- Registered User
- Openlogic
- Remote Access Rights XenApp Essentials Registered User
- Ubuntu Advantage
- Visual Studio Enterprise (Monthly)
- Visual Studio Enterprise (Annual)
- Visual Studio Professional (Monthly)
- Visual Studio Professional (Annual)
- Azure Marketplace charges

<!--- TODO - Update the list -->

## Check your access to a Microsoft Customer Agreement
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## Next steps

- [Understand your invoice for a Microsoft Customer Agreement](billing-understand-your-bill.md)<!--Todo update the link -->
- [Understand billing profiles](billing-mca-overview.md#understand-billing-profiles)

## Need help? Contact support

If you need help, [contact support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) to get your issue resolved quickly.
