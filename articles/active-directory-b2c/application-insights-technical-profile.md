---
title: Define an Application Insights technical profile in a custom policy
titleSuffix: Azure AD B2C
description: Define an Application Insights technical profile in a custom policy in Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg

ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/18/2020
ms.author: mimart
ms.subservice: B2C
---


# Define an Application Insights technical profile in an Azure AD B2C custom policy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) provides support sending event data directly to [Application Insights](../azure-monitor/app/app-insights-overview.md) by using the instrumentation key provided to Azure AD B2C.  With Application Insights technical profile, can get detailed and customized event logs for your user journeys, such as:

* Gain insights on user behavior.
* Troubleshoot your own policies in development or in production.
* Measure performance.
* Create notifications from Application Insights.


## Protocol

The **Name** attribute of the **Protocol** element needs to be set to `Proprietary`. The **handler** attribute must contain the fully qualified name of the protocol handler assembly that is used by Azure AD B2C, for application insights:
`Web.TPEngine.Providers.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

The following example shows the common application insights technical profile. Other application insights technical profiles include the AzureInsights-Common to leverage its configuration. 

```xml
<TechnicalProfile Id="AzureInsights-Common">
  <DisplayName>Azure Insights Common</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
</TechnicalProfile>
```

## Input claims

The **InputClaims** element contains a list of claims to send to the Application Insights. You can also map the name of your claim to the a name you prefer to appear in Application Insights. Following example shows the how to send telemetries to Application Insights. Properties of an event are added through the syntax `{property:NAME}`, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported [claim resolvers](claim-resolver-overview.md).

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
  <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:JourneyId}" DefaultValue="{Context:CorrelationId}" />
  <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
  <InputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="{property:objectId}"  />
</InputClaims>
```

The **InputClaimsTransformations** element may contain a collection of **InputClaimsTransformation** elements that are used to modify the input claims or generate new ones before sending to the Application Insights.

## Persist claims

If the PersistedClaims element is absent.

## Output claims

The **OutputClaims**, and **OutputClaimsTransformations** elements are absent.

## Cryptographic keys

The CryptographicKeys element is not used.


## Metadata

| Attribute | Required | Description |
| --------- | -------- | ----------- |
| InstrumentationKey| Yes | The Application Insights [instrumentation key](../azure-monitor/app/create-new-resource.md#copy-the-instrumentation-key), which will be used for logging the events. | 
| DeveloperMode| No | A Boolean that indicates whether developer mode is enabled. Possible values: `true`, or `false` (default). This controls how events are buffered. In a development environment with minimal event volume, enabling developer mode results in events being sent immediately to Application Insights.| 
|DisableTelemetry |No |A Boolean that indicates whether telemetry should be enabled or not. Possible values: `true`, or `false` (default).| 


## Next steps

- [Create an Application Insights resource](../azure-monitor/app/create-new-resource.md)
- Learn how to [track user behavior in Azure Active Directory B2C using Application Insights](analytics-with-application-insights.md)
