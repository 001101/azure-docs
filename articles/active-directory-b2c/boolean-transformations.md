---
title: Boolean claims transformation examples for the Identity Experience Framework Schema of Azure Active Directory B2C  | Microsoft Docs
description: Boolean claims transformation examples for the Identity Experience Framework Schema of Azure Active Directory B2C.
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

# Boolean claims transformations

This article provides examples for using the boolean claims transformations of the Identity Framework Experience schema in Azure Active Directory (Azure AD) B2C. For more information, see [ClaimsTransformations](claims-transformations.md).

## AndClaims

Performs an `And` of two boolean inputClaims and sets the outputClaim with result of the operation.

| Item  | TransformationClaimType  | Data Type  | Notes |
|-------| ------------------------ | ---------- | ----- |
| InputClaim | inputClaim1 | boolean | The first ClaimType to evaluate. |
| InputClaim | inputClaim2  | boolean | The second ClaimType to evaluate. |
|OutputClaim | outputClaim | boolean | The ClaimTypes that will be produced after this ClaimsTransformation has been invoked (true or false). |

In an orchestration step, you need to check two ClaimTypes. Preset an email self asserted only if the account is a social account and the email is empty. The following claims transformation demonstrates how to `And` to boolean ClaimTypes, `isEmailNotExist`, and `isSocialAccount`. 

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isEmailNotExist" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isSocialAccoutn" TransformationClaimType="inputClaim2" />
  </InputClaims>					
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentEmailSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### Example

* Input claims:
    * **inputClaim1**: true
    * **inputClaim2**: false
* Output claims:
    **outputClaim**: false


## AssertBooleanClaimIsEqualToValue

Checks that string values of two claims are equal, and throws an exception if they are not.

| Item | TransformationClaimType  | Data Type  | Notes |
| ---- | ------------------------ | ---------- | ----- |
| inputClaim | inputClaim | boolean | The ClaimType to be asserted. |
| InputParameter |valueToCompareTo | boolean | The value to compare (true or false). |


The following claims transformation demonstrates how to check the value of a boolean ClaimType with `true` value. If the value of the `accountEnabled` ClaimType is false, an error message is thrown.

```XML
<ClaimsTransformation Id="AssertAccountEnabledIsTrue" TransformationMethod="AssertBooleanClaimIsEqualToValue">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="accountEnabled" TransformationClaimType="inputClaim" />
  </InputClaims>
  <InputParameters>
    <InputParameter Id="valueToCompareTo" DataType="boolean" Value="true" />
  </InputParameters>
</ClaimsTransformation>
```

### Example

* Input claims:
    * **inputClaim**: false
    * **valueToCompareTo**: true
* Result: Error thrown

## NotClaims

Performs a `Not` of the boolean inputClaim and sets the outputClaim with result of the operation.

| Item | TransformationClaimType | Data Type | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim | boolean | The claim to be operated. |
| OutputClaim | outputClaim | boolean | The ClaimTypes that are produced after this ClaimsTransformation has been invoked (true or false). |

Use this claim transformation to perform logical negation on a claim.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="AndClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="inputClaim" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="userExists" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
```

### Example

* Input claims:
    * **inputClaim**: false
* Output claims:
    * *outputClaim**: true

## OrClaims 

Computes an `Or` of two boolean inputClaims and sets the outputClaim with result of the operation.

| Item | TransformationClaimType | Data Type | Notes |
| ---- | ----------------------- | --------- | ----- |
| InputClaim | inputClaim1 | boolean | The first ClaimType to evaluate. |
| InputClaim | inputClaim2 | boolean | The second ClaimType to evaluate. |
| OutputClaim | outputClaim | boolean | The ClaimTypes that will be produced after this ClaimsTransformation has been invoked (true or false). |

Following claims transformation demonstrates how to `Or` two boolean ClaimTypes, `isEmailNotExist` and `isSocialAccount`. In the orchestration step, you can use precondition to preset the email self asserted only if a social account email is empty.

```XML
<ClaimsTransformation Id="CheckWhetherEmailBePresented" TransformationMethod="OrClaims">
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedExists" TransformationClaimType="inputClaim1" />
    <InputClaim ClaimTypeReferenceId="isLastTOSAcceptedGreaterThanNow" TransformationClaimType="inputClaim2" />
  </InputClaims>					
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="presentTOSSelfAsserted" TransformationClaimType="outputClaim" />
  </OutputClaims>
</ClaimsTransformation>
</ClaimsTransformation>
```

### Example

* Input claims:
    * **inputClaim1**: true
    * **inputClaim2**: false
* Output claims:
    * **outputClaim**: true

