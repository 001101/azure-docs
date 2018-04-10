---
title:  Azure Application Insights Usage Cohorts | Microsoft docs
description: Analyze how different sets or users, sessions, events, or operations that have something in common
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/10/2018
ms.author: mbullwin ; daviste
---

# Application Insights Cohorts

A cohort is a set of users, sessions, events, or operations that have something in common. In Azure Application Insights cohorts are defined by an Analytics query. If you find your yourself entering the same filters and time ranges repeatedly, cohorts offer a more flexible alternative. They also can be shared within an Application Insights resource, allowing your whole team to benefit.

![Cohorts blade](.\media\app-insights-usage-cohorts\01.png)

## Cohorts versus basic filters

While cohorts can replicate the functionality provided by filters, the fact that a cohert's definition consists of an Analytics query allows them to be far more adaptable and complex.

You might define a cohort of users that have all tried a new feature in your app. This cohort is a kind of bookmark so it’s easy for you to filter to this group of users in the future from the Application Insights tools.

> [!NOTE]
> Once created, cohorts are available from the Users, Sessions, Events, and User Flows tools.

## Example: engaged users

Your team defines an engaged user as anyone who uses your app five or more times in a given month. Let’s define a cohort of these engaged users.

1. Open the **Cohorts** tool
2. Click **Template Gallery** tab. Here you’ll find a collection of templates for various cohorts.
3. Choose **Engaged Users** – by Days Used”

    * There are three parameters for this cohort:
        * **Activites** which lets you choose which events and page views should count as “usage”
        * **Period** the definition of a month
        * **UsedAtleastCustom**: the number of times they need to use within a period to count as an engaged user.

4. Change **UsedAtleastCustom** to “5+ days” and leave **Period** to the default of 28 days.

    ![Image](.\media\app-insights-usage-cohorts\03.png)

    Now this cohort represents all user IDs that were sent with any custom event or page view on five separate days in the past 28 days.

5. Click **Save**

   > [!TIP]
   >  Give your cohort a name, like “Engaged Users (5+ Days)” and save it to “My reports” or “Shared reports”depending if you want other people with access to this Appication Insights resource to see this cohort.

6. Click **Back to Gallery**

### What can you do with this cohort?

Open the **Users** tool > In the **Show** dropdown > Choose the cohort you just created under **Users who belong to…**

Now the Users tool is filtered to this cohort of users:

![Users pane filtered to a particular cohort](.\media\app-insights-usage-cohorts\04.png)

* A few important things to notice:
    * This is a filter you couldn’t have applied through normal filters. The date logic is more advanced.
    * You can further filter this cohort using the normal filters in the Users tool. So while the cohort is defined on 28 day windows, you can still adjust the time range in the Users tool to be 30, 60, or 90 days. 

This lets you ask more sophisticated questions like, _For people who were engaged in the past 28 days, how did those same people behave over the past 60 days?_ etc. that would otherwise be impossible to express through the query builder.

## Example: events cohort

You can also make cohorts of events. Let’s define a cohort of the events and page views, then see how to use them from the other tools. This might be useful to define a set of events that your team considers “active usage,” or to define a set of events related to a certain new feature.

1. Open the **Cohorts)) tool
2. Click the **Template Gallery** tab. Here you’ll find a collection of templates for various cohorts.
3. Choose **Events Picker**

![Screenshot of Events picker](.\media\app-insights-usage-cohorts\06.png)

1. In the **Activities** dropdown, select the events you’d like to be in the cohort
2. Save the cohort and give it a name

### What can you do with this cohort?

Open the **Users** tool > In the **Who used** dropdown, select the cohort you just created under the **Cohorts** header.

Now the Users tool is filtered to users who used this set of events

## Example: Active users where you modify query

The previous two cohorts were defined using dropdowns. But we can also define cohorts with Analytics queries for total flexibility. Let’s see how by creating a cohort of users from the United Kingdom.

![Animated Gif walking through use of Cohorts tool](.\media\app-insights-usage-cohorts\cohorts001.gif)

1. Open the **Cohorts** tool
2. Click **Template Gallery** tab
3. Choose **Blank Users cohort**

    ![Blank Users Cohort](.\media\app-insights-usage-cohorts\01.png)
    
    * There are three sections:
        * A Markdown text section where you can describe the cohort in more detail for others on your team
        * A parameters section you can use to make your own parameters, like the **Activities** and other dropdowns from the previous two examples. 
        * A query section that you use to define the cohort using an Analytics query.
        * In the query section, you write an Analytics query that selects the certain set of rows that describe the cohort you want to define. The Cohorts tool then implicitly adds a “| summarize by user_Id” clause to the query. This is previewed below the query in a table so you can make sure your query is returning results.

    > [!NOTE]
    > If you don’t see the query, try resizing the section to make it taller and reveal the query.

4. Copy-paste the following into the query editor:

    ```KQL
    union customEvents, pageViews
    | where client_CountryOrRegion == "United Kingdom"
    ```

5. Click **Run Query**. You should see user IDs appear in the table. If not, change to a country where your application has users.

6. Save and name this cohort.

## Frequently asked questions

_I’ve defined a cohort of users from a certain country. When I compare this cohort in the Users tool to just setting a filter on that country in the Users tool, I see different results. Why?_

Cohorts and filters are different. Suppose you have a cohort of users from the United Kingdom (defined like the above example) and you compare its results to setting the filter “Country or region = United Kingdom.”

* The cohort version will show all events from users who have sent at least one event from the United Kingdom in the current time range. If you split by country or region, you’ll likely see many countries and regions.
* The filters version will only show events from the United Kingdom. If you split by country or region, you’ll only see the United Kingdom.

## Learn more
- [Analytics query language](https://go.microsoft.com/fwlink/?linkid=856587)
- [Users, Sessions, Events](app-insights-usage-segmentation.md)
- [User Flows](app-insights-usage-flows.md)
- [Usage overview](app-insights-usage-overview.md)
