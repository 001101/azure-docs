---
title: Upgrade your QnAMaker service | Microsoft Docs
titleSuffix: Azure
description: How to upgrade your QnAMaker service
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
---

# Upgrade your QnAMaker service
You can choose to upgrade individual components of the QnAMaker stack after the initial creation. See the details of the dependent components and SKU selection [here](https://aka.ms/qnamaker-docs-capacity).

## Upgrade QnAMaker Management SKU
To upgrade the QnAMaker management SKU:
1. Go to your QnAMaker resource in the Azure portal, and click on the **Pricing tier**.
![QnAMaker resource](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)
2. Choose the appropriate SKU and press **Select**.
![QnAMaker pricing](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## Upgrade App service
You can [scale up](https://docs.microsoft.com/en-us/azure/app-service/web-sites-scale) or scale down the App service.
1. Go to the App service resource in the Azure portal, and select **scale up** or **scale down** options as required.
![QnAMaker app service scale](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## Upgrade Azure Search service
Currently it is not possible to perform an in place upgrade of the Azure search SKU. However, you can create a new Azure search resource with the desired SKU, restore the data to the new resource, and then link it to the QnAMaker stack.

1. Create a new Azure search resource in the Azure portal, and choose the desired SKU.
![QnAMaker Azure search resource](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)
2. Restore the indexes from your original Azure search resource to the new one. See the backup restore sample code [here](https://github.com/pchoudhari/QnAMakerBackupRestore).
3. Once the data is restored, go to your new Azure search resource, click on **Keys**, and note down the **Name** and the **Admin key**.
![QnAMaker Azure search keys](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)
4. To link the new Azure search resource to the QnAMaker stack, go to the QnAMaker App service.
![QnAMaker appservice](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)
5. Click on **Application settings** and replace the **AzureSearchName** and **AzureSearchAdminKey** fields from step 3.
![QnAMaker appservice setting](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)
6. Restart the App service.
![QnAMaker appservice restart](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## Next steps

> [!div class="nextstepaction"]
> [Use QnAMaker API](../Quickstarts/csharp.md)
