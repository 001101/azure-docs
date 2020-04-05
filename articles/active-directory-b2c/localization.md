---
title: Localization - Azure Active Directory B2C
description: Specify the Localization element of a custom policy in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg

ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 04/06/2020
ms.author: mimart
ms.subservice: B2C
---

# Localization

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

The **Localization** element allows you to support multiple locales or languages in the policy for the user journeys. The localization support in policies allows you to:

- Set up the explicit list of the supported languages in a policy and pick a default language.
- Provide language-specific strings and collections.

```XML
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
  <LocalizedResources Id="api.localaccountsignup.en">
  <LocalizedResources Id="api.localaccountsignup.es">
  ...
```

The **Localization** element contains the following attributes:

| Attribute | Required | Description |
| --------- | -------- | ----------- |
| Enabled | No | Possible values: `true` or `false`. |

The **Localization** element contains following XML elements

| Element | Occurrences | Description |
| ------- | ----------- | ----------- |
| SupportedLanguages | 1:n | List of supported languages. |
| LocalizedResources | 0:n | List of localized resources. |

## SupportedLanguages

The **SupportedLanguages** element contains the following attributes:

| Attribute | Required | Description |
| --------- | -------- | ----------- |
| DefaultLanguage | Yes | The language to be used as the default for localized resources. |
| MergeBehavior | No | An enumeration values of values that are merged together with any ClaimType present in a parent policy with the same identifier. Use this attribute when you overwrite a claim specified in base policy. Possible values: `Append`, `Prepend`, or `ReplaceAll`. The `Append` value specifies that the collection of data present should be appended to the end of the collection specified in the parent policy. The `Prepend` value specifies that the collection of data present should be added before the collection specified in the parent policy. The `ReplaceAll` value specifies that the collection of data defined in the parent policy should be ignored, using instead the data defined in the current policy. |

### SupportedLanguages

The **SupportedLanguages** element contains the following elements:

| Element | Occurrences | Description |
| ------- | ----------- | ----------- |
| SupportedLanguage | 1:n | Displays content that conforms to a language tag per RFC 5646 - Tags for Identifying Languages. |

## LocalizedResources

The **LocalizedResources** element contains the following attributes:

| Attribute | Required | Description |
| --------- | -------- | ----------- |
| Id | Yes | An identifier that is used to uniquely identify localized resources. |

The **LocalizedResources** element contains the following elements:

| Element | Occurrences | Description |
| ------- | ----------- | ----------- |
| LocalizedCollections | 0:n | Defines entire collections in various cultures. A collection can have different number of items and different strings for various cultures. Examples of collections include the enumerations that appear in claim types. For example, a country/region list is shown to the user in a drop-down list. |
| LocalizedStrings | 0:n | Defines all of the strings, except those strings that appear in collections, in various cultures. |

### LocalizedCollections

The **LocalizedCollections** element contains the following elements:

| Element | Occurrences | Description |
| ------- | ----------- | ----------- |
| LocalizedCollection | 1:n | List of supported languages. |

#### LocalizedCollection

The **LocalizedCollection** element contains the following attributes:

| Attribute | Required | Description |
| --------- | -------- | ----------- |
| ElementType | Yes | References a ClaimType element or a user interface element in the policy file. |
| ElementId | Yes | A string that contains a reference to a claim type already defined in the ClaimsSchema section that is used if **ElementType** is set to a ClaimType. |
| TargetCollection | Yes | The target collection. |

The **LocalizedCollection** element contains the following elements:

| Element | Occurrences | Description |
| ------- | ----------- | ----------- |
| Item | 0:n | Defines an available option for the user to select for a claim in the user interface, such as a value in a dropdown. |

The **Item** element contains the following attributes:

| Attribute | Required | Description |
| --------- | -------- | ----------- |
| Text | Yes | The user-friendly display string that should be shown to the user in the user interface for this option. |
| Value | Yes | The string claim value associated with selecting this option. |
| SelectByDefault | No | Indicates whether or not this option should be selected by default in the UI. Possible values: True or False. |

The following example shows the use of the **LocalizedCollections** element. It contains two **LocalizedCollection** elements, one for English and another one for Spanish. Both set the **Restriction** collection of the claim `Gender` with a list of items for English and Spanish.

```XML
<LocalizedResources Id="api.selfasserted.en">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Female" Value="F" />
      <Item Text="Male" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>

<LocalizedResources Id="api.selfasserted.es">
 <LocalizedCollections>
   <LocalizedCollection ElementType="ClaimType" ElementId="Gender" TargetCollection="Restriction">
      <Item Text="Femenino" Value="F" />
      <Item Text="Masculino" Value="M" />
    </LocalizedCollection>
</LocalizedCollections>
```

### LocalizedStrings

The **LocalizedStrings** element contains the following elements:

| Element | Occurrences | Description |
| ------- | ----------- | ----------- |
| LocalizedString | 1:n | A localized string. |

