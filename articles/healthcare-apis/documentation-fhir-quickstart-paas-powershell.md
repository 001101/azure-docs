---
title: Deploy PaaS FHIR Server using PowerShell
description: Deploy PaaS FHIR Server using PowerShell.
services: healthcare-apis
author: hansenms
ms.service: healthcare-apis
ms.topic: quickstart 
ms.date: 02/11/2019.
ms.author: mihansen
---

# Quickstart: Deploy PaaS FHIR Server using PowerShell

In this quickstart, you can deploy the PaaS Microsoft FHIR Server for Azure Using PowerShell.

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Prerequisites

- Azure Subscription

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## Clean up resources

If you're not going to continue to use this application, delete the resource group
with the following steps:

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name $rg.ResourceGroupName
```

## Next steps

Advance to the next article to learn how to create...