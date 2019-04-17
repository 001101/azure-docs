---
title: Troubleshoot the web application firewall for Azure Application Gateway
description: This article provides troubleshooting information for web application firewall (WAF) for Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 4/17/2019
ms.author: ant
ms.topic: conceptual
---

# Troubleshoot web application firewall for Azure Application Gateway

There are a few things you can do if requests that should pass through your web application firewall (WAF) are blocked.

First, ensure you’ve looked through the [WAF overview](waf-overview.md) and the [WAF configuration](application-gateway-waf-configuration.md) documents. Also, make sure you’ve enabled [WAF monitoring](application-gateway-diagnostics.md) These articles explain how the WAF functions, how the WAF rule sets work, and how to access WAF logs.

## Understanding WAF logs

When you have WAF logs available, you can do a few things with them.

For example, say you have a legitimate traffic containing the string “1=1” that you want to pass through your WAF. If you try the request, the WAF blocks traffic that contains your “1=1” string in any parameter or field. You can look through the logs and see the timestamp of the request and the rules that blocked/matched. 

In the following example, you can see that four rules are triggered during the same request (using the TransactionId field). The first one says it matched because the user used a numeric/IP URL for the request, which increases the anomaly score. The next rule that matched is 942130, which is the one you’re looking for. You can see the **1=1** in the `details.data` field. This further increases the anomaly score. Generally, every rule that has the action **Matched** increases the anomaly score. For more information, see [Anomaly scoring mode](waf-overview.md#anomaly-scoring-mode).

The final two log entries show the request was blocked because the anomaly score was high enough. These entries have a different action than the other two. They show they actually *blocked* the request. These rules are mandatory and can’t be disabled. They shouldn’t be thought of as rules, but more as core infrastructure of [Drew, please complete this sentence]

[Screen shot of log]

## Fixing false positives

With this information, and the knowledge that rule 942130 is the one that matched the *1=1* string, you can do a few things to stop this from blocking your traffic:

- Use an Exclusion List

   See [WAF configuration](application-gateway-waf-configuration.md#waf-exclusion-lists) for more information about exclusion lists.
- Disable the rule.

### Using an exclusion list

To make an informed decision about handling a false positive, it’s important to familiarize yourself with the technologies your application uses. For example, say there isn't a SQL server in your technology stack, and you are getting false positives related to those rules. Disabling those rules doesn't necessarily weaken your security.

One benefit of using an exclusion list is that only a specific part of a request is being disabled. However, this means that a specific exclusion is applicable to all traffic passing through your WAF because it is a global setting. For example, this could lead to an issue if *1=1* is a valid request in the body for a certain app, but not for others. Another benefit is that you can choose between body, headers, and cookies to be excluded if a certain condition is met, as opposed to excluding the whole request.

In this example, you’ll want to exclude the *request attribute name* that equals **text1**. This is apparent because you can see the attribute name in the firewall logs: **data: Matched Data: 1=1 found within ARGS:text1: 1=1**. The *attribute* is **text1**. You can also find this attribute name a few other ways, see [Finding request attribute names](#finding-request-attribute-names).

[screenshot of exclusion list]

### Disabling rules

Another way to get around a false positive is to disable the rule that matched on the input the WAF thought was malicious. Since you've parsed the WAF logs and have narrowed the rule down to 942130, you can disable it in the Azure portal. See [Customize web application firewall rules through the Azure portal](application-gateway-customize-waf-rules-portal.md).

One benefit of disabling a rule is that if you know all traffic that contains a certain condition that will normally be blocked is valid traffic, you can disable that rule for the entire WAF. However, if it’s only valid traffic in a specific use case, you open up a vulnerability by disabling that rule for the entire WAF since it is a global setting.

If you want to use Azure PowerShell, see [Customize web application firewall rules through PowerShell](application-gateway-customize-waf-rules-powershell.md). If you want to use Azure CLI, see [Customize web application firewall rules through the Azure CLI](application-gateway-customize-waf-rules-cli.md).

[screenshot of WAF rules]

## Finding request attribute names

With the help of [Fiddler](https://www.telerik.com/fiddler), you inspect individual requests and determine what specific fields of a web page are called. This can help to exclude certain fields from inspection using Exclusion Lists.

In this example, you can see that the field where the *1=1* string was entered is called **text1**.

[Fiddler screenshot]

This is a field you can exclude. To learn more about exclusion lists, See the How-to guide here. You can exclude the evaluation in this case by configuring the following exclusion:

[screenshot of exclusion]

You can also examine the firewall logs to get the information to see what you need to add to the exclusion list. To enable logging, see [Back-end health, diagnostic logs, and metrics for Application Gateway](application-gateway-diagnostics.md).

Examine the firewall log and view the PT1H.json file for the hour that the request you want to inspect occurred.

In this example, you can see that you have four rules with the same TransactionID, and that they all occurred at the exact same time:

[log screenshot]

With your knowledge of how the CRS rule sets work, and that the CRS ruleset 3.0 works with an anomaly scoring system (see more here https://docs.microsoft.com/en-us/azure/application-gateway/waf-overview) you know that the bottom two rules with the **action: Blocked** property are blocking based on the total anomaly score. The rules to focus on are the top two.

The first entry is logged because the user used a numeric IP address to navigate to the Application Gateway, which can be ignored in this case.

The second one (rule 942130) is the interesting one. You can see in the details that it matched a pattern (1=1), and the field is named **text1**. Follow the same previous steps to exclude the **Request Attribute Name** that **equals** **1=1**.

Another way to find the Attribute name is to right-click and inspect the element in a browser. There should be a **name** field for any input area, and that is what qualifies as a *Request Attribute Name*.

## Restrict global parameters to eliminate false positives

- Disable request body inspection

   By setting **Inspect request body** to off, the request bodies of all traffic will not be evaluated by your WAF. This may be useful if you know that the request bodies aren’t malicious to your application.

   By disabling this option, only the request body is not inspected. The headers and cookies remain inspected, unless individual ones are excluded using the exclusion list functionality.

- File size limits

   By limiting the file size for your WAF, you’re limiting the possibility of an attack happening to your web servers. By allowing large files to be uploaded, the risk of your backend being overwhelmed increases. Limiting the file size to a normal use case for your application is just another way to prevent attacks.

   > [!NOTE]
   > If you know that your app will never need any file upload above a given size, you can restrict that by setting a limit.

## Next steps

See [How to configure web application firewall on Application Gateway](tutorial-restrict-web-traffic-powershell.md).
