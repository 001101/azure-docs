---
title: Plan a SaaS offer for Microsoft commercial marketplace  
description: How to plan for a new software as a service (SaaS) offer for listing or selling in Microsoft AppSource, Azure Marketplace, or through the Cloud Solution Provider (CSP) program using the Microsoft commercial marketplace program in Microsoft Partner Center. 
author: mingshen-ms 
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace 
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/31/2020
---

# Plan a SaaS offer for Microsoft commercial marketplace

This article explains the different options and requirements for publishing a software-as-a-service (SaaS) offer to the Microsoft commercial marketplace. Software as a service (SaaS) is a way to deliver and license software that’s accessed online via a subscription instead of being installed on individual computers. This article will help you prepare your offer for publishing to the commercial marketplace with Partner Center.

## Call to action options

You will define your _call to action_ option on the **Offer setup** tab, as explained in [Create a SaaS offer in the commercial marketplace](create-new-saas-offer.md). The call to action you choose when you create an offer determines what additional information you need to provide.

When you publish a SaaS offer, it will be listed in the storefront of Microsoft AppSource, Azure Marketplace, or both. Your offer can be listed in the commercial marketplace with a variety of call-to-action options as shown in the following table.

| Call-to-action | Transaction process |
| ------------ | ------------- |
| Contact me | The customer contacts you directly from information in your listing.``*`` |
| Free trial | The customer is redirected to your target URL via Azure Active Directory (Azure AD).``*`` |
| Get it now (Free) | The customer is redirected to your target URL via Azure AD.``*`` |
| Sell through Microsoft  | Offers sold through Microsoft are called transactable offers. We bill the customer on your behalf for all _transactable_ offers. Microsoft hosts the SaaS application or solution in the commercial marketplace as a transactable offer. |
|||

``*`` Publishers are responsible for supporting all aspects of the software license transaction, including but not limited to order, fulfillment, metering, billing, invoicing, payment, and collection.

For more information about these call to action options, see [Commercial marketplace transact capabilities](marketplace-commercial-transaction-capabilities-and-considerations.md).

After your offer is published, the call to action option you chose for your offer appears as a button in the upper-left corner of your offer’s listing page. For example, the following screenshot shows an offer listing page in the Azure Marketplace storefront with the **Contact me** and **Test drive** buttons.

***Figure 1: Example of call to action buttons in an offer listing***

![Illustrates an offer listing in the storefront.](./media/listing-options.png)

### Technical requirements

The technical requirements differ depending on the call to action item you choose for your offer.

