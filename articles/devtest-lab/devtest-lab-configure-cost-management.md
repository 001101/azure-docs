---
title: View the monthly estimated lab cost trend in Azure DevTest Labs | Microsoft Docs
description: Learn about the Azure DevTest Labs monthly estimated cost trend chart.
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: ''

ms.assetid: 1f46fdc5-d917-46e3-a1ea-f6dd41212ba4
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/22/2016
ms.author: tarcher

---
# View the monthly estimated lab cost trend in Azure DevTest Labs
The Cost Management feature of DevTest Labs helps you track the cost of your lab. 
This article illustrates how to use the **Monthly Estimated Cost Trend** chart 
to view the current calendar month's estimated cost-to-date and the projected end-of-month cost for the current calendar month. In this article, you learn how to view the monthly estimated cost trend chart in the Azure portal.

## Viewing the Monthly Estimated Cost Trend chart
To view the Monthly Estimated Cost Trend chart, follow these steps: 

1. Sign in to the [Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040).
1. If necessary, select **All Services**, and then select **DevTest Labs** from the list. (Your lab might already be shown on the Dashboard under **All Resources**).
1. From the list of labs, select the desired lab.  
1. On the lab's **Overview** area, select **Configuration and policies**.   
1. On the left under **COST TRACKING**, select **Cost trend**.

   The following screen shot shows an example of a cost chart. 
   
    ![Cost chart](./media/devtest-lab-configure-cost-management/graph.png)

The **Estimated cost** value is the current calendar month's estimated cost-to-date. The **Projected cost** is the estimated cost for the entire current calendar month, calculated using the lab cost for the previous five days.

The cost amounts are rounded up to the next whole number. For example: 

* 5.01 rounds up to 6 
* 5.50 rounds up to 6
* 5.99 rounds up to 6

As it states above the chart, the costs you see in the chart are *estimated* costs using [Pay-As-You-Go](https://azure.microsoft.com/offers/ms-azr-0003p/) offer rates.
Additionally, the following are *not* included in the cost calculation:

* CSP and Dreamspark subscriptions are currently not supported as Azure DevTest Labs uses the [Azure billing APIs](../billing/billing-usage-rate-card-overview.md) to calculate the lab cost, which does not support CSP or Dreamspark subscriptions.
* Your offer rates. Currently, we are not able to use your offer rates (shown under your subscription) that you have negotiated with Microsoft or Microsoft partners. We are using Pay-As-You-Go rates.
* Your taxes
* Your discounts
* Your billing currency. Currently, the lab cost is displayed only in USD currency.

## Managing cost targets for your lab
DevTest Labs lets you manage the costs in  your lab to better 

1. On your lab's **Overview** pane, select **Configuration and policies**.
1. On the left under **COST TRACKING**, select **Cost trend**.

    ![Manage target button](./media/devtest-lab-configure-cost-management/cost-trend.png)

1. In the **Cost trend** pane, select **Manage target**.

    ![Manage target button](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)

1. In the Manage target pane, specify your desired thresholds and how you want those thresholds reported when they are reached. 

    ![Manage target pane](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Select a time period for when you want cost targets tracked.
      - **Monthly**: cost targets are tracked every month
      - **Fixed**: cost targets are tracked for the date range you specify in the Start date and End date fields. Typically, this might correspond with the how long your project is scheduled to run.
   - Specify a **Target cost** of how much you project to spend on this lab in the time period you defined. 
   - Select any thresholds you want to monitor, in increments of 25%, up to 125% of your specified **Target cost**.
      - **Notify**: When this threshold is met, you are notified by the webhook URL you specify.
      - **Plot on chart**: When this threshold is met, the results are plotted on a graph that you can view in the cost chart described in [Viewing the Monthly Estimated Cost Trend chart](#viewing-the-monthly-estimated-cost-trend-chart).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## Related blog posts
* [Two more things to keep your cost on track in DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2016/06/21/keep-your-cost-on-track/)
* [Why Cost Thresholds?](https://blogs.msdn.microsoft.com/devtestlab/2016/04/11/why-cost-thresholds/)

## Next steps
Here are some things to try next:

* [Define lab policies](devtest-lab-set-lab-policy.md) - Learn how to set the various policies used to govern how your lab and its VMs are used. 
* [Create custom image](devtest-lab-create-template.md) - When you create a VM, you specify a base, which can be either a custom image or a Marketplace image. This article illustrates
  how to create a custom image from a VHD file.
* [Configure Marketplace images](devtest-lab-configure-marketplace-images.md) - DevTest Labs supports creating VMs based on Azure Marketplace images. This article
  illustrates how to specify which, if any, Azure Marketplace images can be used when creating VMs in a lab.
* [Create a VM in a lab](devtest-lab-add-vm.md) - Illustrates how to create a VM from a base image (either custom or Marketplace), and how to work with
  artifacts in your VM.

