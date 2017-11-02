---
title: Azure PowerShell Script Sample - Scale the service instance | Microsoft Docs
description: Azure PowerShell Script Sample - Scale the service instance
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

# Scale the service instance

This sample script scales and adds region to the API Management service instance.

You can use the **Azure Cloud Shell** to run the script from [Azure portal](https://portal.azure.com/), as described in [Create a new Azure API Management service instance](../powershell-create-service-instance.md). Alternatively, you can install the Azure PowerShell using the instructions found in the [Azure PowerShell guide](/powershell/azure/overview), and then run `Login-AzureRmAccount` to create a connection with Azure.

## Sample script

[!code-powershell[main](../../../powershell_scripts/api-management/scale-and-addregion-apim-service/scale_and_addregion_apim_service.ps1?highlight=1 "Scale the APIM service instance")]

## Next steps

For more information on the Azure PowerShell module, see [Azure PowerShell documentation](https://docs.microsoft.com/powershell/azure/overview).

Additional Azure Powershell samples for Azure API Management can be found in the [PowerShell samples](../powershell-samples.md).
