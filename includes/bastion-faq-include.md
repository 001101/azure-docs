---
 title: include file
 description: include file
 services: bastion
 author: cherylmc
 ms.service: bastion
 ms.topic: include
 ms.date: 05/28/2019
 ms.author: cherylmc
 ms.custom: include file
---

## <a name="preview"></a>How do I participate in the public preview?

To participate in the public preview, go to the [Azure portal - preview link](https://aka.ms/BastionHost) and create a new Azure Bastion resource deployment. The public preview is limited to only specific Azure public regions.

## <a name="publicip"></a>Do I need a public IP on my virtual machine?

You do NOT need a public IP on the Azure Virtual Machine that you are connecting to with the Azure Bastion service. The Bastion service will open the RDP/SSH session/connection to your virtual machine over the private IP of your virtual machine, within your virtual network.

## <a name="rdpssh"></a>Do I need an RDP or SSH client?

You do not need an RDP or SSH client to access the RDP/SSH to your Azure virtual machine in your Azure portal. Use the [Azure portal - preview link](https://aka.ms/BastionHost) to access the preview flight of the portal. This will let you get RDP/SSH access to your virtual machine directly in the browser.

## <a name="agent"></a>Do I need an agent running in the Azure virtual machine?

You don't need to install an agent or any software on your browser or on your Azure virtual machine. The Bastion service is agentless and does not require any additional software for RDP/SSH.

## <a name="browsers"></a>Which browsers are supported?

During the public preview, use the Microsoft Edge browser or Google Chrome on Windows. For Apple Mac, use Google Chrome browser. Microsoft Edge Chromium is also supported on both Windows and Mac, respectively.

## <a name="regions"></a>What regions are available during preview?

You can deploy and use the Bastion resource in any of these preview regions via the [Azure portal - preview link](https://aka.ms/BastionHost).

[!INCLUDE [region](bastion-regions-include.md)]

## <a name="portal"></a>I can't find the Bastion resource in the Azure portal. What should I do?

Make sure that you are using the [Azure portal - preview link](https://aka.ms/BastionHost), not the regular Azure portal.

## <a name="previewbill"></a>Do I get billed for participating in the preview?

You will only be billed partially during public preview. However, there is no SLA attached with your deployment. For more information, see the [pricing page](https://aka.ms/BastionHostPricing).