The **LocalizedString** element contains the following attributes:

| Attribute | Required | Description |
| --------- | -------- | ----------- |
| ElementType | Yes | Possible values: `ClaimsProvider`, `ClaimType`, `ErrorMessage`, `GetLocalizedStringsTransformationClaimType`, `Predicate`, `InputValidation`, or `UxElement`  .   | 
| ElementId | Yes | If **ElementType** is set to `ClaimType`, `Predicate`, or `InputValidation`, this element contains a reference to a claim type already defined in the ClaimsSchema section. |
| StringId | Yes | If **ElementType** is set to `ClaimType`, this element contains a reference to an attribute of a claim type. Possible values: `DisplayName`, `AdminHelpText`, or `PatternHelpText`. The `DisplayName` value is used to set the claim display name. The `AdminHelpText` value is used to set the help text name of the claim user. The `PatternHelpText` value is used to set the claim pattern help text. If **ElementType** is set to `UxElement`, this element contains a reference to an attribute of a user interface element. If **ElementType** is set to `ErrorMessage`, this element specifies the identifier of an error message. See [Localization string IDs](localization-string-ids.md) for a complete list of the `UxElement` identifiers.|

## ElementType

The ElementType reference to a claim type, a claim transformation, or a user interface element in the policy to be localized.

### ClaimsProvider

The ClaimsProvider value is used to localize claim provider name as specified in the StringId. 

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
  </ClaimsProviderSelections>
  <ClaimsExchanges>
    <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
  </ClaimsExchanges>
</OrchestrationStep>
```

The following example shows how to localize the claim provider name.

```xml
<LocalizedString ElementType="ClaimsProvider" StringId="FacebookExchange">Facebook</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="GoogleExchange">Google</LocalizedString>
<LocalizedString ElementType="ClaimsProvider" StringId="LinkedInExchange">LinkedIn</LocalizedString>
```

### ClaimType

The ClaimType value is used to localize one of the claim attributes, as specified in the StringId. 

```xml
<ClaimType Id="email">
  <DisplayName>Email Address</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>Email address that can be used to contact you.</UserHelpText>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
```

The following example  shows how to localize the DisplayName, UserHelpText, and PatternHelpText attributes of the email claim type.

```XML
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">Please enter your email</LocalizedString>
<LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">Please enter a valid email address</LocalizedString>
```

### ErrorMessage

The ErrorMessage value is used to localize one of the system error messages as specified in the StringId. 

```xml
<TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
  <Metadata>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
    <Item Key="UserMessageIfClaimsPrincipalAlreadyExists">You are already registered, please press the back button and sign in instead.</Item>
  </Metadata>
  ...
</TechnicalProfile>
```

The following example shows how to localize the UserMessageIfClaimsPrincipalAlreadyExists error message.


```XML
<LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">The account you are trying to create already exists, please sign-in.</LocalizedString>
```

### GetLocalizedStringsTransformationClaimType

The `GetLocalizedStringsTransformationClaimType` value is used to copy localized strings into claims. For more information, see [GetLocalizedStringsTransformation claims transformation](string-transformations.md#getlocalizedstringstransformation)


### Predicate

The `Predicate` value is used to localize one of the [Predicate](predicates.md) error messages, as specified in the StringId. 


```xml
<Predicates>
  <Predicate Id="LengthRange" Method="IsLengthRange"  HelpText="The password must be between 6 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">6</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>
  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

The following example shows a localized a predicate help text.

```xml
<LocalizedString ElementType="Predicate" ElementId="LengthRange" StringId="HelpText">The password must be between 6 and 64 characters.</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Lowercase" StringId="HelpText">a lowercase letter</LocalizedString>
<LocalizedString ElementType="Predicate" ElementId="Uppercase" StringId="HelpText">an uppercase letter</LocalizedString>
```

### InputValidation

The `InputValidation` value is used to localize one of the [PredicateValidation](predicates.md) group error messages as specified in the StringId. 

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences MatchAtLeast="1">
          <PredicateReference Id="LengthRange" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

The following example shows a localized a predicate validation group help text.

```XML
<LocalizedString ElementType="InputValidation" ElementId="StrongPassword" StringId="CharacterClasses">The password must have at least 3 of the following:</LocalizedString>
```

### UxElement

The UxElement value is used to localize one of the user interface elements as specified in the StringId. The following example shows how to localize the continue and cancel buttons.

```XML
<LocalizedString ElementType="UxElement" StringId="button_continue">Create new account</LocalizedString>
<LocalizedString ElementType="UxElement" StringId="button_cancel">Cancel</LocalizedString>
```

## Next steps

See the following articles for localization examples:

- [Language customization with custom policy in Azure Active Directory B2C](custom-policy-localization.md)
- [Language customization with user flows in Azure Active Directory B2C](user-flow-language-customization.md)