The _Contact me_ call-to-action option has no technical requirements. You have the option to connect a CRM system to manage customer leads, which is described in the [Customer leads](#customer-leads) section, later in this article.

The _Get it now (Free)_, _Free trial_, and _Sell through Microsoft_ call-to-action options have the following technical requirements:

- Your SaaS application must be a multitenant solution.
- You can enable both Microsoft Accounts (MSA) and [Azure Active Directory (Azure AD)](https://azure.microsoft.com/services/active-directory/) for authenticating users.
- You must create a landing page. After a user purchases your offer, they’re directed to the landing page to facilitate any additional provisioning or setup that’s required. For guidance on creating the landing page, see these articles:
  - Build the landing page for your transactable SaaS offer in the commercial marketplace
  - Build the landing page for your free or trial SaaS offer in the commercial marketplace

These technical requirements apply to the Sell through Microsoft call-to-action only:

- Azure AD with single sign-on (SSO) identity management and authentication is required. For detailed guidance, see Azure Active Directory and transactable SaaS offers in the commercial marketplace.
- You must use with the [SaaS Fulfillment APIs](./partner-center-portal/pc-saas-fulfillment-api-v2.md) to integrate with the Azure Marketplace. You need to expose a service that can interact with the SaaS subscription to create, update, and delete a user account and service plan. Critical API changes must be supported within 24 hours. Non-critical API changes will be released periodically. Diagrams and detailed explanations describing the usage of the collected fields are available in documentation for the [APIs](./partner-center-portal/pc-saas-fulfillment-api-v2.md).

## Additional requirements for selling through Microsoft

To sell your SaaS offer through Microsoft, you must meet these additional requirements.

| Requirement | Details |
| ------------ | ------------- |
| You must create at least one plan | You must create at least one plan for your offer. Your plan is priced based on the pricing model you select before publishing: flat rate or per-user. If you choose the flat rate model, you can optionally include additional dimensions used to charge customers for usage not included in the flat rate. We call this [metering](partner-center-portal/saas-metered-billing.md). More details about plans is provided later in this article. |
| Cancellation | Your offer is cancelable by the customer at any time. |
|||

## Gather information for the Technical configuration tab

You need to gather the following information that you’ll need for the Technical configuration tab, only if you’re creating a transactable offer. If you choose to process transactions independently instead of creating a transactable offer, skip this section and go to [Test drives](#test-drives).

- **Landing page URL**: The SaaS site URL (for example: `https://contoso.com/signup`) that end users will be directed to after acquiring your offer from the marketplace and triggering the configuration process from the newly created SaaS subscription. This URL will receive a token that can be used to call the fulfillment APIs to get provisioning details for your interactive registration page.

  This URL will be called with the marketplace purchase identification token parameter which uniquely identifies the specific end customer's SaaS purchase. You must exchange this token for the corresponding SaaS subscription details using the [resolve API](./partner-center-portal/pc-saas-fulfillment-api-v2.md#resolve-a-purchased-subscription). Those details and any others you wish to collect should be used as part of a customer-interactive web page built in your experience to complete end customer registration and activate their purchase. On this page the user should sign up through one-click authentication by using Azure Active Directory (Azure AD).

  This URL with marketplace purchase identification token parameter will also be called when the end customer launches a managed SaaS experience from Azure Portal or M365 Admin Center. You should handle both flows, when the token is provided first time after purchase for new customers and when it's provided for an existing customer managing his SaaS.

    The Landing page you configure here should be up and running 24/7. This is the only way you’ll be notified about new purchases of your SaaS offers made in the marketplace, or configuration requests of an active subscription of an offer.

- **Connection webhook**: For all asynchronous events that Microsoft needs to send to you (for example, SaaS subscription has been canceled), we require you to provide a connection webhook URL. We will call this URL to notify you on the event.

  The webhook you provide should be up and running 24/7 as this is the only way you’ll be notified about updates about your customers' SaaS subscriptions purchased via the commercial marketplace. If you don't already have a webhook system in place, the simplest configuration is to have an HTTP Endpoint Logic app that will listen for any events being posted to it and then handle them appropriately. For example, `https://prod-1westus.logic.azure.com:443/work`. For more information, see [Call, trigger, or nest workflows with HTTP endpoints in logic apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-http-endpoint).

  > [!NOTE]
  > Inside the Azure portal, we require that you create a single-tenant [Azure Active Directory (AD) app](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) to enable one Azure App ID to be used to authenticate the connection between our two services. To find the [tenant ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in), go to your Azure Active Directory and select **Properties**, then look for the Directory ID number that’s listed. For example, `50c464d3-4930-494c-963c-1e951d15360e`.

- **Azure Active Directory tenant ID**: (also known as directory ID). Inside the Azure portal, we require that you [create an Azure Active Directory (AD) app](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) so we can verify that the connection between our two services is behind an authenticated communication. To find the tenant ID for your Azure Active Directory (AD) app, go to the [App registrations](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) blade in Azure Active Directory. In the **Display name** column, select the app. Then look for the **Directory (tenant) ID** number listed (for example, `50c464d3-4930-494c-963c-1e951d15360e`).

- **Azure Active Directory application ID**: You also need your [application ID](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in). To get its value, go to the [App registrations](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) blade in Azure Active Directory. In the **Display name** column, select the app. Then then look for the Application (client) ID number listed (for example, `50c464d3-4930-494c-963c-1e951d15360e`).

  The Azure AD application ID is associated with your publisher ID in your Partner Center account. You must use the same application ID for all offers in that account.

  > [!NOTE]
  > If the publisher has two or more different accounts in Partner Center, two or more different Azure AD app IDs should be used, each for one of the accounts. Each partner account in Partner Center should use unique Azure AD app ID for all the SaaS offers that are published via this account.

### Requirements for selling through Microsoft

If you want to sell your SaaS offer through Microsoft, you must meet these additional requirements.

|Requirement | Details |
| ------------ | ------------- |
| Billing and metering | Your offer is priced based on the pricing model you select before publishing: _flat rate_ or _per-user_. If you choose the flat rate model, you can optionally include additional dimensions used to charge customers for usage not included in the flat rate. We call this [metering](./partner-center-portal/saas-metered-billing.md). |
| Cancellation | Your offer is cancelable by the customer at any time. |
| Transaction landing page | You host an Azure co-branded transaction landing page where users can create and manage their SaaS service account. |
| Subscription API  | You expose a service that can interact with the SaaS subscription to create, update, and delete a user account and service plan. Critical API changes must be supported within 24 hours. Non-critical API changes will be released periodically. |
|||

### Test drives
You can choose to enable a test drive for your SaaS app. Test drives give customers access to a preconfigured environment for a fixed number of hours. You can enable test drives for any publishing option, however this feature has additional requirements. To learn more about test drives, see [What is a test drive?](what-is-test-drive.md). For information about configuring different kinds of test drives, see [Test drive technical configuration](test-drive-technical-configuration.md).

> [!TIP]
> A test drive is different from a [free trial](#free-trials). You can offer a test drive, free trial, or both. They both provide your customers with your solution for a fixed period-of-time. But, a test drive also includes a hands-on, self-guided tour of your product’s key features and benefits being demonstrated in a real-world implementation scenario.

## Customer leads

You must connect your offer to your Customer Relationship Management (CRM) system to collect customer information. The customer will be asked for permission to share their information. These customer details, along with the offer name, ID, and marketplace storefront where they found your offer, will be sent to the CRM system that you've configured. The commercial marketplace supports a variety of CRM systems, along with the option to use an Azure table or configure an HTTPS endpoint using Power Automate.
You can add or modify a CRM connection at any time during or after offer creation. For detailed guidance, see
 [Lead management for commercial marketplace](lead-management-for-cloud-marketplace.md).

## Legal contracts

To simplify the procurement process for customers and reduce legal complexity for software vendors, Microsoft offers a standard contract you can use for your offers in the commercial marketplace. When you offer your software under the standard contract, customers only need to read and accept it one time, and you don't have to create custom terms and conditions.

If you choose to use the standard contract, you have the option to add universal amendment terms and up to 10 custom amendments to the standard contract. You can also use your own terms and conditions instead of the standard contract. For detailed information, see [Standard contract for Microsoft commercial marketplace](standard-contract.md).

> [!NOTE]
> After you publish an offer using the standard contract for the commercial marketplace, you are not able to use your own custom terms and conditions. It is an "or" scenario. You either offer your solution under the standard contract or your own terms and conditions. If you want to modify the terms of the standard contract you can do so through Standard Contract Amendments.

## Offer listing details

When you [create a new SaaS offer](create-new-saas-offer.md) in Partner Center, you will enter text, images, optional videos, and other details on the **Offer listing** page. This is the information that customers will see when they discover your offer listing in the commercial marketplace.

***Figure 2: Example offer listing in the commercial marketplace***

:::image type="content" source="./partner-center-portal/media/example-saas-1.png" alt-text="Illustrates how this offer appears in Microsoft AppSource.":::

**Call-out descriptions**

1. Logo
2. Categories
3. Industries
4. Support address (link)
5. Terms of use
6. Privacy policy
7. Offer name
8. Summary
9. Description
10. Screenshots/videos
11. Documents

***Figure 3: Example offer listing in the Azure portal***

![Illustrates an offer listing in the Azure portal.](./partner-center-portal/media/example-managed-services.png)

**Call out descriptions**

1. Title
1. Description
1. Useful links
1. Screenshots

> [!NOTE]
> Offer listing content is not required to be in English if the offer description begins with the phrase "This application is available only in [non-English language]".

To help create your offer more easily, prepare some of these items ahead of time. The following items are required unless otherwise noted.

- **Name**: This name will appear as the title of your offer listing in the commercial marketplace. The name may be trademarked. It cannot contain emojis (unless they are the trademark and copyright symbols) and must be limited to 50 characters.
- **Search results summary**: Describe the purpose or function of your offer as a single sentence with no line breaks in 100 characters or less. This summary is used in the marketplace listing(s) search results.
- **Description**: This description will be displayed in the marketplace listing(s) overview. Consider including a value proposition, key benefits, intended user base, any category or industry associations, in-app purchase opportunities, any required disclosures, and a link to learn more. You can enter up to 3,000 characters of text in this box, including HTML markup. For additional tips, see [Write a great app description](https://docs.microsoft.com/windows/uwp/publish/write-a-great-app-description).

  > [!NOTE]
  > This text box has rich text editor controls that you can use to make your description more engaging. You can also use HTML tags to format your description. Most tags require both opening and closing tags. For information about HTML formatting, see [Supported HTML tags for offer descriptions](supported-html-tags.md).

- **Getting Started Instructions**: If you choose to sell your offer through Microsoft (transactable offer), this field is required. These are instructions to help customers connect to your SaaS offer. You can add up to 3,000 characters of text and links to more detailed online documentation.
- **Search keywords** (optional): Provide up to three search keywords that customers can use to find your offer in the marketplace(s).

  > [!TIP]
  > The text you enter in the **Name** and **Description** boxes are automatically included in search.

- **Privacy policy link**: The URL for your company’s privacy policy. You must provide a valid privacy policy and are responsible for ensuring your app complies with privacy laws and regulations.
- **Contact information**: You must designate the following contacts from your organization:
  - **Support contact**: Provide the name, phone, and email for Microsoft partners to use when your customers open tickets. You must also include the URL for your support website.
  - **Engineering contact**: Provide the name, phone, and email for Microsoft to use directly when there are problems with your offer. This contact information isn’t listed in the marketplace.
  - **CSP Program contact** (optional): Provide the name, phone, and email if you opt in to the CSP program, so those partners can contact you with any questions. You can also include a URL to your marketing materials.
- **Useful links** (optional): You can provide links to various resources for users of your offer. For example, forums, FAQs, and release notes.
- **Supporting documents**: You can provide up to three customer-facing documents, such as whitepapers, brochures, checklists, or PowerPoint presentations.
- **Media – Logos**: Provide logos as .png files in the following sizes:
  - Small: 48 x 48 pixels (required)
  - Medium: 90 x 90 pixels (required)
  - Large: 216 x 216 pixels (required)
  - Wide: 255 x 115 (optional)
- **Media - Screenshots**: You must add at least one and up to five screenshots with the following requirements, that show how your offer works:
  - 1280 x 720 pixels
  - .png file
  - Must include a caption
- **Media - Videos** (optional): You can add up to four videos with the following requirements, that demonstrate your offer:
  - Name
  - URL: Must be hosted on YouTube or Vimeo only.
  - Thumbnail: 1280 x 720 .png file

> [!TIP]
> To publish your offer to the commercial marketplace, your offer must meet the general [commercial marketplace certification policies](https://docs.microsoft.com/legal/marketplace/certification-policies#100-general) and the [software as a service policies](https://docs.microsoft.com/legal/marketplace/certification-policies#1000-software-as-a-service-saas).

## Define a preview audience for testing your offer
A preview audience can access your offer prior to being published live in the marketplaces in order to test the end-to-end functionality before you publish it live. On the **Preview audience** tab, you can define a limited preview audience. This setting is not available if you choose to process transactions independently instead of selling your offer through Microsoft. If so, you can skip this section and go to [Additional sales opportunities](#additional-sales-opportunities).

> [!NOTE]
> The preview audience differs from a private plan. A private plan is one you make available only to a specific audience you choose. This enables you to negotiate a custom plan with specific customers. For more details, see the next section: Plans.

You can send invites to Microsoft Account (MSA) or Azure Active Directory (AAD) email addresses. Add up to 10 email addresses manually or import up to 20 with a .csv file. If your offer is already live, you can still define a preview audience for testing any changes or updates to your offer.

## Plans

Offers sold through Microsoft (transactable offers) require at least one plan. A plan defines the solution scope and limits, and the associated pricing. You can create multiple plans for your offer to give your customers different technical and pricing options.

If you choose to process transactions independently instead of creating a transactable offer, this tab is not visible. If so, skip this section and go to [Additional sales opportunities](#additional-sales-opportunities).

***Figure 4: Example offer listing in the commercial marketplace showing three plans***

![Illustrates a marketplace listing of an offer with three plans.](./partner-center-portal/media/marketplace-plan.png)

**Call-out descriptions**
1. Software plan
1. Description


The **Plan overview** tab of Partner Center will take you to additional tabs where you complete all details for one or more plans.

> [!NOTE]
> If you choose to publish your transactions independently, this tab is not shown. If so, go to [Additional sales opportunities](#additional-sales-opportunities).

The first pieces of information you are asked to provide are a name and an ID for your plan:

- **Plan ID**: Create a unique plan ID for each plan in this offer. This ID will be visible to customers in the product URL and Azure Resource Manager templates (if applicable). Max 50 characters and must consist only of lowercase, alphanumeric characters, dashes, or underscores. You can’t change this ID after you publish the offer.

- **Plan Name**: The plan name is used to differentiate software plans that may be a part of the same offer (for example, Offer name: Windows Server 2016, Windows Server 2019). This name must be unique across all the plans in the offer. Max 50 characters. Customers will see this name when deciding which plan to select within your offer.

On the **Plan listing** tab, add a plan description as it will appear to your customers in the commercial marketplace. Explain what makes this software plan unique and any differences from other software plans within your offer. This description may contain up to 500 characters.

### Pricing models

You must associate a pricing model with each plan: either _flat rate_ or _per user_. All plans in the same offer must be associated with the same pricing model. For example, an offer cannot have one plan that's flat rate and another plan that’s per user.

**Flat rate** – Enable access to your offer with a single monthly or annual flat rate price. This is sometimes referred to as site-based pricing. With this pricing model, you can optionally define metered plans that use the marketplace metering service API to charge customers for usage that isn't covered by the flat rate. For more information on metered billing, see [Metered billing using the marketplace metering service](./partner-center-portal/saas-metered-billing.md). You should also use this option if the usage behavior is in bursts for your SaaS service.

> [!TIP]
> We recommend that you create plans that are best suited to the usage patterns of your target customer base. This reduces users frequently switching plans based on their changes in usage. For an example of an offer with three metered billing plans, see [Sample offer](./partner-center-portal/saas-metered-billing.md#sample-offer).

**Per user** – Enable access to your offer with the price based on the number of users who can access the offer or occupy seats. With this user-based model, you can set the minimum and maximum number of users supported by the plan. This way, different price points can be configured based on the number of users by configuring multiple plans. These fields are optional. If left unselected, the number of users will be interpreted as not having a limit (min of 1 and max of as many as your service can support). These fields may be edited as part of an update to your plan.

> [!IMPORTANT]
> After your offer is published, the pricing model choice cannot be changed. In addition, all plans for the same offer must share the same pricing model.

The **Pricing and availability** tab is where you will define the plan’s markets, pricing, and availability:

- **Markets**: Every plan must be available in at least one market.

- **Pricing**: For detailed pricing guidance, see [Pricing and billing](#pricing-and-billing). If you have already set prices for your plan in United States Dollars (USD) and add another market location, the price for the new market will be calculated according to the current exchange rates. After saving your changes, you will see an **Export prices (xlsx)** link that you can use to review and change the price for each market before publishing.

### Free trials

You can enable a one-month free trial for your offer, which will automatically convert to a paid offer at the end of the trial. When a customer selects a free trial, we collect their billing information, but billing doesn’t start until the trial is converted to a paid subscription.

SaaS offers through the commercial marketplace enable you to provide a one-month free trial when selling through Microsoft. Free trials are supported for all billing models and terms except metered plans. This option gives customers one month of free access to try your offer.

If you choose to enable a free trial for one or more plans within your offer, the customer can't convert to a paid subscription before the end of the initial one-month period. During this time, customers can evaluate any of the supported plans within the offer that have a free trial enabled and switch between them. If the customer doesn’t switch to a free trial in a different plan before the end of the trial period, the free trial is automatically converted to a paid subscription of the plan they trying.

> [!NOTE]
> If the customer chooses to convert to a plan without free trials, the conversion will happen, but the free trial will end immediately and any data will be lost. After a customer starts paying for a plan, they can’t get a free trial on the same plan again, even if they switch to a plan that supports free trials.

To obtain information on customer subscriptions currently participating in a free trial, use the new API property isFreeTrial, which will be marked as true or false. For more information, see the [SaaS Get Subscription API](./partner-center-portal/pc-saas-fulfillment-api-v2.md#get-subscription).

### Plan visibility

You can make your plan private, which means it will be available only to a specific audience. This audience is defined by Azure tenant IDs with the option to include a description of each tenant you assign. You can enter up to 10 tenant IDs manually, or import up to 20,000 tenant IDs with a .csv file that you can download from the **Pricing and availability** tab in Partner Center.

After your offer is published with a private plan, you can update the audience or choose to make the plan available to everyone. After a plan is published as visible to everyone it must remain visible to everyone and cannot be configured as a private plan again.

> [!NOTE]
> The private audience differs from a preview audience. On the **Preview audience** tab, you can define an audience who can preview your offer prior to the offer being published live in the marketplace. While the private audience designation only applies to a specific plan, the preview audience can view all plans (private or not), but only during the limited preview period while the plan is being tested and validated.

### Pricing and billing

For SaaS apps that run in your (the publisher’s) Azure subscription, infrastructure usage is billed to you directly; customers do not see actual infrastructure usage fees. You should bundle Azure infrastructure usage fees into your software license pricing to compensate for the cost of the infrastructure you deployed to run the solution.

SaaS app offers, that are sold through Microsoft, support monthly or annual billing based on a flat fee, per user, or consumption charges using the [metered billing service](./partner-center-portal/saas-metered-billing.md). Commercial marketplace operates on an agency model, whereby publishers set prices, Microsoft bills customers, and Microsoft pays revenue to publishers while withholding an agency fee.

This is a sample breakdown of costs and payouts to demonstrate the agency model. In this example, Microsoft bills $100.00 to the customer for your software license and pays out $80.00 to the publisher.

| Your license cost | $100 per month |
| ------------ | ------------- |
| Azure usage cost (D1/1-Core) | Billed directly to the publisher, not the customer |
| Customer is billed by Microsoft | $100.00 per month (Publisher must account for any incurred or pass-through infrastructure costs in the license fee) |
| **Microsoft bills** | **$100 per month** |
| Microsoft pays you 80% of your license cost<br>`*` For qualified SaaS apps, Microsoft pays 90% of your license cost| $80.00 per month<br>``*`` $90.00 per month |
|||

`*` Partners who have qualified for the Reduced Marketplace Service Fee will see a reduced transaction fee on the SaaS offers from May 2019 until June 2020.

**Reduced Marketplace Service Fee** – For  certain SaaS offers that you have published on our Commercial Marketplace, Microsoft will reduce its Marketplace Service Fee from 20% (as described in the Microsoft Publisher Agreement) to 10%. In order for your offer to qualify, at least one of your offers must have been designated by Microsoft as being either IP co-sell ready or IP co-sell prioritized. Eligibility must be met at least five (5) business days before the end of each calendar month in order to receive this reduced Marketplace Service Fee for the month. The Reduced Marketplace Service Fee does not apply to VMs, Managed Apps, or any other products made available through our Commercial Marketplace. The Reduced Marketplace Service Fee will only be available to qualified offers for license charges collected by Microsoft between May 1, 2019 and June 30, 2020. After this time, the Marketplace Service Fee will return to its normal amount.

## Additional sales opportunities

You can choose to opt into Microsoft-supported marketing and sales channels. When creating your offer in Partner Center, you will see two tabs toward the end of the process:

- **Co-sell with Microsoft**: This option lets Microsoft sales teams consider your solution when evaluating their customers’ needs. See [Co-sell option in Partner Center](./partner-center-portal/commercial-marketplace-co-sell.md) for detailed information on how to prepare your offer for evaluation.

- **Resell through CSPs**: Use this option to allow Microsoft Cloud Solution Providers (CSP) partners to resell your solution as part of a bundled offer. See [Cloud Solution Providers](https://docs.microsoft.com/azure/marketplace/cloud-solution-providers) for more information.

## Next steps

- [Create a SaaS offer in the commercial marketplace](create-new-saas-offer.md)
- [Best practices for marketplace offer listings](gtm-offer-listing-best-practices.md)
