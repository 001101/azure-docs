---
title: Create a water quality monitoring app with IoT Central | Microsoft Docs
description: Learn to build Create a water quality monitoring application using Azure IoT Central application templates.
author: miriambrus
ms.author: miriamb
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
---

<!---Recommended: Removal all the comments in this template before you sign-off or merge to master.--->

<!---Tutorials are scenario-based procedures for the top customer tasks identified in milestone one of the [APEX content model](contribute-get-started-mvc.md).
You only use tutorials to show the single best procedure for completing an approved top 10 customer task.
--->

# Tutorial: Create a water quality monitoring application in IoT Central

This tutorial guides you to create an Azure IoT Central water quality monitoring application from the IoT Central Water Quality Monitoring application template. 

This tutorial shows you, as a builder, how to create an Azure IoT Central water quality monitoring application using the **IoT Central Water quality monitoring application template**. 

The tutorial will learn how to: 

> [!div class="checklist"]
> * Use the Azure IoT Central **Water quality monitoring** template to create your water quality monitoring application
> * Explore and customize operator dashboard 
> * Explore water quality monitor device template
> * Explore simulated devices
> * Explore and configure rules
> * Configure jobs
> * Customize your application branding using whitelabeling


## Prerequisites

To complete this tutorial you need:
-  An Azure subscription is recommended. You can optionally use a free 7-day trial. If you don't have an Azure subscription, you can create one on the [Azure sign-up page](https://aka.ms/createazuresubscription).


## Create Water Quality Monitoring app in IoT Central

In this section, we will use the Azure IoT Central **Water quality monitoring template** to create your water quality monitoring application in IoT Central.


To create a new Azure IoT Central water quality monitoring application:  

