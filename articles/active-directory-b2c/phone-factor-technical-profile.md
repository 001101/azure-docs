---
title: Define a phone factor technical profile in a custom policy
titleSuffix: Azure AD B2C
description: Define a phone factor technical profile in a custom policy in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg

ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/31/2020
ms.author: mimart
ms.subservice: B2C
---

# Define a phone factor technical profile in an Azure Active Directory B2C custom policy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) provides support for enrolling and verifying phone numbers. This technical profile:

- Provides a user interface to interact with the user.
- Uses content definition to control the look and feel.
- Supports both phone calls and text messages to validate the phone number.
- Supports multiple phone numbers. The user can select one of the phone numbers to verify.  
- If a phone number is provided, the phone factor user interface asks the user to verify the phone number. If not provided, it asks the user to enroll a new phone number.
- Returns a claim indicating whether the user provided a new phone number. You can use this claim to decide whether the phone number should  be persisted to the Azure AD user profile.  

## Protocol

The **Name** attribute of the **Protocol** element needs to be set to `Proprietary`. The **handler** attribute must contain the fully qualified name of the protocol handler assembly that is used by Azure AD B2C for phone factor:
`Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

The following example shows a phone factor technical profile for enrollment and validation:

```XML
<TechnicalProfile Id="PhoneFactor-InputOrVerify">
  <DisplayName>PhoneFactor</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.PhoneFactorProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## Input claims

The InputClaims element must contain following claims. You can also map the name of your claim to the name defined in the phone factor technical profile. 

|  Data Type| Required | Description |
| --------- | -------- | ----------- | 
| string| Yes | A unique identifier of the user. The claim name, or PartnerClaimType must be set to `UserId`.|
| string| Yes | List of claim types that store a phone number. If the input claims' value are empty, Azure AD B2C asks the user to type and verify a phone number.  Otherwise, Azure AD B2C presents the phone number, asking the user to verify it. In case of multiple input claims, the user is asked to choose and verify one of them.    |


The following example demonstrates using multiple phone numbers. For more information, see [sample policy](https://github.com/azure-ad-b2c/samples/tree/master/policies/mfa-add-secondarymfa).

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="userIdForMFA" PartnerClaimType="UserId" />
  <InputClaim ClaimTypeReferenceId="strongAuthenticationPhoneNumber" />
  <InputClaim ClaimTypeReferenceId="secondaryStrongAuthenticationPhoneNumber" />
</InputClaims>
```

The InputClaimsTransformations element may contain a collection of InputClaimsTransformation elements that are used to modify the input claims or generate new ones before presenting them to the phone factor page.

## Output claims

The OutputClaims element contains a list of claims returned by the phone factor technical profile.

|  Data Type| Required | Description |
|  -------- | ----------- |----------- |
| boolean | Yes | Indicates whether the new phone number has been entered by the user. The claim name, or PartnerClaimType must be set to `newPhoneNumberEntered`|
| string| Yes | The verified phone number. The claim name, or PartnerClaimType must be set to `Verified.OfficePhone`.|

The OutputClaimsTransformations element may contain a collection of OutputClaimsTransformation elements that are used to modify the output claims or generate new ones.

## Cryptographic keys

The **CryptographicKeys** element is not used.


## Metadata

| Attribute | Required | Description |
| --------- | -------- | ----------- |
| ContentDefinitionReferenceId | Yes | The identifier of the [content definition](contentdefinitions.md) associated with this technical profile. |
| ManualPhoneNumberEntryAllowed| No | Specify whether or not a user is allowed to manually enter a phone number. Possible values: `true` or `false` (default).|
| setting.authenticationMode | No | The method to validate the phone number. Possible values: `sms`, `phone` or `mixed` (default).|
| setting.autodial| No| Specify whether the technical profile should auto dial or auto send an SMS. Possible values: `true` or `false` (default). Auto dial requires the `setting.authenticationMode` metadata be set to `sms`, or `phone`. The input claims collection must have a single phone number. |

### UI elements

The phone factor authentication page user interface elements can be [localized](localization-string-ids.md#azure-mfa-error-messages).

## Next steps

- Check the [social and local accounts with MFA](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) starter pack.

