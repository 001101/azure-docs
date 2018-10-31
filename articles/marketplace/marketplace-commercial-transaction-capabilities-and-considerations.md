---  
title: Marketplace ‎Commercial Transaction Capabilities and Considerations | Azure
description: This article describes the Transact pricing, billing, invoicing, and payout considerations for an offer type.
services:  Azure, Marketplace, Compute, Storage, Networking, Transact Offer Type
documentationcenter:
author: yijenj
manager: nuno costa
editor:

ms.assetid: 
ms.service: marketplace
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 10/29/2018
ms.author: yijenj

---  
# Introduction

Azure marketplace publishing options offer unique ways to connect cloud software and service providers with customers. We will cover the following topics related to commerce in the Azure Marketplace:

* General and offer type specific requirements.
* Free software trials and discounts.
* Customer billing, invoicing, and payment.
* Publisher pricing models, payout, and reporting.

**List & Trial publishing options**

In Azure marketplace, publishers can leverage the list and trial publishing options for promotional and user acquisition purposes. With the list or trial publishing options, Microsoft does not participate directly in the publisher’s software license transactions, and there is no associated transaction fee. Publishers are responsible for supporting all aspects of the software license transaction, including but not limited to: order, fulfillment, metering, billing, invoicing, payment, and collection. With the list and trial publishing options, publishers keep 100% of publisher software licensing fees collected from the customer. 

**Transact publishing option**

In addition to the list and trial publishing options, the transact publishing option is available to Azure Marketplace publishers.   It takes advantage of Microsoft’s globally available commerce capabilities. This option allows Microsoft to host cloud marketplace transactions on behalf of the publisher.

## Transact General Overview

When using the transact publishing option, Microsoft enables the sale and deployment of third-party software to the customer’s Azure subscription. The publisher can  choose from pricing models supported in Azure Marketplace.

To use the Transact publishing option in Azure Marketplace, create an offer using one of these three offer types: Virtual Machines, Azure Applications, or SaaS Apps. 

![[Transacting Enterprise Deals in Azure Marketplace]](./media/marketplace-publishers-guide/Transact-enterprise-deals.png)

**Virtual Machines and Azure Applications**

For Virtual Machines and Azure Applications, the Azure infrastructure usage fees are billed to the customer’s Azure subscription.  Infrastructure usage fees are priced and presented separately from the software provider’s licensing fees on the customer’s invoice.

**SaaS Apps** 

For SaaS Apps, the publisher must account for Azure infrastructure usage fees, and software licensing fees as a single cost item.  It is represented as a flat monthly fee to the customer. The Azure infrastructure usage is managed and billed to the partner directly.  Actual infrastructure usage fees are not seen by the customer.  Publishers typically opt to bundle Azure infrastructure usage fees into their software license pricing.  Software licensing fees are not metered or consumption based.

**Private Offers** 

In addition to using offer types and billing models to monetize an offer, publishers can transact a private version of solution offer, complete with negotiated, deal-specific pricing, and custom configurations using a customized image. Private offers are supported by all 3 transact publishing options. 

## Transact Billing Models 

Depending on the transaction option used, the publisher’s software license fees can be presented as:  

* Free: No charge for software licenses. 

* Bring your own license (BYOL): Any applicable charges for software licenses are managed directly between the publisher and customer. Microsoft only passes through Azure infrastructure usage fees. (Virtual Machines and Azure Applications only.)

* Pay-as-you-go (PAYG): Software license fees are presented as a per-hour, per core (aka vCPU) pricing rate based on the Azure infrastructure used. (Virtual Machines and Azure Applications only.)

* Subscription pricing: Software license fees are presented as a monthly, recurring fee.  (SaaS Apps and Azure Applications – Managed Apps only.) 

* Free software trial: No charge for software licenses for 30- or 90-days.

**Free and bring-your-own-license (BYOL) pricing** 

When publishing a free or bring-your-own-license transaction offer, Microsoft does not play a role in facilitating the sales transaction for your software license fees. Like the list and trial publishing options, the publisher keeps 100% of software license fees. 

**Pay-as-you-go (PAYGO) and subscription pricing** 

