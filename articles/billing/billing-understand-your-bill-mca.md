---
title: Understand the charges on your billing profile's invoice - Azure| Microsoft Docs
description: Learn how to read and understand the charges on your invoice
services: ''
documentationcenter: ''
author: jureid
manager: jureid
editor: ''
tags: billing

ms.assetid: 32eea268-161c-4b93-8774-bc435d78a8c9
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/19/2019
ms.author: banders

---
# Understand the charges on your billing profile's invoice

You can reconcile the charges on your invoice by analyzing the individual transactions that got billed on the invoice.

In the billing account for a Microsoft Customer Agreement, an invoice is generated each month for every billing profile. The invoice includes all charges from the previous month. You can view your invoices in the Azure portal. For more information, see [Understand your invoice](billing-understand-your-bill-mca.md).

This article applies to a billing account for a Microsoft Customer Agreement. Check if you have a [Microsoft Customer Agreement](billing-mca-overview.md#check-your-access-to-a-microsoft-customer-agreement).

## View transactions for an invoice in the Azure portal

1. Sign in to the [Azure portal](https://www.azure.com).

2. Search on **Cost Management +Billing**.

![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-costmanagement+billing.png)

3. Select **All transactions** from the left side of the screen. Depending on your access you may have to select a billing account or a billing profile, then select **All transactions**.

4. The All transactions page displays the following information:

![Screenshot that shows the billed transactions list](./media/billing-understand-your-bill-mca/mca-billed-transactions-list.png)

|Column  |Definition  |
|---------|---------|
|Date     | The date of transaction  |
|Invoice ID     | The identifier for the invoice on which the transaction got billed |
|Transaction type     |  The type of transaction like purchase, cancel, and usage charges  |
|Product family     | The category of product like compute for Virtual machines or database for Azure SQL database|
|Product sku     | A unique code identifying the instance of your product |
|Amount     |  The amount of transaction      |
|Invoice section     | The transaction shows up on this section of billing profile's invoice |
|Billing profile     | The transaction shows up on this billing profile's invoice |

5. Search on invoice ID to filter the transactions for the invoice.

## View charges by invoice sections

Invoice sections let you organize the charges on a billing profile's invoice. For more information, see [Understand invoice section](billing-mca-overview.md#understand-invoice-sections). When an invoice is generated, charges for all sections associated with the billing profile show up on the invoice.

The following image shows the invoice section charges on a sample invoice.

![Example image showing the details by invoice section information](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | Invoice PDF value | Usage CSV key|
 | --- | --- |
 |IT Department |invoiceSectionName |

Once you have identified the charges for an invoice section, you can view the transactions in the Azure portal to reconcile the charges.

1. Go to the All transactions page in the Azure portal to view transactions for an invoice. For more information, see [View transactions for an invoice in the Azure portal](#view-transactions-for-an-invoice-in-the-azure-portal).

1. Select an invoice section in the invoice section filter to view transactions for the invoice section.

## Analyze your Azure usage charges

Use the Azure usage and charges csv file to analyze the usage-based charges on your invoice. You can download the file for an invoice in the Azure portal. For more information, see [Get your Azure billing invoice and daily usage data](billing-download-azure-invoice-daily-usage-date.md).

You can filter the usage and charges file to reconcile the usage charges by invoice section.

![Example image showing the details by invoice section information](./media/billing-understand-your-bill-mca/invoicesection-details.png)

 | Invoice PDF value | Usage CSV key|
 | --- | --- |
 |IT Department |invoiceSectionName |
 |Usage Charges - Azure Standard |productOrderName |
 |Storage |serviceFamily |

The following steps walk you through finding daily usage for the example IT department in the detailed usage CSV file:

1. Filter the **invoiceSectionName** column in the CSV file to **IT Department**.
2. Filter the **productOrderName** column in the CSV file to **Usage Charges - Azure Standard**.
3. Filter the **serviceFamily** column in the CSV file to **Storage**.

## Understand pending charges to estimate your next invoice

In the billing account for a Microsoft Customer Agreement, until the charges are invoiced, they are estimate and considered pending. They are finalized and considered billed when they show on the invoice. You can view pending charges for a billing profile to estimate the charges on your next invoice.

### View charges summary

1. Sign in to the [Azure portal](https://www.azure.com).

2. Search on **Cost Management +Billing**.

![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-costmanagement+billing.png)

3. Select a billing profile. Depending on your access, you may have to select a billing account. From the billing account, select **Billing profiles** then select a billing profile.

4. Select **Summary** tab from the top of the screen.

5. The charges section display the month-to-date and last month's charges.

![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/mca-billing-profile-summary.png)

The month-to-date charges are the pending charges for the current month and will be billed on the invoice generated for the month. Your last month's charges are pending if the invoice is still not generated for last month.

### View pending transactions

Once you identify pending charges, you can view individual transactions that are contributing to the charges in the All transactions page. At this point, pending usage charges are not displayed on the All transaction page. You can view the pending usage charges on the Azure subscriptions page. For more information, see [View pending usage charges](#view-pending-usage-charges)

1. Sign in to the [Azure portal](https://www.azure.com).

2. Search on **Cost Management +Billing**.

![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-costmanagement+billing.png)

3. Select a billing profile. Depending on your access, you may have to select a billing account. From the billing account, select **Billing profiles** then select a billing profile.

4. Select **All transactions** from the left side of the screen.

5. Search for **pending** in the search box. Use the **Timespan** filter to view pending charges for current or last month.

![Screenshot that shows the pending transactions list](./media/billing-understand-your-bill-mca/mca-pending-transactions-list.png)

### View pending usage charges

1. Sign in to the [Azure portal](https://www.azure.com).

2. Search on **Cost Management +Billing**.

![Screenshot that shows Azure portal search for cost management + billing](./media/billing-understand-your-bill-mca/billing-search-costmanagement+billing.png)

3. Select a billing profile. Depending on your access, you may have to select a billing account. From the billing account, select **Billing profiles** then select a billing profile.

4. Select **All subscriptions** from the left side of the screen.

5. The Azure subscriptions page displays current and last month's charges for each subscription in the billing profile. If an invoice is not generated, last month's charges are still pending.

![Screenshot that shows the Azure subscriptions list for billing profile](./media/billing-understand-your-bill-mca/mca-billing-profile-subscriptions-list.png)

You can view the detailed usage-based charges using the Azure usage and charges file. To download the file, see [Get your Azure billing invoice and daily usage data](billing-download-azure-invoice-daily-usage-date.md).

## <a name="payment"></a>Pay your bill

Instructions for paying your bill are shown at the bottom of the invoice. [Learn how to pay](billing-understand-your-invoice-mca.md#how-to-pay).

If you've already paid your bill, you can check the status of the payment on the Invoices page in the Azure portal. 

## Check your access to a Microsoft Customer Agreement
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## Need help? Contact us

If you have questions or need help, [create a support request](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## Next steps

To learn more about your invoice and detailed usage, see:

- [How to get your Azure billing invoice and daily usage data](billing-download-azure-invoice-daily-usage-date.md)
- [Understand terms on your Microsoft Customer Agreement invoice](billing-understand-your-invoice-mca.md)
- [Understand terms on your Microsoft Azure detailed usage](billing-understand-your-usage.md)
