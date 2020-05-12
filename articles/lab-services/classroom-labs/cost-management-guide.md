---
title: Cost management guide for Azure Lab Services
description: Understand the different ways to view costs for Lab Services.
author: rbest

ms.author: rbest
ms.date: 05/09/2020
ms.topic: article
ms.service: lab-services
---

# Cost Management for Azure Lab Services

Cost management can be broken down into two distinct areas, cost estimation and cost analysis.  Cost estimation occurs when setting up the lab to make sure that initial structure of the lab will fit within the expected budget.  Cost analysis usually occurs at the end of the month, to analyze the costs and determine the necessary actions for the next month.

# Estimating the lab costs

Each lab dashboard has a Costs & Billing section that lays out a rough estimate of what the lab will cost for the month.  The cost estimate summarizes the hour usage with the maximum number of users by the estimated cost per hours.  To get the most accurate estimate setup the lab, including the schedule, and the dashboard will reflect the estimated cost.  This estimate may not be all the possible costs, there are a few resources that are not included.  The template cost is not factored into the cost estimate as it may vary significantly in the amount of time needed to create the template, but the template cost is the same as overall lab cost per hour for manual estimation. Any Shared image gallery costs are not included into the lab dashboard as a gallery may be shared between multiple labs.  Lastly hours incurred when the lab creator starts a machine are excluded from this estimate.

> [!div class="mx-imgBorder"]
> ![Dashboard cost estimation](../media/cost-management-guide/dashboard-cost-estimation.png)

# Analyzing previous months usage

The cost analysis is for reviewing previous months usage to help determine any adjustments for the lab.  The breakdown of costs in the past can be found in the Subscription Cost Analysis.  In the Azure Portal, one can type "Subscriptions" in the upper search field then select the Subscriptions option.  

> [!div class="mx-imgBorder"]
> ![Subscription search](../media/cost-management-guide/subscription-search.png)

Select the specific subscription that is to be reviewed.

> [!div class="mx-imgBorder"]
> ![Subscription selection](../media/cost-management-guide/subscription-select.png)

 Select "Cost Analysis" in the left hand pane under "Cost Management".

 > [!div class="mx-imgBorder"]
> ![Subscription cost analysis](../media/cost-management-guide/subscription-cost-analysis.png)

This dashboard will allow in-depth cost analysis, including the ability to export to different file types on a schedule.  The Cost Management has numerous capabilities for more information see > ![Cost Management Billing Overview]()

Filtering by the resource type: microsoft.labservices/labaccounts will show only the cost associated with Lab services.

# Understanding the usage

Below is a sample of the cost analysis. 

 > [!div class="mx-imgBorder"]
> ![Subscription cost analysis](../media/cost-management-guide/cost-analysis.png)

There are six columns: Resource, Resource Type, Location, Resource group name, Tags, Cost.  The Resource column contains the information about the Lab account, Lab Name, and the VM.  The rows with Lab account / Lab Name / default is the cost for the lab, which can be seen on the second and third rows.  The used VMs will have a cost under the Lab account / Lab Name / default / VM name.  In this example summing the first row with second row, both starting with "aaalab / dockerlab" will give you the total cost for the lab "dockerlab" in the "aaalab" Lab Account.

 To get Shared image gallery information, change the resource type to Microsoft.Compute / Galleries which will give you the overall cost for the image gallery.  Be careful multiple labs could be using the same Share Image Gallery.

 # Separating costs

 Some Universities have used the Lab Account and the resource group as ways to separate out the different classes.  Each class will have it's own Lab account and resource group. In the cost analysis pane, add a filter based on the resource group name with the appropriate resource group name for the class and only the costs for that class will be visible.  This allows a clearer delineation between the different classes when viewing the costs.  The scheduled export feature of the Cost analysis allows for the costs of each class to be downloaded in separate files. The Share Image galleries may not show up in the costs depending on where the gallery is stored. 