When publishing a pay-as-you-go or subscription transaction offer, Microsoft provides the technology and services to process software license purchases, returns, and chargebacks. In this scenario, the publisher authorizes Microsoft to act as an agent for these purposes. The publisher allows Microsoft to facilitate the software licensing transaction, while retaining their designation as the seller, provider, distributor, and licensor.

Microsoft enables customers to order, license, and use publisher software, subjecting to the terms and conditions of both Azure Marketplace and the publisher’s end-user licensing agreement (see Cloud Partner Portal). Publishers must provide their end-user licensing agreement in the marketplace offer.

Orders processed through marketplace are billed to the customer’s Azure subscription in a single bill, the same billing method as the customer’s Azure infrastructure costs. Customers can use the preferred invoicing method and payment instrument used for their Azure subscription billing.

**Free software trials** 

For transact publishing scenarios, the publisher can make a software license available free for 30- or 90 days. This capability does not eliminate the cost of Azure infrastructure usage that is driven by use of the partner solution.

**Private offers and discounting** 

For each transact publishing scenario, the publisher can optionally make available privately negotiated pricing and terms for an offer. For a virtual machine offer, the publisher can also optionally make available custom images.

## Transact Requirements 

The Transact requirements for different offer types are covered in this section.

### For all offer types

**Dev Center and Microsoft account** 

* Both a Dev Center and a Microsoft account are required for the transact publishing option, regardless of the offer’s pricing model.
* The Dev Center account holds all relevant financial details required for Microsoft to collect fees from the customer on the publisher’s behalf and pay out the publisher.
* Although you may use the same organizational or Microsoft sign-in details across both accounts, Dev Center is a separate account from the Cloud Publisher Portal account. To use the transact publishing option, the publisher must complete the Dev Center account sign-up process, in addition to signing up for access to the Cloud Partner Portal.

