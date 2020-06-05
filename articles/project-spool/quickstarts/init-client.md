---
title: Capture A Local Video Stream
description: TODO
author: mikben    
manager: jken
services: azure-project-spool

ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-project-spool

---

> [!WARNING]
> This document is under construction.


## Instantiate Your Calling Client

In this quickstart, you'll learn how to instantiate your Azure Communication Services calling client.


### Prerequisites

- An active Azure Communication Services resource. [This quickstart](./get-started.md) shows you how to create and manage your first resource.
- A camera-equipped device with the latest version of Chrome or Edge installed.
- The ACS client-side JS SDK.


### Instantiate Your Calling Client

```javascript
    
    const tokenCredential = new UserAccessTokenCredential(token);
    const callClient = await CallingFactory.create(tokenCredential);
    
```

### Next Steps

- Capture your local media stream
- Place a call
- Send an SMS message

