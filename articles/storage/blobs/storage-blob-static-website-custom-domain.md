---
title: 'Tutorial: Enable custom domain with SSL on a static website using Azure CDN - Azure Storage'
description: Learn how to configure a custom domain for static website hosting.
services: storage
author: normesta

ms.service: storage
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: normesta
ms.reviewer: seguler
ms.custom: seodec18
---

# Tutorial: Use Azure CDN to enable a custom domain with SSL for a static website

This tutorial is part two of a series. In it, you learn to enable a custom domain endpoint with SSL for your static website. 

The tutorial shows how to use [Azure CDN](../../cdn/cdn-overview.md) to configure the custom domain endpoint for your static website. With Azure CDN, you can provision custom SSL certificates, use a custom domain, and configure custom rewrite rules all at the same time. Configuring Azure CDN results in additional charges, but provides consistent low latencies to your website from anywhere in the world. Azure CDN also provides SSL encryption with your own certificate. For information on Azure CDN pricing, see [Azure CDN pricing](https://azure.microsoft.com/pricing/details/cdn/).

In part two of the series, you learn how to:

> [!div class="checklist"]
> * Create a CDN endpoint on the static website endpoint
> * Enable custom domain and SSL

## Prerequisites

Before you begin this tutorial, complete part one, [Tutorial: Host a static website on Blob Storage](storage-blob-static-website-host.md). 

## Sign in to the Azure portal

Sign in to the [Azure portal](https://portal.azure.com/) to get started.

## Create a CDN endpoint on the static website endpoint

1. Open the [Azure portal](https://portal.azure.com/) in your web browser.
1. Locate your storage account and display the account overview.
1. Select **Azure CDN** under the **Blob Service** menu to configure Azure CDN.
1. In the **CDN profile** section, specify a new or existing CDN profile. For more information, see [Quickstart: Create an Azure CDN profile and endpoint](../../cdn/cdn-create-new-endpoint.md).
1. Specify a pricing tier for the CDN endpoint. This tutorial uses the **Standard Akamai** pricing tier, because it propagates quickly, typically within a minute. Other pricing tiers may take longer to propagate, but may also offer other advantages. See [Compare Azure CDN product features](../../cdn/cdn-features.md).
1. In the **CDN endpoint name** field, specify a name for your CDN endpoint. The CDN endpoint must be unique across Azure.
1. Specify your the static website endpoint in the **Origin hostname** field. To find your static website endpoint, navigate to the **Static website** settings for your storage account. Copy the primary endpoint and paste it into the CDN configuration.
1. Create the CDN endpoint and wait for it to propagate.
1. Test your CDN endpoint by navigating to it in your browser.

## Enable custom domain and SSL

1. Create a CNAME record with your domain name provider to redirect your custom domain to the CDN endpoint. The CNAME record for the *www* subdomain should be similar to the following:

    ![Specify CNAME record for subdomain www](media/storage-blob-static-website-custom-domain/subdomain-cname-record.png)

1. In the Azure portal, click on the newly created endpoint to configure the custom domain and the SSL certificate.
1. Select **Add custom domain** and enter your domain name, then click **Add**.
1. Select the newly created custom domain mapping to provision an SSL certificate.
1. Set **Custom Domain HTTPS** to **ON**. Select **CDN Managed** to have Azure CDN manage your SSL certificate. Click **Save**.
1. Test your website by accessing your website url.

## Next steps

In part two of this tutorial, you learned how to configure a custom domain with SSL in Azure CDN for your static website.

Follow this link to learn more about static website hosting on Azure Storage.

> [!div class="nextstepaction"]
> [Learn more about static websites](storage-blob-static-website.md)
