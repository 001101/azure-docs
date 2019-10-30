---
title: Azure Functions Diagnostics Overview
description: Learn how you can troubleshoot issues with your function app with Azure Functions Diagnostics. 
services: functions
keywords: functions, diagnostics, support, serverless, troubleshooting, self-help
author: yunjchoi
manager: jen7714

ms.service: azure-functions
ms.topic: article
ms.date: 11/01/2019
ms.author: yunjchoi
ms.custom: na
---
# Azure Functions Diagnostics overview

When you’re running a serverless application, you want to be prepared for any issues that may arise, from 500 errors to your users telling you that your site is down. Azure Functions diagnostics is an intelligent and interactive experience to help you troubleshoot your app with no configuration required. When you do run into issues with your app, Azure Functions diagnostics points out what’s wrong to guide you to the right information to more easily and quickly troubleshoot and resolve the issue.

Although this experience is most helpful when you’re having issues with your app within the last 24 hours, all the diagnostic graphs are always available for you to analyze.

## Open Azure Functions Diagnostics

To access Azure Functions Diagnostics:

- Navigate to your function app in the [Azure portal](https://portal.azure.com).
- Click **Platform features** tab at the top.
- Click on **Diagnose and solve problems** under **Resource Management**, which opens AKS Diagnostics.
- Choose a category that best describes the issue of your cluster by using the keywords in the homepage tile
OR type a keyword that best describes your issue in the search bar, for example _Function Execution and Errors_.

![Homepage](./media/functions-diagnostics/homepage.png)

## Interactive interface

Once you select a homepage category that best aligns with your function app's problem, Azure Functions diagnostics' interactive interface, Genie, can guide you through diagnosing and solving problem with your app. You can use the tile shortcuts provided by Genie to view the full diagnostic report of the problem category that you are interested. The tile shortcuts provide you a direct way of accessing your diagnostic metrics.

![Genie](./media/functions-diagnostics/genie.png)

After clicking on these tiles, you can see a list of topics related to the issue described in the tile. These topics provide snippets of notable information from the full report. You can click on any of these topics to investigate the issues further. Also, you can click on View Full Report to explore all the topics on a single page.

![Preview](./media/functions-diagnostics/preview.png)

## View a diagnostic report

After you click on a category, you can view a diagnostic report specific to serverless app. Diagnostic report intelligently calls out if there is any issue in your cluster with status icons. You can drill down on each topic by clicking on More Info to see detailed description of the issue, recommended actions, links to helpful docs, related-metrics, and logging data. Diagnostic reports are intelligently generated based on the current state of your cluster after running variety of checks. Diagnostic reports can be a useful tool for pinpointing the problem of your cluster and finding the next steps to resolve the issue.

## Pinpoint the line of code causing exceptions or errors

## Next steps

Post your questions or feedback at [UserVoice](https://feedback.azure.com/forums/355860-azure-functions) by adding "[Diag]" in the title.
