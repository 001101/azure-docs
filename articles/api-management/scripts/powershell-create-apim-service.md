---
title: Azure PowerShell Script Sample - Create an APIM service | Microsoft Docs
description: Azure PowerShell Script Sample - Create an APIM service
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''

ms.service: api-management
ms.workload: mobile
ms.devlang: na
ms.topic: sample
ms.date: 10/30/2017
ms.author: apimpm
ms.custom: mvc
---

# Create an API Management service

This sample script creates a Developer SKU API Management Service.

You can use the **Azure Cloud Shell** to run the script from [Azure portal](https://portal.azure.com/), as described in [Create a new Azure API Management service instance](../powershell-create-service-instance.md). Alternatively, you can install the Azure PowerShell using the instructions found in the [Azure PowerShell guide](/powershell/azure/overview), and then run `Login-AzureRmAccount` to create a connection with Azure.

## Sample script

[!code-powershell[main](../../../powershell_scripts/api-management/create-apim-service/create_apim_service.ps1?highlight=1 "Create a service")]

## Next steps

For more information on the Azure PowerShell module, see [Azure PowerShell documentation](https://docs.microsoft.com/powershell/azure/overview).

Additional Azure Powershell samples for Azure API Management can be found in the [PowerShell samples](../powershell-samples.md).
