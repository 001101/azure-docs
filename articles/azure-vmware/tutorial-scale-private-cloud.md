---
title: Tutorial: Scale a private cloud
description: In this tutorial, you use the Azure portal to scale an Azure VMware Solution (AVS) Preview private cloud.
ms.topic: tutorial
ms.date: 05/04/2020

#Customer intent: As a VMware administrator, I want to learn how to scale an Azure VMware Solution (AVS) private cloud in the Azure portal.
---

# Tutorial: Scale an Azure VMware Solution (AVS) Preview private cloud

You can scale the number of clusters and the number of hosts in a private cloud. The cluster and host limits in a private cloud are provided in [the private cloud concept article](concepts-private-clouds-clusters.md).

In this tutorial, you use the Azure portal to:

> [!div class="checklist"]
> * Add a cluster to an existing private cloud
> * Add hosts to an existing cluster

## Prerequisites

You need a private cloud to complete this tutorial. If you haven't yet created a private cloud, use the [create a private cloud tutorial](tutorials-create-private-cloud.md) to create a private cloud.

## Scale a private cloud

On the overview page of an existing private cloud, select **Scale private cloud**.

![Select "Scale private cloud" in Overview](./media/scale-private-cloud/ss1-select-scale-private-cloud.png)

Select **Add a cluster**.

![Select "Add a cluster"](./media/scale-private-cloud/ss2-select-add-cluster.png)

In the **Add cluster** form, enter a name, and use the slider to select the number of hosts. Select **OK**.

![Configure a new private cloud cluster](./media/scale-private-cloud/ss3-configure-new-cluster.png)

The deployment of the new cluster will begin.

## Scale a cluster in a private cloud 

On the overview page of an existing private cloud, select **Scale private cloud**.

![Select "Scale private cloud" in Overview](./media/scale-private-cloud/ss4-select-scale-private-cloud-2.png)

In the **Add cluster** form, enter a name, and use the slider to select the number of hosts. Select **OK**.

![Configure a new private cloud cluster](./media/scale-private-cloud/ss5-scale-cluster.png)

The addition of hosts to the cluster will begin.

## Next steps

If you require another AVS private cloud, [create another private cloud](tutorials-create-private-cloud.md).

<!-- LINKS - external-->

<!-- LINKS - internal -->
