---
title: Output batching - Azure Event Grid IoT Edge | Microsoft Docs 
description: Output batching in Event Grid on IoT Edge.
author: HiteshMadan
manager: rajarv
ms.author: himad
ms.reviewer: spelluru
ms.date: 10/06/2019
ms.topic: article
ms.service: event-grid
services: event-grid
---

# Output batching

Event Grid has support for delivering more than one event in a single delivery request. This feature makes it possible to increase the overall delivery throughput without paying the HTTP per-request overheads. Batching is turned off by default and can be turned on per-subscription.

> [!WARNING]
> The maximum allowed duration to process each delivery request does not change, even though the subscriber code potentially has to do more work per batched request. Delivery timeout defaults to 60 seconds.

## Batching policy

Event Grid's batching behavior can be customized per subscriber, by tweaking the following two settings:

* Maximum events per batch

  This setting sets an upper limit on the number of events that will be added to a batched delivery request.

* Approximate batch size in bytes

  This setting is used to control the maximum number of bytes that will be sent per delivery request

## Batching behavior

* All or none

  Event Grid operates with all-or-none semantics. It doesn't support partial success of a batch delivery. Subscribers should be careful to only ask for as many events per batch as they can reasonably handle in 60 seconds.

* Optimistic batching

  The batching policy settings aren't strict bounds on the batching behavior, and are respected on a best-effort basis. At low event rates, you'll often observe the batch size being less than the requested maximum events per batch.

* Default is set to OFF

  By default, Event Grid only adds one event to each delivery request. The way to turn on batching is to set either one of the settings mentioned earlier in the article in the event subscription JSON.

* Default values

  It isn't necessary to specify both the settings (Maximum events per batch and Approximate batch size in bytes) when creating an event subscription. Event Grid uses (configurable) default values if only one setting is set. See the following sections for the default values, and how to override them.

## Turning on output batching

```json
{
  "properties": {
    "destination": {
      "endpointType": "WebHook",
      "properties": {
        "endpointUrl": "<your_webhook_url>"
      }
    },
    "deliveryPolicy": {
      "maxEventsPerBatch": 10,
      "approxBatchSizeInBytes": 65536
    }
  }
}
```

## Configuring maximum allowed values

The following deployment time settings control the maximum value allowed when creating an event subscription.

| Property Name | Description |
| -- | -- |
| `api:deliveryPolicyLimits:maxBatchSizeInBytes` | Maximum value allowed for the `ApproxBatchSizeInBytes` setting. Default value: `1_058_576`.
| `api:deliveryPolicyLimits:maxEventsPerBatch` | Maximum value allowed for the `MaxEventsPerBatch` setting. Default value: `50`.

## Configuring default values

The following deployment time settings control the default value of each setting when it's not specified in the event subscription. To reiterate - at least one setting must be set on the event subscription to turn on batching behavior.

| Property Name | Description |
| -- | -- |
| `broker:defaultMaxBatchSizeInBytes` | Maximum delivery request size when only `MaxEventsPerBatch` is specified. Default `1_058_576`.
| `broker:defaultMaxEventsPerBatch` | Maximum number of events to add to a batch when only `MaxBatchSizeInBytes` is specified. Default `10`.
