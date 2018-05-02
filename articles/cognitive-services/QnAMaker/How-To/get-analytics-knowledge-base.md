---
title: How to get analytics on your knowledge base - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: How to get analytics on your knowledge base 
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
---

# Get analytics on your knowledge base

QnAMaker stores all chat logs and other telemetry, if you have enabled App Insights during the [creation of your QnAMaker service](./set-up-qnamaker-service-azure.md). Run the sample queries to get your chat logs from App Insights.

1. Go to your App Insights resource.

![Click on your application insights resource](../media/qnamaker-how-to-analytics-kb/resources-created.png)

2. Click on **Analytics**. A new window opens where you can query QnAMaker telemetry.

![Click on Analytics](../media/qnamaker-how-to-analytics-kb/analytics.png)

3. Paste in the following query and run it.

![Run query](../media/qnamaker-how-to-analytics-kb/run-query.png)
```query
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse name with *"/knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId
    ) on id
    | extend question = tostring(customDimensions['Question'])
    | extend answer = tostring(customDimensions['Answer'])
    | project KbId, timestamp, resultCode, duration, question, answer
```

## Run queries for other analytics on your QnAMaker knowledge base

### Total 90-day traffic

```query
    //Total Traffic
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by bin(timestamp, 1d), KbId
``` 

### Total question traffic in a given time period

```query
    //Total Question Traffic in a given time period
    let startDate = todatetime('2018-02-18');
    let endDate = todatetime('2018-03-12');
    requests
    | where timestamp <= endDate and timestamp >=startDate
    | where url endswith "generateAnswer" and name startswith "POST" 
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | summarize ChatCount=count() by KbId
```

### User traffic

```query
    //User Traffic
    requests
    | where url endswith "generateAnswer"
    | project timestamp, id, name, resultCode, duration
    | parse name with *"/knowledgebases/"KbId"/generateAnswer"
    | join kind= inner (
    traces | extend id = operation_ParentId 
    ) on id
    | extend UserId = tostring(customDimensions['UserId'])
    | summarize ChatCount=count() by bin(timestamp, 1d), UserId, KbId
```

### Latency distribution of questions

```query
    //Latency distribution of questions
    requests
    | where url endswith "generateAnswer" and name startswith "POST"
    | parse name with *"/knowledgebases/"KbId"/generateAnswer" 
    | project timestamp, id, name, resultCode, performanceBucket, KbId
    | summarize count() by performanceBucket, KbId
```

## Next steps

> [!div class="nextstepaction"]
> [Manage keys](./key-management.md)
