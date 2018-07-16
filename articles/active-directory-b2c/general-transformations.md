---
title: General claims transformation examples for the Identity Experience Framework Schema of Azure Active Directory B2C  | Microsoft Docs
description: General claims transformation examples for the Identity Experience Framework Schema of Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman

ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/16/2018
ms.author: davidmu
ms.component: B2C
---

# General Claims Transformations

This article provides examples for using general claims transformations of the Identity Framework Experience schema in Azure Active Directory (Azure AD) B2C. For more information, see [ClaimsTransformations](claims-transformations.md).

## DoesClaimExist

Checks if the inputClaim exists or not and sets outputClaim to true or false accordingly.

| Item | TransformationClaimType | Data Type | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | firstDateTime | | The input claim whose existence needs to be verified. |
| OutputClaim | outputClaim | boolean | The ClaimType that is produced after this ClaimsTransformation has been invoked. |

Sometimes you need to check if a ClaimType exists or contains any value. Use this claims transformation to check if your ClaimType exist. The return value is a boolean that indicates whether the claim exists. Following example checks if the email address exists.

```XML
<ClaimsTransformation Id="CheckIfEmailPresent" TransformationMethod="DoesClaimExist">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="inputClaim" />
  </InputClaims>					
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="isEmailPresent" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### Example

* Input claims:
    * **inputClaim**: someone@contoso.com
* Output claims: 
    * **outputClaim**: true

## Hash

Hash the provided plain text using the salt and a secret.

| Item | TransformationClaimType | Data Type | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | plaintext | string | The input claim to be encrypted |
| InputClaim | salt | string | The salt parameter. You can create a random value, using `CreateRandomString` claims transformation. |
| InputParameter | randomizerSecret | string | Points to an existing Azure AD B2C **Policy Keys**. To create a new one: In your Azure AD B2C tenant, select **B2C Settings > Identity Experience Framework**. Select **Policy Keys** to view the keys that are available in your tenant. Select **Add**. For **Options**, select **Manual**. Provide a name (the prefix B2C_1A_ might be added automatically.). In the Secret box, enter any secret you want to use, such as 1234567890. For Key usage, select **Secret**. Select **Create**. |
| OutputClaim | outputClaim | boolean | The ClaimType that is produced after this ClaimsTransformation has been invoked. The claim configured in the `plaintext` inputClaim. |

```XML
<ClaimsTransformation Id="HashPasswordWithEmail" TransformationMethod="Hash">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="password" TransformationClaimType="plaintext" />
    <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="salt" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="randomizerSecret" DataType="string" Value="B2C_1A_AccountTransformSecret" />
  </InputParameters>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="hashedPassword" TransformationClaimType="hash" />
  </OutputClaims>
</ClaimsTransformation>
```

### Example

* Input claims:
    * **plaintext**: MyPass@word1
    * **salt**: 487624568
    * **randomizerSecret**: B2C_1A_AccountTransformSecret
* Output claims: 
    * **outputClaim**: CdMNb/KTEfsWzh9MR1kQGRZCKjuxGMWhA5YQNihzV6U=

## NullClaim

Clean the value of a given claim type

| Item | TransformationClaimType | Data Type | Notes |
| ---- | ----------------------- | --------- | ----- |
| OutputClaim | alternativeSecurityId | string | The claim its value to be NULL. |

Use this claim transformation to remove unnecessary data from the claims property bag. So, the session cookie will be smaller. The following example removes the value of the `dispalyName` claim type.

```XML
<ClaimsTransformation Id="SetTOSToNull" TransformationMethod="NullClaim">
  <OutputClaims>
  <OutputClaim ClaimTypeReferenceId="TermsOfService" TransformationClaimType="claim_to_null" />
  </OutputClaims>
</ClaimsTransformation>
```
* Input claims:
    * **outputClaim**: Welcome to Contoso App. If you continue to browse and use this website, you are agreeing to comply with and be bound by the following terms and conditions....
* Output claims:
    **outputClaim**: NULL

