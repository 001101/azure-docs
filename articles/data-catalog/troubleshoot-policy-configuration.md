---
title: How to configure the Azure Active Directory policy for Azure Data Catalog
description: ou may encounter a situation where you can sign in to the Azure Data Catalog portal, but when you attempt to sign in to the data source registration tool, you encounter an error message.
services: data-catalog
author: markingmyname
ms.author: maghan
ms.author: kfile
ms.service: data-catalog
ms.topic: conceptual
ms.date: 04/06/2019
---

# Azure Active Directory policy configuration

You may encounter a situation where you can sign in to the Azure Data Catalog portal, but when you attempt to sign in to the data source registration tool, you encounter an error message that prevents you from signing in. This error may occur when you are on the company network or when you are connecting from outside the company network.

## Registration tool

The registration tool uses *forms authentication* to validate user sign-ins against Azure Active Directory. For successful sign-in, an Azure Active Directory administrator must enable forms authentication in the *global authentication policy*.

With the global authentication policy, you can enable authentication separately for intranet and extranet connections, as shown in the following image. Sign-in errors may occur if forms authentication is not enabled for the network from which you're connecting.

 ![Azure Active Directory global authentication policy](./media/troubleshoot-policy-configuration/global-auth-policy.png)

For more information, see [Configuring authentication policies](https://technet.microsoft.com/library/dn486781.aspx).