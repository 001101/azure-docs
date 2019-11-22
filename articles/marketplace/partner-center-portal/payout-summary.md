---
title: Commercial marketplace payout summary
description: The Payout summary shows you details about the money you’ve earned with your offer. It also lets you know when you’ll receive payments and how much you'll be paid.
author: qianw211
manager: evansma
ms.author: v-qiwe
ms.service: marketplace
ms.topic: guide
ms.date: 11/21/2019
---

# Payout reporting

The **Payout summary** shows you details about the money you’ve earned with Microsoft. It also lets you know when you’ll receive payments and how much you'll be paid.

If you sell products in the Azure Marketplace, you’ll also see info on successful payouts in the **Payout summary**. For more details regarding Azure Marketplace payment, see the [Microsoft Azure Marketplace Participation Policies](https://go.microsoft.com/fwlink/p/?LinkId=722436) and the [Microsoft Azure Marketplace Publisher Agreement](https://go.microsoft.com/fwlink/p/?LinkID=699560).

## FAQs

* Complete the Partner Center enrollment and set the permission as MSA admin or finance administrator to access the payout report (add the enrollment link)
* Set up your Payout account and tax forms (add the link)
* See [Billing basics in Partner Center](https://docs.microsoft.com/partner-center/billing-basics) for more information.

> [!NOTE]
> To be eligible for payout, your proceeds must reach the [payment threshold](payment-thresholds-methods-and-timeframes.md) of $50. For details about the payment threshold see this page and review the [Microsoft Azure Marketplace Publisher Agreement](https://go.microsoft.com/fwlink/p/?LinkID=699560).

## Billing

Depending on the transaction option used, the publisher’s software license fees can be presented as follows:

* Free: No charge for software licenses.
* **Bring the own license (BYOL)**: Any applicable charges for software licenses are managed directly between the publisher and customer. Microsoft only passes through Azure infrastructure usage fees. (Virtual Machines and Azure Applications only).
* **Pay-as-you-go**: Software license fees are presented as a per-hour, per-core (VCPU) pricing rate based on the Azure infrastructure used. This only applies to Virtual Machines and Azure Applications. 
* **Subscription pricing**: Software license fees are presented as a monthly or annual recurring fee billed as a flat rate or per-seat. This only applies to SaaS Apps and Azure Applications - Managed Apps.

### Pay as you go

If you enable the Pay-As-You-Go option, then you have the following cost structure. 

* If you enable the Pay-As-You-Go option, then you have the following cost structure.

|Your license cost  | $1.00 per hour  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$1.14 per hour*       |

* In this scenario, Microsoft bills $1.14 per hour for use of your published VM image.

|Microsoft bills  | $1.14 per hour  |
|---------|---------|
|Microsoft pays you 80% of your license cost|   $0.80 per hour     |
|Microsoft keeps 20% of your license cost  |  $0.20 per hour       |
|Microsoft keeps 100% of the Azure usage cost | $0.14 per hour |

### Bring Your Own License (BYOL)

* If you enable the BYOL option, then you have the following cost structure.

|Your license cost  | License fee negotiated and billed by you  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$0.14 per hour*       |

* In this scenario, Microsoft bills $0.14 per hour for use of your published VM image.

|Microsoft bills  | $0.14 per hour  |
|---------|---------|
|Microsoft keeps the Azure usage cost    |   $0.14 per hour     |
|Microsoft keeps 0% of your license cost   |  $0.00 per hour       |

### SaaS App subscription

This option must be configured to sell through Microsoft and can be priced at a flat rate or per user on a monthly or annual basis.
•	If you enable the Sell through Microsoft option for a SaaS offer, then you have the following cost structure.

|Your license cost       | $100.00 per month  |
|--------------|---------|
|Azure usage cost (D1/1-Core)    | Billed directly to the publisher, not the customer |
|*Customer is billed by Microsoft*    |  *$100.00 per month (note: publisher must account for any incurred or pass-through infrastructure costs in the license fee)*  |

* In this scenario, Microsoft bills $100.00 for your software license and pays out $80.00 to the publisher.
* Partners who have qualified for the Reduced Marketplace Service Fee will see a reduced transaction fee on the SaaS offers from May 2019 until June 2020. In this scenario, Microsoft bills $100.00 for your software license and pays out $90.00 to the publisher.

|Microsoft bills  | $100.00 per month  |
|---------|---------|
|Microsoft pays you 80% of your license cost <br> \* Microsoft pays you 90% of your license cost for any qualified SaaS apps   |   $80.00 per month <br> \* $90.00 per month    |
|Microsoft keeps 20% of your license cost <br> \* Microsoft keeps 10% of your license cost for any qualified SaaS apps.  |  $20.00 per month <br> \* $10.00     |

**Reduced Marketplace Service Fee:** For certain SaaS Products that you publish on our Commercial Marketplace, Microsoft will reduce its Marketplace Service Fee from 20% (as described in the Microsoft Publisher Agreement) to 10%.  In order for your Product to qualify, at least one of your products must be designated by Microsoft as either IP co-sell ready or IP co-sell prioritized. To receive this reduced Marketplace Service Fee for the month, eligibility must be met at least five (5) business days before the end of the previous calendar month. Reduced Marketplace Service fee will not apply to VMs, Managed Apps or any other products made available through our Commercial Marketplace.  This Reduced Marketplace Service Fee will be available to qualified offers, with license charges collected by Microsoft between May 1, 2019 and June 30, 2020.  After that time, the Marketplace Service Fee will return to its normal amount.

### Customer invoicing, payment, billing, and collections

**Invoicing and payment**

Publisher can use the customer's preferred invoicing method to deliver subscription or PAYGO software license fees.

**Enterprise agreement** 

If the customer's preferred invoicing method is the Microsoft Enterprise Agreement, your software license fees will be billed using this invoicing method as an itemized cost, separate from any Azure-specific usage costs.

**Credit cards and monthly invoice** 

Customers can also pay using a credit card and a monthly invoice. In this case, your software license fees will be billed just like the Enterprise Agreement scenario, as an itemized cost, separate from any Azure-specific usage costs.

### CSP and Direct Pay Users

For example, if the customer purchases using a credit card:

|Description    |    Date  | Orders/Usage  | Payout |
|----------|----------|-----------|-------------|
|Order Period   | Aug 15, 2019 - Aug 30, 2019 | **Orders report** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Usage report** <br> <ul> <li>CustomerId </li> <li>Customer Name</li> <li>(UsageReference)PurchaseRecordId/LineItemId</li> <li> Estimated Extended Charge <br> Estimated Payout (PC) </li> </ul> |  |
|Term Ending (month)   | Aug 30, 2019 | | |
|Billing Date | Sept 1, 2019 | | |
|Customer Payment Date | Sept 1, 2019 | | |
|Escrow Period (credit cards only, 30 days) | Sept 1, 2019 - Sept 30, 2019 | | **Payout Reporting Corelate** <br> **Orders report** <br> <ul><li>AssetId</li> <li>Reseller Customer Id</li> <li> Reseller Customer Name</li> <li>Payout Status: Unprocessed </li> </ul> <br> **Usage** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Customer Name</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> <li>Payout Status: Unprocessed</li> </ul>|
|Collection Period Start | Sept 1, 2019 | | |
|Collection Period End (maximum, 30 days) | Sept 30, 2019 | | |
|Payout Calculation Date (monthly on the 15th) | Oct 1, 2019 | | **Payout Reporting Corelate** <br> **Orders report** <br> <ul><li>AssetId</li> <li>Reseller Customer Id</li> <li> Reseller Customer Name</li> <li>Payout Status: upcoming </li> </ul> <br> **Usage** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Customer Name</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> <li>Payout Status: Upcoming</li> </ul> |
|Payout Date | Oct 15, 2019 | | **Payout Reporting Corelate** <br> **Orders report** <br> <ul><li>AssetId</li> <li>Reseller Customer Id</li> <li> Reseller Customer Name</li> <li>Payout Status: sent </li> </ul> <br> **Usage** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Customer Name</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> <li>Payout Status: sent</li> </ul> |

### Enterprise agreement

| Description |    Date  | Usage | Payout |
|----------|----------|---------|-----------|
|Order Period | Aug 15, 2019 - Aug 30, 2019 | **Orders report** <br> <ul> <li>OrderId</li> <li>CustomerId</li> </ul> <br> **Usage report** <br> <ul> <li>CustomerId </li> <li>Customer Name</li> <li>(UsageReference)PurchaseRecordId/LineItemId</li> <li> Estimated Extended Charge <br> Estimated Payout (PC) </li> </ul> | |
|Term Ending (quarter) | Sept 30, 2019 | | |
|Billing Date | Oct 15, 2019 | | |
|Escrow Period (credit cards only, 30 days) | n/a | | |
|Collection Period Start | Oct 15, 2019 | | |
|Credit cards only, 30 days | Nov 1st, 2019 - Nov 30th, 2019 | | **CSP and direct pay customers payout Reporting Corelate** <br> <br> **Orders report** <br> <ul><li>AssetId</li> <li>Reseller Customer Id</li> <li> Reseller Customer Name</li> <li>Payout Status: Unprocessed </li> </ul> <br> **Usage** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Customer Name</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> <li>Payout Status: Unprocessed</li> </ul> |
|Collection Period End (maximum, 90 days) | Jan 15, 2020 | | |
|Customer Payment Date | Dec 30, 2019 | | |
|Payout Calculation Date (monthly on the 15th) | Jan 15, 2020 | | |
|Payout Date | Feb 15, 2020 | | **For quarterly based customers payout report corelate** <br> <br> **Orders report** <br> <ul><li>AssetId</li> <li>Reseller Customer Id</li> <li> Reseller Customer Name</li> <li>Payout Status: sent </li> </ul> <br> **Usage** <br> <ul> <li>AssetId</li> <li>CustomerId</li> <li>Customer Name</li> <li>OrderId</li> <li>LineItemId</li> <li>transactionAmount</li> <li>EarningAmountInLastPaymentCurrency</li> <li>Payout Status: sent</li> </ul> |

**Billing and collections** 

Publisher software license billing is presented using the customer selected method of invoicing and follows the invoicing timeline. Customers without an Enterprise Agreement in place are billed monthly for marketplace software licenses. Customers with an Enterprise Agreement are billed monthly via an invoice that is presented quarterly.

When subscription or Pay-as-You-Go pricing models are selected, Microsoft acts as the agent of the publisher and is responsible for all aspects of billing, payment, and collection.

### Publisher payout and reporting

* Any software licensing fees collected by Microsoft as an agent are subject to a 20% transaction fee unless otherwise specified and are deducted at the time of publisher payout.

* Customers typically purchase using the Enterprise Agreement or a credit-card enabled pay-as-you-go agreement. The agreement type determines billing, invoicing, collection, and payout timing.

## Transaction history download export

This option provides a download of each earning line item you see in the Transaction history page, earning type, date, associated transaction amount, customer, product, and other transactional details applicable to your programs.

### The Store users the threshold is $10


| Column name                    | Description                                                                                                                              | 
|--------------------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| earningId                      | Unique identifier for each earning                                                                                                       |
| participantId                  | The primary identity of the partner earning under the program                                                                            | 
| participantIdType              | Mostly program ID for Incentive programs and Seller IF for Store programs and Azure Marketplace                                          | 
| participantName                | Name of the earning partner                                                                                                              | 
| partnerCountryCode             | Location/country of the earning partner                                                                                                  |
| programName                    | Incentive/store program name                                                                                                             | 
| transactionId                  | Unique identifier for the transaction                                                                                                    | 
| transactionCurrency            | Currency in which the original customer transaction occurred (this is not partner location currency)                                     | 
| transactionDate                | Date of the transaction. Useful for programs where many transactions contribute to one earning                                           | 
| transactionExchangeRate        | Exchange rate date used to show the corresponding transaction USD amount                                                                 | 
| transactionAmount              | Transaction amount in the original transaction currency based on which earning is generated                                              | 
| transactionAmountUSD           | Transaction amount in USD                                                                                                                | 
| transactionCountryCode           | Transaction purchase/sold to country code                                                                                                               | 
| lever                          | Indicates business rule for the earning                                                                                                  | 
| earningRate                    | Incentive rate applied on transaction amount to generate an earning                                                                      | 
| quantity                       | Varies based on program. Indicates billed quantity for transactional programs                                                            | 
| earningType                    | Indicates if it is fee, rebate, coop, sell etc.                                                                                          | 
| earningAmount                  | Earning Amount in the original transaction currency                                                                                      |
| earningAmountUSD               | Earning Amount in USD                                                                                                                    |
| earningDate                    | Date of the earning                                                                                                                      |
| earningExchangeRate            | Exchange rate used to show the corresponding USD amount                                                                                  |
| exchangeRateDate               | Exchange rate date used to calculate EarningAmount USD                                                                                   | 
| ClaimId               | Unique identifier for claim (some programs only)                                                                                   |
| PlanId               | Unique identifier for plan(some programs only)                                                                                   | 
| paymentId            | Unique identifier for the payment. This number is usually visible in your bank statement                                            |
| paymentStatus            | Payment status                                            |
| paymentStatusDescription            | Friendly description of payment status                                            |
| PaymentAmountWOTax            |                                             |
| customerId                     | Customer identifier                                                                                                                     |
| customerName                   | Transaction customer name                                                                                                                     |
| partNumber                     | Part number linked to transaction. Microsoft terminology.                                                                                                                     |
| productName                    | Product family name linked to transaction                                                                                                       |
| invoiceNumber                    | Invoice number                                                                                                       |
| subscriptionId                    | Subscription identifier associated with customer                                                                                                       |
| subscriptionStartDate                    | Subscription start date                                                                                                       |
| subscriptionEndDate                    | Subscription end date                                                                                                       |
| resellerId                     | Reseller identifier                                                                                                                      |
| resellerName                   | Reseller name                                                                                                                            |
| distributorId                   | Distributor identifier                                                                                                                            |
| distributorName                   | distributorName                                                                                                                            |
| agreementNumber                   | Agreement number                                                                                                                            |
| agreementStartDate                   | Agreement start date                                                                                                                            |
| agreementEndDate                   | Agreement end date                                                                                                                            |
| workload                   | Workload                                                                                                                            |
| purchaseTypeCode                   | Will always be blank                                                                                                                            |
| purchaseOrderType                   |                                                                                                                             |
| purchaseOrderCoverageStartDate                   |                                                                                                                             |
| purchaseOrderCoverageEndDate                   |                                                                                                                             |
| programOfferingLevel                   |                                                                                                                             |
| Customer TenantID                   |                                                                                                                             |
| externalReferenceId                   |                                                                                                                             |
| externalReferenceIdLabel                   |                                                                                                                             |

### The Store users the threshold is $50.

| Column name                    | Description                                                                                                                              | 
|--------------------------------|------------------------------------------------------------------------------------------------------------------------------------------|
| earningId                      | Unique identifier for each earning                                                                                                       |
| participantId                  | The primary identity of the partner earning under the program                                                                            | 
| participantIdType              | Mostly program ID for Incentive programs and Seller IF for Store programs and Azure Marketplace                                          | 
| participantName                | Name of the earning partner                                                                                                              | 
| partnerCountryCode             | Location/country of the earning partner                                                                                                  |
| programName                    | Incentive/store program name                                                                                                             | 
| transactionId                  | Unique identifier for the transaction                                                                                                    | 
| transactionCurrency            | Currency in which the original customer transaction occurred (this is not partner location currency)                                     | 
| transactionDate                | Date of the transaction. Useful for programs where many transactions contribute to one earning                                           | 
| transactionExchangeRate        | Exchange rate date used to show the corresponding transaction USD amount                                                                 | 
| transactionAmount              | Transaction amount in the original transaction currency based on which earning is generated                                              | 
| transactionAmountUSD           | Transaction amount in USD                                                                                                                | 
| lever                          | Indicates business rule for the earning                                                                                                  | 
| earningRate                    | Incentive rate applied on transaction amount to generate an earning                                                                      | 
| quantity                       | Varies based on program. Indicates billed quantity for transactional programs                                                            | 
| quantityType                   | Indicates type of quantity e.g. Billed quantity, MAU                                                                                     |
| earningType                    | Indicates if it is fee, rebate, coop, sell etc.                                                                                          | 
| earningAmount                  | Earning Amount in the original transaction currency                                                                                      |
| earningAmountUSD               | Earning Amount in USD                                                                                                                    |
| earningDate                    | Date of the earning                                                                                                                      |
| calculationDate                | Date the earning was calculated in the system                                                                                            |
| earningExchangeRate            | Exchange rate used to show the corresponding USD amount                                                                                  |
| exchangeRateDate               | Exchange rate date used to calculate EarningAmount USD                                                                                   | 
| paymentAmountWOTax             | Earning amount (without tax) in Pay To currency for “Sent” payments only                                                                 |
| paymentCurrency                | Pay to currency chosen by partner in the Payment profile. Shown only for sent payments                                                   |
| paymentExchangeRate            | Exchange rate used to calculate paymentAmountWOTax in payment currency using ExchangeRateDate                                            |
| paymentId            | Unique identifier for the payment. This number is usually visible in your bank statement                                            |
| paymentStatus            | Payment status                                            |
| paymentStatusDescription            | Friendly description of payment status                                            |
| customerId                     | Will always be blank                                                                                                                     |
| customerName                   | Will always be blank                                                                                                                     |
| partNumber                     | Will always be blank                                                                                                                     |
| productName                    | Product name linked to transaction                                                                                                       |
| productId                      | Unique product identifier                                                                                                                |
| parentProductId                | Unique parent product identifier. Please note: if there isn’t a parent product for the transaction, then Parent Product ID = Product ID. |
| parentProductName              | Name of the parent product. Please note: if there isn’t a parent product for the transaction, then Parent Product Name = Product Name.   |
| productType                    | Type of product (such as App, Add-on, Game, etc.)                                                                                        |
| invoiceNumber                  | Invoice number (applicable for EA only)                                                                                                  |
| resellerId                     | Reseller identifier                                                                                                                      |
| resellerName                   | Reseller name                                                                                                                            |
| transactionType                | Type of transaction (such as purchase, refund, reversal, chargeback, etc.)                                                               |
| localProviderSeller            | Local provider/seller of record                                                                                                          |
| taxRemitted                    | Amount of tax remitted (sales, use, or VAT/GST taxes).                                                                                   |
| taxRemitModel                  | Party responsible for remitting taxes (sales, use, or VAT/GST taxes).                                                                    |
| storeFee                       | The amount retained by Microsoft as a fee for making the offer available in the marketplace.                                           |
| transactionPaymentMethod       | Customer payment instrument used for the transaction (such as Card, Mobile Carrier Billing, PayPal, etc.)                                |
| tpan                           | Indicates the third-party ad network                                                                                                     |
| customerCountry                | Customer country                                                                                                                         |
| customerCity                   | Customer city                                                                                                                            |
| customerState                  | Customer state                                                                                                                           |
| customerZip                    | Customer zip/postal code                                                                                                                 |
| TenantID                       |                                                                                                                                          |
| externalReferenceId            | Unique identifier for the program                                                                                                        |
| externalReferenceIdLabel       | Unique identifier label                                                                                                                  |
| transactionCountryCode       | Country Code in which transaction happened                                                                                                                  |
| taxCountry       | Sold To Customer Country                                                                                                                  |
| taxState       | Sold To customer State                                                                                                                  |
| taxCity       | Sold to Customer City                                                                                                                  |
| taxZipCode       | Sold To Customer Zip                                                                                                                  |
| LicensingProgramName       |                                                                                                                   |
| Program Code       | String to map with the program name                                                                                                                   |
| earningAmountInLastPaymentCurrency       | Earning amount in the last payment currency (field will be empty, if no prior payment have  been paid)                                                                                                                   |
| lastPaymentCurrency       | Last payment currency (field will be empty, if no prior payment have been paid)                                                                                                                   |
| AssetId       | The unique identifier for the customer orders for your marketplace service.  It represents the transacted purchase line items. There can be multiple assets.                                                                                                                   |
| OrderId       | relates to a customer's invoice                                                                                                                   |
| LineItemId       | individual line in a customer's invoice                                                                                                                   |
| Customer Country       | The country name provided by the customer.  This could be different than the country in a customer’s Azure Subscription.                                                                                                                   |
| Customer EmailAddress       | The e-mail address provided by the end customer.  This could be different than the e-mail address in a customer’s Azure Subscription.                                                                                                                   |
| SkuId       | SKU Id as defined during publishing. An offer may have many SKUs, but a SKU can only be associated with a single offer.                                                                                                                   |

>[!Note]
>All reporting and insights for the transact publishing option are available via the Insights section of the Cloud Partner Portal or Analytics section of Partner Center.

## Billing questions and support

To get help on billing questions, please contact [commercial marketplace publisher support](https://aka.ms/marketplacepublishersupport).
