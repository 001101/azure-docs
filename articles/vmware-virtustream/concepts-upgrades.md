---
title: Concepts - Upgrades of Azure VMware Solution by Virtustream private clouds
description: Learn about the key upgrade processes and features in Azure VMware Solution by Virtustream.
services: 
author: v-jetome

ms.service: vmware-virtustream
ms.topic: conceptual
ms.date: 7/8/2019
ms.author: v-jetome 
ms.custom: 

---

# Azure VMware Solution by Virtustream Upgrade Concepts

One of the key benefits of Azure VMware Solution (AVS) by Virstustream private clouds is that the platform is maintained for you. Platform maintenance includes automated upgrades to a validated software bundle. The upgrades are performed regularly, ensuring that you always have the latest validated versions of the Azure VMware Solution (AVS) by Virtustream software.

## AVS by Virtustream private cloud software upgrades

The AVS by Virtustream private cloud platform includes VMware vSphere, ESXi, vSAN, and NSX-T software. This bundle includes specific versions of each VMware software component. The bundle is tested and validated for new installations and automated lifecycle management of the platform.

The lifecycle management upgrade process doesn't require downtime for your private clouds. The upgrade process ensures you're automatically using the latest version of the validated AVS by Virtustream private cloud software. Upgrades are applied on regular cadence so that private clouds are never more than one version behind the latest release of the validated software bundle. You'll be notified of planned upgrades to your private cloud. You can defer the upgrade if your private cloud is within one version of the latest release.

Critical patches and updates are applied when they're validated. You'll be notified in advance but cannot defer critical upgrades. This policy ensures your private cloud has critical patches and updates applied immediately.

VMware software versions are provided in the [private clouds and clusters concept article][concepts-private-clouds-clusters] and the [FAQ][faq].

## Next steps

The next step is to [create a private cloud][tutorials-create-private-cloud].

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-private-clouds-clusters]: ./concepts-private-clouds-clusters.md
[faq]: ./faq.md
[tutorials-create-private-cloud]: ./tutorials-create-private-cloud.md
