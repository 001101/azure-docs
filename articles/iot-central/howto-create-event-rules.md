---
title: Create and manage event rules in your Azure IoT Central application | Microsoft Docs
description: Azure IoT Central event rules enable you to monitor your devices in near real time and to automatically invoke actions, such as sending an email, when the rule triggers.
author: ankitgupta
ms.author: ankitgup
ms.date: 04/29/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
---

# Create an Event rule and set up notifications in your Azure IoT Central application

You can use Azure IoT Central to remotely monitor your connected devices. Azure IoT Central rules enable you to monitor your devices in near real time and automatically invoke actions, such as send an email or trigger Microsoft Flow, when the rule triggers. In just a few clicks, you can define the condition to monitor your device data and configure the corresponding action. This article explains event monitoring rule in detail.

Devices can use [event measurement](howto-set-up-template.md) to send important or informational device events. An event rule triggers when the selected device event is reported by the device.

## Create an event rule

To create an event rule, the device template must have atleast one Event measurement defined. This example uses a refrigerated vending machine device that reports fan motor error event. The rule monitors the event reported by the device and sends an email whenever the event is reported.

1. Using Device Explorer, navigate to the device template you are adding the rule for.

1. Under the selected template click on an existing device. 

    >[!TIP] 
    >If the template doesn't have any devices then add a new device first.

1. If you haven’t created any rules yet, you see the following screen:

    ![No rules yet](media\howto-create-event-rules\Rules_Landing_Page.png)


1. On the **Rules** tab, click **+ New Rule** to see the types of rules you can create.


1. Click on **Event** tile to create a rule.

    ![Rule types](media\howto-create-event-rules\Rule_Types.png)

    
1. Enter a name that helps you to identify the rule in this device template.

1. To immediately enable the rule for all the devices created from this template, toggle **Enable rule**.

    ![Rule Detail](media\howto-create-event-rules\Rule_Detail.png)

    >[!NOTE] 
    >The rule automatically scopes all the devices under the device template.

### Configure the rule condition

Condition defines the criteria that is monitored by the rule.

1. Choose the **+** next to **Condition** to add a new condition.

1. Choose the event that you want to monitor from the Measurement dropdown. In this example, **Fan Motor Error** event has been selected.

   ![Condition](media\howto-create-event-rules\Condition_Filled_Out.png) 


1. Optionally, you can also set **Count** as **Aggregation** and provide the corresponding threshold.

    - Without aggregation, the rule triggers for each event data point that meets the condition. For example, if the rule is configured to trigger when 'Fan Motor Error' event occurs then the rule will trigger almost instantaneously when the device reports that event.
    - If Count is used as an aggregate function, then you have to  provide a **Threshold** and an **Aggregate time window** over which the condition needs to be evaluated. In this case, the count of events are aggregated and the rule will trigger only if the aggregated event count matches the threshold.
 
    For example, if you want to alert when there are more than three device events within 5 minutes, then select the event and set the aggregate function as "count",  operator as "greater than" and "threshold" as 3. Set "Aggregation time period" as "5 minutes". The rule triggers when more than three events are sent by the device within 5 minutes. The rule evaluation frequency is the same as **Aggregate time window**, which means the rule is  evaluated once every 5 minutes in this example. 

    ![Add Event Condition](media\howto-create-event-rules\Aggregate_Condition_Filled_Out.png)

    >[!NOTE] 
    >More than one event measurement can be added under **Condition**. When multiple conditions are specified, all the conditions must be met for the rule to trigger. Each conditon gets joined by an 'AND' clause implicitly. When using aggregate, every measurement must be aggregated.

### Configure the action

This section shows you how to specify what the rule does when the condition matches by adding an action.

1. Choose the **+** next to **Actions**. Here you see the list of available actions. During public preview, **Email** is the only supported action.

    ![Add Action](media\howto-create-event-rules\Add_Action.png)

1. Choose the **Email** action, enter a valid email address in the **To** field, and provide a note to appear in the body of the email when the rule triggers.

    > [!NOTE]
    > Emails are only sent to the users that have been added to the application and have logged in at least once. Learn more about [user management](howto-administer.md) in Azure IoT Central.

   ![Configure Action](media\howto-create-event-rules\Configure_Action.png)

1. To save the rule, choose **Save**. The rule goes live within a few minutes and starts monitoring the events being sent to your application. When the condition specified in the rule matches, the rule triggers the configured email action.

## Parameterize the rule

Actions can also be configured using **Device Property** as a parameter. If an email address is stored as a device property, then it can be used when you define the **To** address.

## Delete a rule

If you no longer need a rule, delete it by opening the rule and choosing **Delete**. Deleting the rule removes it from the device template and all the associated devices.

## Enable or disable a rule for a device template

Navigate to the device and choose the rule you want to enable or disable. Toggle the **Enable rule for all devices of this template** button to enable or disable the rule for all devices that are associated with the device template.

## Enable or disable a rule for a device

Navigate to the device and choose the rule you want to enable or disable. Toggle the **Enable rule for this device** button to either enable or disable the rule for that device.

## Next steps

Now that you have learned how to create rules in your Azure IoT Central application, here is the suggested next step:

> [!div class="nextstepaction"]
> [Add Microsoft Flow action in rules](howto-add-microsoft-flow.md)
> [Add Webhook action in rules](howto-create-webhooks.md)
> [How to manage your devices](howto-manage-devices.md)