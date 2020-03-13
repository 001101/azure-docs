---
title: Encryption of data at rest for the Translator service
titleSuffix: Azure Cognitive Services
description: Encryption of data at rest for the Translator service.
author: erindormier
manager: venkyv

ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: egeaney
#Customer intent: As a user of the Translator service, I want to learn how encryption at rest works.
---

# Encryption of data at rest for the Translator

Translator automatically encrypts your data when it is persisted to the cloud, helping to meet your organizational security and compliance goals.

[!INCLUDE [cognitive-services-about-encryption](../../../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Customer-managed keys are only available on all pricing tiers for the Translator service. 

Follow these steps to enable customer-managed keys for Translator:

1. Create your new regional Translator Text or regional Cognitive Services resource. This will not work with a global resource.
2. Enabled Managed Identity in the Azure portal, and add your customer-managed key information.
3. Create a new workspace in Custom Translator and associate this subscription information. 

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk.md)]

## Next steps

* [Learn more about Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)