*For more information on setting up these accounts, see [Becoming a publisher](https://docs.microsoft.com/azure/marketplace/become-publisher).* 

**For specific offer types** 

The transact publishing option is only available for use with the following marketplace offer types: 

**Virtual Machine** 

Select from free, bring-your-own-license, or pay-as-you-go-pricing models and present as SKUs defined at the offer level. On the customer’s Azure bill, Microsoft presents the publisher software license fees separately from the underlying Azure infrastructure fees. Azure infrastructure fees are driven by use of the publisher software.

**Azure Applications: Solution Template or Managed App** 

Must provision one or more virtual machines, and pulls through the sum of the virtual machine pricing. For managed apps, a single plan, flat-rate monthly subscription can be selected as the pricing model in lieu of the virtual machine pricing. In both cases, Azure infrastructure usage fees are passed to the customer separately from software license fees, but on the same billing statement.

**Pay-As-You-Go and Bring Your Own License Billing Options** 

Consider the following examples.  

* If you enable the Pay-As-You-Go option, then you have the following result.   

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

* If you enable the BYOL option, then you have the following result.

|Your license cost  | License fee negotiated and billed by you  |
|---------|---------|
|Azure usage cost (D1/1-Core)    |   $0.14 per hour     |
|*Customer is billed by Microsoft*    |  *$0.14 per hour*       |

* In this scenario, Microsoft bills $0.14 per hour for use of your published VM image.

|Microsoft bills  | $0.14 per hour  |
|---------|---------|
|Microsoft keeps the Azure usage cost    |   $0.14 per hour     |
|Microsoft keeps 0% of your license cost   |  $0.00 per hour       |

**SaaS App Subscription (aka Sell through Azure)** 

Must be configured to sell through Microsoft and can be priced via one or more flat-rate monthly Plans defined at the offer level.

Consider the following examples.

* If you enable the Sell through Azure option, then you have the following result.

|Your license cost       | $100.00 per month  |
|--------------|---------|
|Azure usage cost (D1/1-Core)    | Billed directly to the publisher, not the customer |
|*Customer is billed by Microsoft*    |  *$100.00 per month (note: publisher must account for any incurred or pass-through infrastructure costs in the license fee)*  |

* In this scenario, Microsoft bills $100.00 for your software license and pays out $80.00 to the publisher.

|Microsoft bills  | $100.00 per month  |
|---------|---------|
|Microsoft pays you 80% of your license cost    |   $80.00 per month     |
|Microsoft keeps 20% of your license cost   |  $20.00 per month       |

### Private Offers

This pricing can be the higher or lower than the publicly displayed pricing and can be used to discount or add a premium for an offer. Private offers can be made available to one or more customers by white-listing their Azure subscription at the offer level.

#### Customer invoicing, payment, billing, and collections

**Invoicing and Payment**

Publisher can use the customer’s preferred invoicing method to deliver subscription or PAYGO software license fees.

**Enterprise Agreement** 

If the customer’s preferred invoicing method is the Microsoft Enterprise Agreement, your software license fees will be billed via this invoicing method as an itemized cost, separate from any Azure-specific usage costs.

**Credit cards and Monthly invoice** 

Customers can also pay using a credit card and a monthly invoice. In this case, your software license fees will be billed just like the Enterprise Agreement scenario, as an itemized cost, separate from any Azure-specific usage costs.

**Free credits and monetary commitment** 

Some customers elect to prepay Azure via a monetary commitment in the Enterprise Agreement or have been provided free credits for use with Azure. Although these credits can be used to pay for Azure usage, they cannot be used to pay for publisher software license fees.

**Billing and collections** 

Publisher software license billing is presented via the customer selected method of invoicing and follows the invoicing timeline. Customers without an Enterprise Agreement in place are billed monthly for marketplace software licenses. Customers with an Enterprise Agreement are billed monthly via an invoice that is presented quarterly.

When subscription or PAYGO pricing models are selected, Microsoft acts as the agent of the publisher and is responsible for all aspects of billing, payment, and collection.

#### Publisher payout and reporting

* Any software licensing fees collected by Microsoft as an agent are subject to a 20% transaction fee unless otherwise specified, and are deducted at the time of publisher payout.

* Customers typically purchase using the Enterprise Agreement or a credit-card enabled pay-as-you-go agreement. The agreement type dictates billing, invoicing, collection, and payout timing.

Consider the following examples.

If the customer purchases using a credit card:

|Description    |    Date  |
|----------|----------|
|Order Period   | August 15, 2018 - August 30, 2018 |
|Term Ending (month)   | August 30, 2018 |
|Billing Date | September 1, 2018 |
|Customer Payment Date | September 1, 2018 |
|Escrow Period (credit cards only, 30 days) | September 1, 2018 – September 30, 2018 |
|Collection Period Start | September 1, 2018 |
|Collection Period End (maximum, 30 days) | September 30, 2018 |
|Payout Calculation Date (monthly on the 15th) | October 1, 2018 |
|Payout Date | October 15, 2018 |

If the customer purchases using an Enterprise Agreement:  
|Description    |    Date  |
|----------|----------|
|Order Period | August 15, 2018 - August 30, 2018 |
|Term Ending (quarter) | September 30, 2018 |
|Billing Date | October 15, 2018 |
|Escrow Period (credit cards only, 30 days) | n/a |
|Collection Period Start | October 15, 2018 |
|Collection Period End (maximum, 90 days) | January 15, 2018 |
|Customer Payment Date | December 30, 2018 |
|Payout Calculation Date (monthly on the 15th) | January 15, 2018 |
|Payout Date | February 15, 2019 |

>[!NOTE] 
>All reporting and insights for the transact publishing option are available via the Insights section of the Cloud Partner Portal.

#### Billing questions and support

For more information and legal policies, see the [Publisher Agreement](https://cloudpartner.azure.com/Content/Unversioned/PublisherAgreement2.pdf) (available via the Cloud Partner Portal).

To get help understanding any aspect of the transact publishing option, [create a support incident](https://support.microsoft.com/getsupport?wf=0&tenant=classiccommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=16230&forceorigin=esmc&ccsid=636764613233453423) and choose Virtual Machines or Web Apps (aka SaaS Apps) depending on the offer type used.

## Next Steps

* Review the eligibility requirements in the publishing options by offer type section to finalize the selection and configuration of your offer.
* Review the publishing patterns by storefront for examples on how your solution maps to an offer type and configuration.
* Sign in to the [Cloud Partner Portal](https://cloudpartner.azure.com) to create and configure your offer.
