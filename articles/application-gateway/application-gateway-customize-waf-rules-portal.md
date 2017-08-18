---
title: Customize web application firewall rules in Azure Application Gateway--Azure portal | Microsoft Docs
description: This page provides information on how to customize web application firewall rules in Application Gateway with the Aure portal.
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn

ms.assetid: 1159500b-17ba-41e7-88d6-b96986795084
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom:
ms.workload: infrastructure-services
ms.date: 03/28/2017
ms.author: gwallace

---

# Customize web application firewall rules through the Azure portal

> [!div class="op_single_selector"]
> * [**Azure portal**](application-gateway-customize-waf-rules-portal.md)
> * [**PowerShell**](application-gateway-customize-waf-rules-powershell.md)
> * [**Azure CLI 2.0**](application-gateway-customize-waf-rules-cli.md)

Azure Application Gateway web application firewall (WAF) provides protection for web applications. These protections are provided by the Open Web Application Security Project (OWASP) Core Rule Set (CRS) set of generic attack detection rules. Some rules can cause false positives and block real traffic. For this reason, Application Gateway provides the capability to customize rule groups and rules on a web application firewall-enabled Application Gateway. For more information on the specific rule groups and rules, see [List of web application firewall CRS rule groups and rules](application-gateway-crs-rulegroups-rules.md).

>[!NOTE]
> If your application gateway is not using the WAF tier, you are given the option to upgrade the application gateway to the WAF tier as shown in the following image:

![Enable WAF][fig1]

## View rule groups and rules

**To view rule groups and rules**
   1. Navigate to an application gateway, and then select **Web application firewall**.  
   2. Select **Advanced rule configuration**.  
   This view shows a table on the page of all the rule groups provided with the chosen rule set. All of the rules are checked.

![Configure disabled rules][1]

## Search for rules to disable

The **Web application firewall settings** blade provides the capability to filter the rules through a text search. The result displays only the rule groups and rules that contain the searched for text.

![Search for rules][2]

## Disable rule groups and rules

When disabling rules, you can disable an entire rule group or specific rules under one or more rule groups. 

**To disable rule groups or specific rules**

   1. Search for the rules or rule groups that you want to disable.
   2. Uncheck the rules that you want to disable. 
   2. Select **Save**. This step saves the changes to the application gateway.

![Save changes][3]

## Next steps

After you configure your disabled rules, we recommend that you learn how to view your WAF logs. For more information, see [Application Gateway diagnostics](application-gateway-diagnostics.md#diagnostic-logging)

[fig1]: ./media/application-gateway-customize-waf-rules-portal/1.png
[1]: ./media/application-gateway-customize-waf-rules-portal/figure1.png
[2]: ./media/application-gateway-customize-waf-rules-portal/figure2.png
[3]: ./media/application-gateway-customize-waf-rules-portal/figure3.png
