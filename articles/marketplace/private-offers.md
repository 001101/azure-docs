---
title: Private offers | Azure
description: Private offers in the Azure Marketplace for app and service publishers.
services: Azure, Marketplace, Compute
documentationcenter:
author: qianw211
manager: pabutler
editor:

ms.assetid: 
ms.service: marketplace
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 11/1/2018
ms.author: qianw211

---
# Private offers

Private Offers on [Microsoft Azure Marketplace](https://azuremarketplace.microsoft.com/) enable publishers to create SKUs that are only visible to targeted customers.

## Private Offers for enterprise purchasing

Enterprise customers want to leverage online marketplaces to find, try out, and buy cloud services. With private offers, publishers can privately share solutions with specific customers in a variety of ways, in order to meet enterprise buying needs:

- **Private pricing** lets publishers extend discounts and off-list pricing from publicly available offerings.
- **Private terms and conditions** enable publishers to tailor terms and conditions to a specific customer.
- **Private offers** let publishers configure their single virtual machine offers, SaaS applications, and Azure applications for an individual customer's needs. The private option also enables publishers to provide preview access to new product features, before launching more broadly to all customers.

Private Offers allow you to acquire more customers with the flexibility to negotiate as you need.

The range of private marketplace features allow enterprises to self-select which offers to make available to their employees. Together, these features open the door to strong Enterprise adoption of cloud marketplaces.  Enterprises can now buy and sell in ways they expect and demand.

## Private Offers on Azure Marketplace

Azure Marketplace publishers are now able to create these private offers via the [Cloud Partner Portal](https://cloudpartner.azure.com/).  With the new quick customer capabilities, you can grant or revoke access to customers in a matter of minutes via the Cloud Partner Portal.

The Private Offer option is now available to single virtual machine offers, SaaS applications, and Azure applications (implemented as solution templates or managed applications).

## Scenarios

Private offers unlock the ability for publishers to create exclusive offers for their closest customers, with customized software and terms.  You can use customized terms in a variety of scenarios:

* Private Offers can provide *specialized pricing* such as discounts, long-term pricing, and bulk software deals to targeted customers.
* Publishers can broaden the audience for the private offers as they make updates, allowing them to run *limited beta releases*. These releases can then be made public later with a few mouse clicks.
* *Special terms and offers* can be entered into these offers. This enables publishers to offer software with special agreements for software deployment.

## Creating Private Offers and SKUs

For existing offers, publishers can easily create new private variations by creating new SKUs and marking them as private.  Private SKUs are only visible and purchasable by the targeted customers. Private SKUs must use a new SKU ID; however, they may reuse the images already published for a public SKU. They allow publishers to create multiple private SKUs for a public SKU without having to submit multiple images. If you update the base image, the images across all the public and private SKUs will be updated seamlessly.

Such offers will have both public SKUs and one or more private SKUs only visible to targeted customers. Depending upon the SKU Id referred during deployment, the disk is deployed, and the corresponding prices are charged to the customers. Publishers can also alter the terms and description shown for these items during deployment.

For **new private offers or SKUs** only available privately, publishers can create their offers as any other offer, and then mark the SKUs private. The offers that only have private SKUs will not have a publicly visible version. These SKUs will not be visible on [Azure Marketplace](https://azuremarketplace.microsoft.com/) or the [Azure portal](https://azure.microsoft.com/features/azure-portal/).

We are giving publishers the ability to target customers using *subscription identifiers*. Publishers may enter an individual subscription ID, or upload a CSV of subscription IDs, to specify access to their private offers.  The customer list can be updated and reflected in the storefront within a matter of minutes.

## Deploying Private Offers

The experience of deploying private offers for the end customers remains the same as any other public offer. Upon viewing the catalog on the Azure portal, customers will be prompted that they have private offers available to them:

![[Private Offers]](./media/marketplace-publishers-guide/private-offer.png)

Private Offers will also appear in search results. They can be deployed via command line and Azure Resource Manager templates like any other offers. Just look out for the “Private” badge.

## Next steps

If you would like to take advantage of these new capabilities, you can get started selling on the [Azure Marketplace](https://azuremarketplace.microsoft.com/sell).
