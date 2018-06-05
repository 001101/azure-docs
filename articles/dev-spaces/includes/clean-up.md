---
title: "include file"
description: "include file"
ms.custom: "include file"
services: azure-dev-spaces
ms.service: "azure-dev-spaces"
ms.component: "azds-kubernetes"
author: "ghogen"
ms.author: "ghogen"
ms.date: "05/11/2018"
ms.topic: "include"
manager: "douge"
---
## Clean up
To completely delete an Azure Dev Spaces instance on a cluster, including all the dev spaces and running services within it, use the `azds controller delete` command. Bear in mind that this action is irreversible.

The following example lists the Azure Dev Spaces in your active subscription, and then deletes the Dev Spaces resources named 'mydevspace' that is in the resource group 'mydevspace-rg'.

```cmd
    azds list
    azds controller delete --name mydevspace --resource-group mydevspace-rg
```

