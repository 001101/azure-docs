---
title: Quickstart:Create a Traffic Manager profile in Azure portal| Microsoft Docs
description: This quickstart article describes how to create a Traffic Manager profile to build a highly available web applications.
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: jeconnoc
editor: ''
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.assetid:
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2018
ms.author: kumud
---

# Quickstart: Use Traffic Manager for high availability of websites using the Azure portal

Azure Traffic Manager allows you to control user traffic distribution to websites or web applications across the different Azure regions. 

This quickstart describes how to create a Traffic Manager profile that delivers high availability of your websites. Traffic Manager continuously monitors the websites and provides automatic failover to the backup website when the primary website is not available.

If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

## Pre-requisites
This quickstart requires that you have deploy two instances of websites running in different Azure regions. The two websites serve as primary and backup endpoints for Traffic Manager.
### Create Web Apps

In this section, you create two website instances using Azure Web Apps in two Azure regions, namely, *East US* and *West Europe*. These Web Apps later serve as primary and backup service endpoints for the Traffic Manager profile.

1. On the top left-hand side of the screen, select **Create a resource** > **Web** > **Web App** > **Create**.
2. In **Web App**, enter or select the following information and enter default settings where none are specified:

     | Setting         | Value     |
     | ---              | ---  |
     | Name           | Enter a unique name for your web app  |
     | Resource group          | Select **Existing**, and the select *myResourceGroupTM1* |
     | App Service plan/Location         | Select **New**.  In the App Service plan, enter  *myAppServicePlanEastUS*, and then select **OK**. 
     |      Location  |   East US        |
    |||

3. Select **Create**.
4. Repeat steps 1-3 to create another Web App in the **West Europe** location, in a new Resource Group named *MyResourceGroupTM2*, and with a service plan name *myAppServicePlanWestEurope*.

    ![Create a Web App](./media/traffic-manager-create-profile/create-web-app.png)
    *Figure: Create a Web App*


## Sign in to Azure 

Sign in to the Azure portal at https://portal.azure.com.

## Create a Traffic Manager profile
Create a Traffic manager profile that directs user traffic based on endpoint [priority](traffic-manager-routing-methods.md#priority).

1. On the top left-hand side of the screen, select **Create a resource** > **Networking** > **Traffic Manager profile** > **Create**.
2. In the **Create Traffic Manager profile**, enter or select, the following information, accept the defaults for the remaining settings, and then select **Create**:
    | Setting                 | Value                                              |
    | ---                     | ---                                                |
    | Name                   | This name needs to be unique within the trafficmanager.net zone and results in the DNS name, trafficmanager.net which is used to access your Traffic Manager profile.                                   |
    | Routing method          | Select the **Priority** routing method.                                       |
    | Subscription            | Select your subscription.                          |
    | Resource group          | Select **Create new** and enter *myResourceGroupTM1*. |
    | Location                | Select **East US**.  This setting refers to the location of the resource group, and has no impact on the Traffic Manager profile that will be deployed globally.                              |
    |
  
    ![Create a Traffic Manager profile](./media/traffic-manager-create-profile/traffic-manager-profile.png)
    *Figure: Create a Traffic Manager profile*



## Add Traffic Manager endpoints

Add the Web App in the *East US* as primary endpoint to route all the user traffic. Add the web app in *West Europe* as a backup. When the primary endpoint is unavailable, traffic is automatically routed to the secondary endpoint.

1. In the portal’s search bar, search for the Traffic Manager profile name that you created in the preceding section and select the profile in the results that the displayed.
2. In **Traffic Manager profile**, in the **Settings** section, click **Endpoints**, and then click **Add**.
3. Enter, or select, the following information, accept the defaults for the remaining settings, and then select **OK**:

    | Setting                 | Value                                              |
    | ---                     | ---                                                |
    | Type                    | Azure endpoint                                   |
    | Name           | myPrimaryEndpoint                                        |
    | Target resource type           | App Service                          |
    | Target resource          | **Choose an app service** to show the listing of the Web Apps under the same subscription. In **Resource**, pick the App service that you want to add as the first endpoint. |
    | Priority               | Select **1**. This results in all traffic going to this endpoint if it is healthy.    |
    |        |           |

     ![Add a Traffic Manager endpoint](./media/traffic-manager-create-profile/add-traffic-manager-endpoint2.png)

      *Figure: Add a Traffic Manager endpoint*
1. Repeat steps 2 and 3 for the next Azure Web Apps endpoint. Make sure to add it with its **Priority** value set at **2**.
6.	When the addition of both endpoints is complete, they are displayed in **Traffic Manager profile** along with their monitoring status as **Online**.
  

## Test Traffic Manager profile
In this section, you test how the Traffic Manager fails over to the secondary endpoint when the primary endpoint is unavailable.

1.	In the portal’s search bar, search for the **Traffic Manager profile** name that you created in the preceding section. In the results that are displayed, click the traffic manager profile.
1. Click **Overview**.
2. The **Traffic Manager profile** displays the DNS name of your newly created Traffic Manager profile.

    ![Traffic Manager DNS name](./media/traffic-manager-create-profile/traffic-manager-dns-name.png)
     *Figure: Determine the DNS name for the Traffic Manager profile*
    
1. In a web browser, type the DNS name of your Traffic Manager profile to view your web application. In this quickstart scenario, all requests are routed to the primary endpoint that is set to **Priority 1**.

    ![Test Traffic Manager profile](./media/traffic-manager-create-profile/traffic-manager-test.png)
    *Figure: Test Traffic Manager traffic routing*
2. To view Traffic Manager failover in action, disable your primary website as follows:
    a. In the Traffic Manager Profile page, select **Settings**>**Endpoints**>*MyPrimaryEndpoint.
    b. In *MyPrimaryEndpoint*, select **Disabled**. 
    c. The primary endpoint *MyPrimaryEndpoint* status now shows as ****Disabled**.
3. Copy the DNS name of your Traffic Manager Profile (see step 3) to successfully view your Web App in a web browser. When the primary endpoint is disabled, the user traffic gets routed to the secondary endpoint.

## Delete the Traffic Manager profile
When no longer needed, delete the resource groups (**ResourceGroupTM1** and **ResourceGroupTM2**). To do so, select the resource group (**ResourceGroupTM1** or **ResourceGroupTM2**), and then select **Delete**.

## Next steps

- Learn how to [direct traffic to improve your app performance](traffic-manager-configure-performance-routing-method.md).




