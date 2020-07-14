---
title: Add a preview audience for your SaaS offer 
description: How to add a preview audience for your software  as a service (SaaS) offer in Microsoft Partner Center. 
author: mingshen-ms
ms.author: mingshen
ms.reviewer: dannyevers
ms.service: marketplace 
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 07/31/2020
---

# Add a preview audience for your SaaS offer

As you create your software as a service (SaaS) offer in Partner Center, you need to define a preview audience who can review your offer listing before it goes live. This article explains how to configure a preview audience.

## Define a preview audience

On the **Preview audience** tab, you can define a limited audience who can review your software as a service (SaaS) offer before you publish it live to the broader marketplace audience.

> [!NOTE]
> This tab is not visible if you choose to process transactions independently. If so, go to [Marketing options](create-new-saas-offer-marketing.md).

You can send invites to Microsoft Account (MSA) or Azure Active Directory (Azure AD) email addresses. Add a minimum of one and up to 10 email addresses manually or import up to 20 with a .csv file. You can update the preview audience list at any time.

> [!TIP]
> The preview audience differs from a private audience. A preview audience is allowed access to your offer before it’s published live in the marketplaces. You may also choose to create a plan and make it available only to a private audience. We’ll show you how to create a private plan later in this article.

**Add email addresses manually**

1. On the **Preview Audience** tab, add a single Azure AD or MSA email address and an optional description in the boxes provided.
2. To add another email address, select the **Add another email** link.
3. Select **Save draft** before continuing to the next tab, **Technical configuration**.
4. Go to [Provide technical details](#provide-technical-details).

**Add email addresses using the CSV file**

1. On the **Preview Audience** tab, select the **Export Audience (csv)** link.
2. Open the .CSV file in an application, such as Microsoft Excel.
3. In the CSV file, in the **ID** column, enter the email addresses you want to add to the preview audience.
4. In the **Description** column, you can optionally add a description for each email address.
5. In the **Type** column, add **MixedAadMsaId** to each row that has an email address.
6. Save the file as a .CSV file.
7. On the **Preview Audience** tab, select the **Import Audience (csv)** link.
8. In the **Confirm** dialog box, select **Yes**.
9. Select the .CSV file and then select **Open**.
10. Select **Save draft** before continuing to the next tab: **Technical configuration**.

## Next steps

- [Provide technical details for your SaaS offer](create-new-saas-offer-technical.md)
