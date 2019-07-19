---
title: Delivery and Retry - Azure Event Grid IoT Edge | Microsoft Docs 
description: Delivery and Retry in Event Grid on IoT Edge.  
author: vkukke
manager: rajarv
ms.author: vkukke
ms.reviewer: 
ms.date: 07/18/2019
ms.topic: article
ms.service: event-grid-on-edge
services: event-grid-on-edge
---

## Overview
Event Grid provides durable delivery. It tries to deliver each message at least once for each matching subscription immediately. If an subscriber's endpoint doesn't acknowledge receipt of an event or in case of failure, Event Grid retries delivery based on a fixed **retry schedule** and **retry policy**.  Currently Event Grid module delivers an event at a time to the subscriber. The payload is however an array with a single event.

> [!IMPORTANT]
>There is no persistence support for event data. This means redeploying Event Grid module will cause you to lose any events that were not yet delivered. Event persistence is under development and will be supported in a future release.

### Retry Schedule
Event Grid waits up to 60 seconds for a response after delivering a message. If the subscriber's endpoint does not ACK the response, then the message will be enqueued in one of our back off queues for subsequent retries.

There are two pre-configured back off queues that determine the schedule on which a retry will be attempted. They are:-

| Schedule | Description |
| ---------| ------------ |
| 1 minute | Messages that end up here are attempted every minute.
| 10 minutes | Messages that end up here are attempted every 10th minute.

As soon as the message is published we try to deliver. If failure occurs, then the message is enqueued into **1 minute** queue. After a minute another attempt is made to deliver the event. If it fails then the message is enqueued into **10 minute** queue and retried after 10 minutes. 
The message will be attempted every 10 minutes until either successfully delivered or dropped due to retry policy limits. 

### Retry policy Limits
There are two configurations that determine retry policy. They are:-
1. Maximum number of attempts 
1. Event TTL

A event that cannot be delivered will be retried until one of these limits is reached in which case they will be end up being dropped. The retry schedule itself is described above. 

Configuration of these limits can be done either for all subscribers or per subscription basis. 
The below section describes each one is further detail.

#### Configuring defaults for all subscribers
There are two properties **broker:defaultMaxDeliveryAttempts** and **broker:defaultEventTimeToLiveInSeconds** that can be configured as part of Event Grid deployment which controls retry policy defaults for all subscribers.

| Property Name | Description |
| ---------------- | ------------ |
| **broker:defaultMaxDeliveryAttempts** | Maximum number of attempts to deliver an event. Default 30.
| **broker:defaultEventTimeToLiveInSeconds** | Event TTL in seconds after which an event will be dropped if not delivered. Default **7200** seconds

##### Example - **Configure a maximum of 3 attempts and TTL of 30 minutes before dropping the events from being delivered**

```json
 {
        "Env": [
            "broker:defaultMaxDeliveryAttempts=3",
            "broker:defaultEventTimeToLiveInSeconds=1800",
        ],
        "HostConfig": {
            "PortBindings": {
                "4438/tcp": [
                    {
                        "HostPort": "4438"
                    }
                 ]
             }
        }
 }
 ```

#### Configuring defaults per subscriber

You can also specify retry policy limits on a per subscription basis.
Please refer to our [API  documentation](api.md) on how to do this as part of subscription create or update.  Subscription level defaults override the module level configurations. 
