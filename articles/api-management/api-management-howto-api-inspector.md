---
title: Debug your APIs using request tracing in Azure API Management | Microsoft Docs
description: Follow the steps of this tutorial to learn how to inspect request processing steps in Azure API Management.
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''

ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2017
ms.author: apimpm

---

# Debug your APIs using request tracing

This tutorial describes how to inspect request processing to help you with debugging and troubleshooting your API. 

![API inspector](media/api-management-howto-api-inspector/api-inspector001.PNG)

In this tutorial, you learn how to:

> [!div class="checklist"]
> * How to trace a call

## Prerequisites

+ [Create an Azure API Management instance](get-started-create-service-instance.md)
+ [Import and publish an API](import-and-publish.md)
+ [Transform the API using policies](transform-api.md)

## Use API Inspector to trace a call

1. Sign in to the Azure portal at [https://portal.azure.com](https://portal.azure.com).
2. Browse to your APIM instance.
3. Select **APIs**.
4. Click **Demo Conference API** from your API list.
5. Select **GetSpeakers** operation.
6. Switch to the **Test** tab.
7. Make sure to include an HTTP header named **Ocp-Apim-Trace** with the value set to **true**.
8. Click **"Send"** to make an API call. 
9. Wait for the call to complete. 
10. Go to the **Trace** tab in the **API console**. You can click any of the following links to jump to detailed trace info: **inbound**, **backend**, **outbound**.

    In the **inbound** section, you see the original request API Management received from the caller and all the policies applied to the request including the rate-limit and set-header policies we added in step 2.

    In the **backend** section, you see the requests API Management sent to the API backend and the response it received.
    
    In the **outbound** section, you see all the policies applied to the response before sending back to the caller.
 
    > [!TIP]
    > Each step also shows the elapsed time since the request is received by API Management.

## Next steps

> [!div class="nextstepaction"]
> [Use revisions](api-management-get-started-revise-api.md)]