1. Navigate to the [Azure IoT Central Home page](https://aka.ms/iotcentral) website.

* If you have an Azure subscription, sign in with the credentials you use to access it, otherwise sign in using a Microsoft account:

    ![Enter your organization account](media/tutorial-waterqualitymonitoring/sign-in.png)

2. Click on **Build** from the left navigation menu and select the **Government** tab. The government page displays several government application templates.

    ![Build Government App templates](./media/tutorial-waterqualitymonitoring/iotcentral-government-tab-overview.png)


1. Select the **Water Quality Monitoring** application template. 
This template includes sample water quality device template, simulated device, operator dashboard, and pre-configured monitoring rules.    

2. Click **Create app**, which will open **New application** creation form with the following fields:
* **Application name**. By default the application  uses *Water quality monitoring* followed by a unique ID string that IoT Central generates. Optionally, choose a friendly application name. You can change the application name later too.
* **URL** – Optionally, you can choose to your desired URL. You can change the URL later too. 
* If you have an Azure subscription, enter your *Directory, Azure subscription, and Region*. If you don't have a subscription, you can enable **7-day free trial** and complete the required contact information.  

    For more information about directories and subscriptions, see the [create an application quickstart](../core/quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

5. Click **Create** button at the bottom of the page. 

    ![Azure IoT Central Create Application page](./media/tutorial-waterqualitymonitoring/new-application-waterqualitymonitoring.png)


6. You now have created a water quality monitoring app using the Azure IoT Central **Water quality monitoring template**. IoT Central deploys the application and you will land on the **Dashboard**, which will look like the picture below.

<!-->
   ![Water Quality Monitoring application dashboard](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard.png)
-->

Your newly created application comes with pre-configured:
* Sample operator dashboards
* Sample pre-defined water quality monitor device templates
* Simulated water quality monitor devices
* Pre-configured rules and jobs
* Sample Branding using white labeling 

It is your application and you can modify it anytime. Let’s now explore the application and make some customizations.  


## Explore and customize operator dashboard 
After creating the application you land in the **Wide Water water quality monitoring dashboard**.


   ![Water Quality Monitoring dashboard](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-dashboard.png)

As a builder, you can create and customize views on the dashboard for operators. Before you try to customize, let's explore the dashboard. 

All the data displayed in the dashboard is based on simulated device data, which will be explored in the next section. 

### Viewing **Wide World water quality monitoring dashboard**
The dashboard consists of different kinds of tiles:

* **Wide World Water utility image tile**: the first tile in the dashboard is an image tile of a fictitious Water utility "Wide World Water". You can customize the tile and put your own image or remove it. 

* **Average pH KPI tile**: you can see that there are KPI tiles at the top like **Average pH in the last 30 minutes**. You can customize KPI tiles and set to a different type and time range.

*  **Water monitoring area map**: IoT Central uses Azure Maps, which you can directly set in your application and display device location. You can also map location information to your device from the application and can use Azure Maps to display it on a map. Try to hover over the map and try the controls over the map. 

* **Average pH distribution Heatmap chart**: you can choose different visualization charts to display device telemetry data in the way that is most appropriate to your application. Heatmap 

* **Critical quality indicators line chart**: you can visualize one or multiple device telemetries plotted as a line chart over a desired time range.  

* **Concentration of chemical agents bar chart**: you can visualize one or multiple device telemetry data in a bar chart as in the sample. 

* **Action button**: the dashboard includes an example of an action tile that an operator can initiate directly from the monitoring dashboard, like initiating an action to reset device properties. 

* **Properties list tiles**: the dashboard has multiple properties tiles representing threshold information, device health information, and maintenance information. 


### Customize dashboard 

As a builder, you can customize views in dashboard for operators. You can try:
* Click on **Edit** to customize the **Wide World water quality monitoring dashboard**. You can customize the dashboard by clicking on the **Edit** menu. Once the dashboard is in **edit** mode, you can add new tiles, or you can configure 

    ![Edit Dashboard](./media/tutorial-waterqualitymonitoring/edit-dashboard.png)

* You can also click on **+ New** to create new dashboard and configure from scratch. You can have multiple dashboards and you can navigate between your dashboards from the dashboard menu. 

## Explore water quality monitor device template
A device template in Azure IoT Central defines the capability of a device, which can be telemetry, properties, and commands. As a builder, you can define device templates in IoT Central that represent the capability of the devices that will be connected. In IoT Central, you can also create simulated devices to test your device template and application. 
 

The **Water quality monitoring** application you have created from the application template comes with a reference water quality monitor device template.

To view the device template:

* Click on **Device templates** from the left navigation pane of your application in IoT Central. 
* In the Device templates list you will see **Water Quality Monitor**. Open by clicking on the name.

    ![Device Template](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate.png)

### Customizing the device template

Try to customize the following:
* Navigate to **Customize** from the device template menu
* Find the `Temperature` telemetry type
* Update the **Display name** of `Temperature` to `Reported temperature`
* You can also try update unit of measurement, or set *Min value* and *Max value*
* **Save** any changes 

#### Add a cloud property 
* Navigate to **Cloud property** from the device template menu
* Add a new cloud property by clicking **+ Add Cloud Property**. In IoT Central, you can add a property that is relevant to the device but not expected to be sent by a device. As an example, a cloud property could be an alerting threshold specific to installation area, asset information, or maintenance information etc. 
* **Save** any changes 
 
#### Views 
* The water quality monitor device template comes with pre-defined views. Explore the views and you can make updates. The views define how operators will see the device data but also inputting cloud properties. 

  ![Device Template Views](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devicetemplate-views.png)

#### Publish 

* If you made any changes make sure to **Publish** the device template. 


### Create a new device template 
* Select **+ New** to create a new device template and follow the creation process. 
You will be able to create a custom device template from scratch or you can choose a device template from the Azure Device Catalog. 


## Explore simulated devices

The **Water quality monitoring** application you have created from the application template has two simulated devices mapped to the water quality monitor device template. 

### To view the devices:
* Navigate to **Device** from IoT Central left navigation pane. 

   ![Devices](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-devices.png)


* Select and click on one simulated device 

    ![Device 1](./media/tutorial-waterqualitymonitoring/waterqualitymonitor-device1.png)

* From the  **Cloud Properties** tab try updating the `Acidity (pH) Threshold` value from `8` to `9`. 
* Explore the **Device Properties** tab and **Device Dashboard** tab. 

> [!NOTE]
> Note that all the tabs have been configured from the **Device template Views**.


### Add new devices
You can add new devices by clicking on **+ New** on the **Devices** tab. 


## Explore and configure rules

In Azure IoT Central you can create rules to automatically monitor on device telemetry, and trigger an action when one or more conditions are met. The actions may include sending email notifications, or triggering a Microsoft Flow action or a webhook action to send data to other services.

The **Water quality monitoring** application you have created template has two pre-configured rules.

### To view rules:
* Navigate to **Rules** from IoT Central left navigation pane. 

   ![Rules](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-rules.png)


* Select and click on **High pH alert** which is one of the pre-configured rules in the application. 

     ![High pH Alert](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-highphalert.png)

* The `High pH alert` rule is configured to check against the condition `Acidity (pH) is greater than 8`. 

Now let's create an email action.

To add an action to the rule:

* Select **+ Email**. 
* Enter *High pH alert* as the friendly **Display name** for the action.
* Enter the email address associated with your IoT Central account in **To**. 
* Optionally, enter a note to include in text of the email.
* Select **Done** to complete the action.
* Select **Save** to save and activate the new rule. 

Within a few minutes, you should receive email when the configured **condition** is met.

> [!NOTE]
> The application will send email each time a condition is met. **Disable** the rule to stop receiving email from the automated rule. 
  
To create a new rule: 
* Select **+New** on the **Rules** from the left navigation pane.

## Configure jobs


## Customize your application 
As a builder, you can change several settings to customize the user experience in your application.

### To change the application theme:

* You can change the **Theme** by clicking the **Settings** on the masthead.


### To change the application logo and browser icon:

* Select **Administration > Customize your application**.
* Use the **Change** button to choose an image to upload as the **Application logo**.
* Use the **Change** button to choose a **Browser icon** image that will appear on browser tabs.
* You can also replace the default **Browser colors** by adding HTML hexadecimal color codes.

   ![Azure IoT Central customize your application](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-customize-your-application.png)

### To update the application image:

* Select **Administration > Application settings**.

* Use the **Select image** button to choose an image to upload as the application image. 


  
## Clean up resources

If you're not going to continue to use this application, delete your application with the following steps:

1. Open the Administration tab from the left navigation menu of your IoT Central application. 
2. Select Application settings and click Delete button at the bottom of the page. 

    ![Delete application](./media/tutorial-waterqualitymonitoring/waterqualitymonitoring-application-settings-delete-app.PNG)        

<!---Required:
To avoid any costs associated with following the tutorial procedure, a Clean up resources (H2) should come just before Next steps (H2)
--->

## Next steps

Advance to the next article to learn how to create...
> [!div class="nextstepaction"]

<!--- Required:
Tutorials should always have a Next steps H2 that points to the next logical tutorial in a series, or, if there are no other tutorials, to some other cool thing the customer can do. A single link in the blue box format should direct the customer to the next article - and you can shorten the title in the boxes if the original one doesn’t fit.
Do not use a "More info section" or a "Resources section" or a "See also section". --->
