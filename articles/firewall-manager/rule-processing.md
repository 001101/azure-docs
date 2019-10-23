---
title: Azure Firewall rule processing logic
description: Learn about Azure Firewall rule processing logic
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: concept
ms.date: 10/24/2019
ms.author: victorh
---

# Azure Firewall rule processing logic

Azure Firewall has NAT rules, network rules, and applications rules. The rules are processed according to the rule type.

## Network rules and applications rules

Network rules are applied first, then application rules. The rules are terminating. So if a match is found in network rules, then application rules are not processed.  If there is no network rule match, and if the packet protocol is HTTP/HTTPS, the packet is then evaluated by the application rules. If still no match is found, then the packet is evaluated against the infrastructure rule collection. If there is still no match, then the packet is denied by default.

## NAT rules

Inbound connectivity can be enabled by configuring Destination Network Address Translation (DNAT) as described in [Tutorial: Filter inbound traffic with Azure Firewall DNAT using the Azure portal](tutorial-firewall-dnat.md). DNAT rules are applied first. If a match is found, an implicit corresponding network rule to allow the translated traffic is added. You can override this behavior by explicitly adding a network rule collection with deny rules that match the translated traffic. No application rules are applied for these connections.

## Inherited rules

Network rule collections inherited from a parent policy are always prioritized above network rule collections that are defined as part of your new policy. The same logic also apply to application rule collections. However, network rule collections are always processed before application rule collections regardless of inheritance.

Any rules associated with threat intelligence-based filtering is always inherited.

## Next steps

- [Learn more about Azure Firewall Manager](overview.md)