---
title: 'How To: Add simple commands to Custom Commands application - Speech service'
titleSuffix: Azure Cognitive Services
description: In this article, you will add parameter to an existing custom commands application.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: sausin
---

# Add parameters to commands
In this article, you will be adding parameters to the commands.

## Prerequisites
> [!div class="checklist"]
> * [How To: Create an empty application](./how-to-custom-commands-create-empty-project.md)
> * [How To: Add simple commands](./how-to-custom-commands-add-simple-commands.md)

## Configure parameters for TurnOn command

### Add OnOff parameter
Let's edit the existing **TurnOn** command to turn on and turn off multiple devices.
1. Because the command will now handle both on and off scenario, rename the Command to **TurnOnOff**.
   - In the left pane, select the **TurnOn** command and then select the ellipsis (...) button next to **New command** at the top of the pane.
   
   - Select **Rename**. In the **Rename command** windows, change **Name** to **TurOnOff**.

1. Next, you add a new parameter to this command which represents whether the user wants to turn the device on or off.
   - Select  **Add** present at top of the middle pane. From the drop-down, select **Parameter**.
   - In the right pane, in the **Parameters** section, add value in the **Name** box as **OnOff**.
   - Select **Required**. In the **Add response for a required parameter** window, select **Simple editor**. In the **First variation**, add
        ```
        On or Off?
        ```
   - Select **Update**.

       > [!div class="mx-imgBorder"]
       > ![Create required parameter response](media/custom-commands/add-required-on-off-parameter-response.png)
   
   - Now we configure the parameters properties. For explanation of all the configuration properties of a command, go to [references](./custom-commands-references.md#Commands-configuration). Configure the rest of the properties of the parameter as follows:
      

       | Configuration      | Suggested value     | Description                                                      |
       | ------------------ | ----------------| ---------------------------------------------------------------------|
       | Name               | `OnOff`           | A descriptive name for parameter                                                                           |
       | Is Global          | unchecked       | Checkbox indicating whether a value for this parameter is globally applied to all Commands in the application|
       | Required           | checked         | Checkbox indicating whether a value for this parameter is required before completing the Command |
       | Response for required parameter      |Simple editor > `On or Off?`      | A prompt to ask for the value of this parameter when it isn't known |
       | Type               | String          | The type of parameter, such as Number, String, Date Time or Geography   |
       | Configuration      | Accept predefined input values from internal catalog | For Strings, this limits inputs to a set of possible values |
       | Predefined input values     | `on`, `off`           | Set of possible values and their aliases         |
       
        > [!div class="mx-imgBorder"]
        > ![Create parameter](media/custom-commands/create-on-off-parameter.png)

   -  Select **Save** to save all configurations of the parameter.
 ### Add SubjectDevice parameter 
   - Next, select **Add** again to add a second parameter to represent the name of the devices which can be controlled using this command. Use the following configuration.
   

       | Setting            | Suggested value       | 
       | ------------------ | --------------------- | 
       | Name               | `SubjectDevice`         | 
       | Is Global          | unchecked             | 
       | Required           | checked               | 
       | Response for required parameter     | Simple editor > `Which device do you want to control?`    | 
       | Type               | String                |          |
       | Configuration      | Accept predefined input values from internal catalog | 
       | Predefined input values | `tv`, `fan`               |
       | Aliases (`tv`)      | `television`, `telly`     |

   - Select **Save**

### Modify example sentences

For commands with parameters, it's helpful to add example sentences that cover all possible combinations. For example:

1. Complete parameter information - `turn {OnOff} the {SubjectDevice}`
1. Partial parameter information - `turn it {OnOff}`
1. No parameter information - `turn something`

Example sentences with different degree of information allow the Custom Commands application to resolve both one-shot resolutions and multi-turn resolutions with partial information.

With that in mind, edit the example sentences to use the parameters as suggested below:

```
turn {OnOff} the {SubjectDevice}
{SubjectDevice} {OnOff}
turn it {OnOff}
turn something {OnOff}
turn something
```
Select **Save**.

> [!TIP]
> In the Example sentences editor use curly braces to refer to your parameters. - `turn {OnOff} the {SubjectDevice}`
> Use tab for auto-completion backed by previously created parameters.

### Modify completion rules to include parameters
Modify the existing Completion rule **ConfirmationResponse**.

1. In the **Conditions** section, select **Add a condition**.
1. In the **New Condition** window, in the **Type** list, select **Required parameters**. In the check-list below, check both **OnOff** and **SubjectDevice**.
1. Select **Create**.
1. In the **Actions** section, edit the existing **Send speech response** action by hovering over the action and selecting the edit button. This time, let's make use of newly created **OnOff** and **SubjectDevice** parameters

    ```
    Ok, turning the {SubjectDevice} {OnOff}
    ```
1. Select **Save**.

### Try it out
1. Select **Train** icon present on top of the right pane.

1. When training completes, select **Test**. A **Test your application** window will appear.
 Try a few interactions.

    - Input: turn off the tv
    - Output: Ok, turning off the tv
    - Input: turn off the television
    - Output: Ok, turning off the tv
    - Input: turn it off
    - Output: Which device do you want to control?
    - Input: the tv
    - Output: Ok, turning off the tv

## Configure parameters for SetTemperature command
Let's modify the **SetTemperature** command to enable it to set the temperature as directed by the user.

Add new parameter **Temperature** with the following configuration

| Configuration      | Suggested value     |
| ------------------ | ----------------|
| Name               | `Temperature`           |
| Required           | checked         |
| Response for required parameter      | Simple editor > `What temperature would you like?`
| Type               | Number          |


### Edit example utterances
```
set the temperature to {Temperature} degrees
change the temperature to {Temperature}
set the temperature
change the temperature
```

### Edit completion rules
Edit the existing completion rules as per the following configuration-

| Configuration      | Suggested value     |
| ------------------ | ----------------|
| Conditions         | Required parameter > Temperature           |
| Actions           | Send speech response > `Ok, setting temperature to {Temperature} degrees` |

### Try it out
**Train** and **Test** the changes with a few interactions.

- Input: Set temperature
- Output: What temperature would you like?
- Input: 50 degrees
- Output: Ok, setting temperature to 50 degrees

## Configure parameters to the SetAlarm command

Add a parameter called **DateTime** with the following configuration.

   | Setting                           | Suggested value                     | 
   | --------------------------------- | ----------------------------------------|
   | Name                              | `DateTime`                               |
   | Required                          | checked                                 |
   | Response for required parameter   | Simple editor > `For what time?`            | 
   | Type                              | DateTime                                |
   | Date Defaults                     | If date is missing use today            |
   | Time Defaults                     | If time is missing use start of day     |


> [!NOTE]
> In this article, we predominantly made use of string, number and DateTime parameter types. For list of all supported parameter types and their properties, go to [references](./custom-commands-references.md).


### Edit example utterances
```
set an alarm for {DateTime}
set alarm {DateTime}
alarm for {DateTime}
```

### Edit completion rules
Edit the existing completion rules as per the following configuration-

   | Setting    | Suggested value                               |
   | ---------- | ------------------------------------------------------- |
   | Actions    | Send speech response - `Ok, alarm set for {DateTime}`  |


### Try it out

**Train** and **Test** the changes.
- Input: Set alarm for tomorrow at noon
- Output: Ok, alarm set for 2020-05-02 12:00:00
- Input: Set an alarm
- Output: What time?
- Input: 5pm
- Output: Ok, alarm set for 2020-05-01 17:00:00


## Try out all the commands
Test out the all the three commands together using utterances related to different commands:

- Input: Set an alarm
- Output: For what time?
- Input: Turn on the tv
- Output: Ok, turning the tv on
- Input: Set an alarm
- Output: For what time?
- Input: 5pm
- Output: Ok, alarm set for 2020-05-01 17:00:00
    
> [!IMPORTANT]
> Notice how you could switch between the different commands.


> [!div class="nextstepaction"]
> [How To: Configure parameter as external catalog entries](./how-to-custom-commands-add-external-catalog-string-entity.md)
