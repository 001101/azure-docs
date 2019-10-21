---
title: Microsoft Azure Peering Service FAQ
description: Learn about Microsoft Azure Peering Service FAQ
services: peering-service
author: ypitsch
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 011/04/2019
ms.author: ypitsch
---

# Peering Service FAQ

This article elucidates on the most frequently asked questions about Peering Service connection.

**Q. Who are the target customers?**  

A. Enterprises who connect to Microsoft Cloud using the internet as transport.  

**Q. Can customers sign up for the Peering Service with multiple providers?** 

A. Yes, customers can sign up for the *Peering Service* with multiple providers in the same region or different region, but not for the same prefix.

**Q. Can a customer select a unique ISP for their sites per geographical region?**  

 Yes, customer can do so. It is recommended to select the Partner ISP per region that suits their business and operational needs.

**Q. How is Peering Service different from normal Internet access?**

A. Partners who have registered to teh Microsoft Peering service have been working with Microsoft to offer optimized latency and reliability to Microsoft Services.  

**Q. How is Peering Service different from ExpressRoute?**

A. ExpressRoute is a private, dedicated connection from given one or multiple locations. While Peering Service offers optimized Public connectivity and does not support any private connectivity. It also offers optimized connectivity for local internet breakouts.


**Q. What is Microsoft Edge PoP?**

A. It is a physical location where Microsoft interconnects with other networks. In that Microsoft Edge PoP location, services such as Azure Front Door and Azure CDN are hosted. Refer [Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-features) for more information.


## Next steps

Learn about [Peering Service connection](peering-service-faq.md).

To find a Service Provider, see [Peering Service partners and locations](peering-service-location-partners.md).

To onboard the Peering Service connection, see [Peering Service connection](peering-service-onboarding-model.md).

To register the connection, see [Peering Service connection](peering-service-azure-portal.md).

To measure the telemetry, see [Measure connection telemetry](peering-service-measure-connection-telemetry.md).