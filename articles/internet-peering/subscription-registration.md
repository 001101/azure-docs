---
title: Associate Peer ASN to Azure Subscription using PowerShell
description: Associate Peer ASN to Azure Subscription using PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
---

# Associate Peer ASN to Azure Subscription using PowerShell

Before you submit a peering request, you should first associate your ASN with Azure subscription using the steps below.

If you prefer, you can complete this guide using the [Azure portal](subscription-registration-portal.md).

### Working with Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## Create PeerASN to associate your ASN with Azure Subscription

### Sign in to your Azure account and select your subscription
[!INCLUDE [Account](./includes/account.md)]

### Register for Peering feature flag
Register Peering feature flag for your subscription using the command below. This is required because Peering setup using Azure PowerShell is currently under Preview.

* For Direct Peering use below.

```powershell
Register-AzProviderFeature -FeatureName AllowDirectPeering -ProviderNamespace Microsoft.Peering
```

* For Exchange Peering use below.

```powershell
Register-AzProviderFeature -FeatureName AllowExchangePeering -ProviderNamespace Microsoft.Peering
```

You can check the registration status using the commands below:

* For Direct Peering use below.

```powershell
Get-AzProviderFeature -FeatureName AllowDirectPeering -ProviderNamespace Microsoft.Peering
```

Below is an example response:
```powershell
FeatureName          ProviderName      RegistrationState
-----------          ------------      -----------------
AllowDirectPeering   Microsoft.Peering Registered
```

* For Exchange Peering use below.

```powershell
Get-AzProviderFeature -FeatureName AllowExchangePeering -ProviderNamespace Microsoft.Peering
```

Below is an example response:
```powershell
FeatureName          ProviderName      RegistrationState
-----------          ------------      -----------------
AllowExchangePeering Microsoft.Peering Registered
```

> [!IMPORTANT]
> Please wait for *RegistrationState* to turn "Registered" before proceeding. It may take 5 to 30 minutes after you execute the command.

### Register for Peering resource provider
Register Peering resource provider for your subscription using the command below. This is required because Peering setup using Azure PowerShell is currently under preview.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

You can check the registration status using the commands below:
```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.Peering
```

> [!IMPORTANT]
> Please wait for *RegistrationState* to turn "Registered" before proceeding. It may take 5 to 30 minutes after you execute the command.

### Update the peer information associated with this subscription

```powershell
New-AzPeerAsn `
    -Name "Contoso_1234" `
    -PeerName "Contoso" `
    -PeerAsn 1234 `
    -Email noc@contoso.com, support@contoso.com `
    -Phone "+1 (555) 555-5555"
```

> [!NOTE]
> -Name corresponds to resource name and can be anything you choose. However, -peerName corresponds to your company's name and needs to be as close as possible to your PeeringDb profile. Note that value for -peerName supports only characters a-z, A-Z, and space.

A subscription can have multiple ASNs. Update the peering information for each ASN. Ensure that "name" is unique for each ASN.

Peers are expected to have a complete and up-to-date profile on [PeeringDB](https://www.peeringdb.com). We use this information during registration to validate the peer's details such as NOC information, technical contact information, and their presence at the peering facilities etc.

Note that in place of {subscriptionId} in the output above, actual subscription Id will be displayed.

## View status of a PeerASN

Check for ASN Validation state using the command below:

```powershell
Get-AzPeerAsn
```

Below is an example response:
```powershell
PeerContactInfo : Microsoft.Azure.PowerShell.Cmdlets.Peering.Models.PSContactInfo
PeerName        : Contoso
ValidationState : Approved
PeerAsnProperty : 1234
Name            : Contoso_1234
Id              : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/Contoso_1234
Type            : Microsoft.Peering/peerAsns
```

> [!IMPORTANT]
> Please wait for the ValidationState to turn "Approved" before submitting a Peering request. It may take up to 12 hours for this approval.

## Modify PeerAsn
You may modify NOC contact information anytime.

Below is an example:

```powershell
Set-PeerAsn -Name Contoso_1234 -Email "newemail@test.com" -Phone "1800-000-0000"
```

## Delete PeerAsn
Deleting a PeerASN is not currently supported. If you need to delete PeerASN, please contact [Microsoft Peering](mailto:peering@microsoft.com).

## Next steps

* [Create or modify a Direct Peering](howto-directpeering-arm.md)
* [Convert a legacy Direct Peering to Azure resource](howto-legacydirect-arm.md)
* [Create or modify Exchange Peering](howto-exchangepeering-arm.md)
* [Convert a legacy Exchange Peering to Azure resource](howto-legacyexchange-arm.md)

## Additional resources

For more information, please visit [Peering FAQs](faqs.md)

[!INCLUDE [peering-feedback](./includes/feedback.md)]

