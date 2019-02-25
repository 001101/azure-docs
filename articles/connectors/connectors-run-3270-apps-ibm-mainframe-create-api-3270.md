---
title: Connect to 3270 apps on IBM mainframes with Azure - Azure Logic Apps
description: Integrate and automate 3270 screen-driven apps with Azure by using Azure Logic Apps and HIS 3270 connector
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ChristopherHouser
ms.author: chrishou
ms.reviewer: estfan, valthom
ms.topic: article
ms.date: 03/06/2019
tags: connectors
---

# Integrate 3270 screen-driven apps on IBM mainframes with Azure by using Azure Logic Apps and HIS 3270 connector

> [!NOTE]
> This connector is in *public preview*.

With Azure Logic Apps and the HIS 3270 connector, you can 
access and run IBM mainframe apps that you usually drive by 
navigating through 3270 emulator screens. That way, you can 
integrate your IBM mainframe apps with Azure, Microsoft, 
and other apps, services, and systems by creating automated 
workflows with Azure Logic Apps. The connector communicates 
with IBM mainframes by using the TN3270 protocol and is 
available in all Azure Logic Apps regions except for Azure 
Government and Azure China. If you're new to logic apps, review 
[What is Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

This article describes how the HIS 3270 connector helps you run 
3270 screen-driven apps on IBM mainframes by creating logic apps, 
what you need for the connector to work, and the steps for adding 
the connector's actions to your logic apps so you can run your 
mainframe apps from automated logic app workflows.

## Why use the HIS connector?

To access apps on IBM mainframes, you typically use a 3270 
terminal through a 3270 emulator, often called a "green screen". 
This method is a time-hardened way but has many limitations. 
Host Integration Server (HIS) lets you work directly with 
these apps, but sometimes, separating the screen and business 
logic isn't possible. Or maybe you no longer have information 
about how the host applications work. 

For these scenarios, you can create .NET apps that programmatically 
drive the 3270 screens without making changes to the host. To create 
these apps, you can use the Session Integrator tool, which is a .NET 
library for writing custom apps that can access 3270 screen-driven data, 
also known as "screen scraping".

To extend these scenarios, the HIS 3270 connector in Azure Logic 
Apps works with the standalone 3270 Design Tool, which you use to 
"capture" the host screens for a specific task and define methods 
with input and output parameters. This tool converts that information 
into metadata the connector uses when you add an 3270 action to your 
logic app. The tool generates a metadata file that defines the navigation 
through the 3270 screens, the methods that you can use as actions in your 
logic app, along with the methods' input and output parameters, for driving 
your IBM mainframe apps. 

So, by handling navigation through the 3270 screens, entering data into the 
host application, and returning the results to your logic app, you can integrate 
your legacy apps with Azure, Microsoft, and other apps, services, and systems 
that Azure Logic Apps supports.

## Prerequisites

* An Azure subscription. If you don't have an Azure subscription, 
<a href="https://azure.microsoft.com/free/" target="_blank">sign up for a free Azure account</a>.

* Access to a TN3270 Server

* Basic knowledge about 
[how to create logic apps](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Recommended: An [integration service environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment.md), 
which you use as the location for creating and running your 
logic app. An ISE provides access from your logic app to 
resources that are protected inside Azure virtual networks.

* A logic app to use for automating and running your 3270 app. 
The 3270 connector doesn't have triggers, so use another 
trigger to start your logic app, such as the **Recurrence** 
trigger. You can then add 3270 actions. To get started, 
[create a blank logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md). 
If you use an ISE, select that ISE as your logic app's location.

* A Host Integration Designer XML (HIDX) file, 
which defines the methods you use for creating and 
running a 3270 action. You can create this file by 
using the standalone 3270 Design Tool.

* An [integration account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) 
where you can later add and store your HIDX file as a map so your 
logic app can access the metadata and method definitions in that file. 
Make sure your integration account is linked to the logic app you're using. 
Also, if you use an ISE, make sure your integration account's 
location is the same ISE that your logic app uses.

## Create app metadata

In a 3270 screen-driven app, the screens and data fields are unique 
to your scenarios, so the 3270 connector needs this information about 
your app, which you can provide as metadata. This metadata describes 
information that helps your logic app identify and recognize screens, 
describes how to navigate between screens, where to input data, 
and where to expect results. To specify and generate this metadata, 
you use the standalone 3270 Design Tool, which walks you through 
these specific *modes*, or stages, as described later in more details:

* **Capture**: In this mode, you identify the screens required for completing 
a specific task with your mainframe app, for example, getting a bank balance. 

* **Navigation plan**: In this mode, you specify the plan or path for how 
to navigate through your mainframe app's screens for the specific task.

* **Method**: In this mode, you define the method, for example, `GetBalance`, 
that describes the screen navigation path. You also choose the fields on each 
screen that become the method's input and output parameters.

### Capture screens

In this mode, you mark an item on each 3270 screen that uniquely identifies that screen. 
For example, you might specify a line of text or a more complex set of conditions, 
such as specific text and a non-empty field. You can either record these screens 
over a live connection to the host server, or import this information from an IBM 
Basic Mapping Support (BMS) map. The live connection uses a TN3270 emulator for 
connecting to the host. Each connector action must map to a single task that starts 
with connecting to your session and ends with disconnecting from your session.

1. Connect to your host server and start your session.

1. In the design tool, to begin capturing screens, choose **Start**.

1. From the first screen, step through your app for the specific task.

1. After you finish the task, log off from your session as you usually do.

1. In the design tool, choose **Stop**. Disconnect from the host.

1. After capturing all the screens for the task, now specify the 
fields for identifying each screen.

   With the 3270 data stream, screens don't have default identifiers, 
   so you need to select unique text on each screen. For complex scenarios, 
   you can specify multiple conditions, for example, unique text and a 
   field with a specific value.

After you finish selecting the identification fields, 
move to the next mode.

### Define navigation

In this mode, you define the flow or steps for navigating 
through your mainframe app's screens for the specific task. 
For example, sometimes, you might have more than one path that 
your app can take where one path produces the correct result, 
while the other path produces an error. For each screen, 
specify the keystrokes necessary for moving to the next screen, 
such as `CICSPROD <enter>`.

After you capture the screens for a task, the designer tool 
shows thumbnails that represent those screens. 

1. In the design tool, to start your plan, choose **New Plan**.

1. Drag those thumbnails to the navigation plan surface.

   Along with these screens, you have a screen that's named "Empty". 
   When you first connect to the Customer Information Control System (CICS), 
   you must choose **Clear** before you can enter the name for the transaction 
   you want to run. This "clear" screen doesn't have any attributes to recognize. 
   So, by default, this screen is named "Empty". To represent the blank screen 
   where you enter the transaction name, use this "Empty" screen.

1. Arrange the screens following the order required for the task you're defining.

1. After arranging the screens, draw arrows between the screens by using the Flow tool. 

   These arrows define the flow, including forks and joins, through the screens.

1. For each screen, specify the Automatic Initiate Descriptor (AID) 
key and the static text that moves the flow to the next screen.
You might have just the AID key, or both the AID key and text. 

> [!TIP]
> If you're automating several tasks that use the same connect and 
> disconnect screens, the design tool provides special Connect 
> and Disconnect plan types. After you define these plans, 
> you can add them to start and end in your navigation plan.

After you finish your navigation plan, 
you can define methods in the next mode.

### Define method

For each parameter, you specify the data type, such as a string, integer, 
date or time, and so on. When you're done, you can test your method on the 
live host and confirm that the method works as expected. You then generate 
the metadata file, or Host Integration Designer XML (HIDX) file, which now 
has the method definitions to use for creating and running an action for 
the HIS 3270 connector.

### Add metadata file to integration account

When you're done, 
[add your HIDX file as a map to your integration account](../logic-apps/logic-apps-enterprise-integration-maps.md) by using the Azure portal. 
In a later section on this page, you learn how to add an HIS 3270 
action to your logic app. When you add this action for the first time, 
you're prompted to create a connection and provide connection 
information, such as your integration account and host server. 
After you create the connection, you can select your previously 
added HIDX file, the method to run, and the parameters to use. 

After you finish all these steps, the action you created is 
ready for connecting to your IBM mainframe, drive screens 
for your app, enter data, return results, and so on. 
You can also continue adding other actions to your logic 
app for integrating with other apps, services, and systems.

## Run HIS 3270 actions

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Sign in to the [Azure portal](https://portal.azure.com), 
and open your logic app in Logic App Designer, if not open already.

1. Under the last step where you want to add an action, 
choose **New step**, and select **Add an action**. 

1. Under the search box, choose **Enterprise**. 
In the search box, enter "3270" as your filter. 
From the actions list, select this action: 
**Runs a mainframe program over a TN3270 connection**

   ![Select 3270 action](./media/connectors-create-api-3270/select-3270-action.png)

   To add an action between steps, 
   move your pointer over the arrow between steps. 
   Choose the plus sign (**+**) that appears, 
   and then select **Add an action**.

1. If no connection exists yet, provide the 
necessary information for your connection, 
and choose **Create**.

   | Property | Required | Value | Description |
   |----------|----------|-------|-------------|
   | **Connection Name** | Yes | <*connection-name*> | The name for your connection |
   | **Integration Account ID** | Yes | <*integration-account-name*> | Your integration account's name |
   | **Integration Account SAS URL** | Yes | <*integration-account-SAS-URL*> | Your integration account's Shared Access Signature (SAS) URL |
   | **Server** | Yes | <*TN3270-server-name*> | The host name for your TN3270 server |
   | **Port** | No | <*TN3270-server-port*> | The port used by your TN3270 server |
   | **Device Type** | No | <*IBM-terminal-model*> | The model name or number for the IBM terminal to emulate |
   | **Code Page** | No | <*code-page-number*> | The code page number for the host |
   | **Logical Unit Name** | No | <*logical-unit-name*> | The specific logical unit name to request from the host |
   | **Enable SSL?** | No | On or off | Turn on or turn off SSL encryption. |
   | **Validate host ssl certificate?** | No | On or off | Turn on or turn off validation for the server's certificate. |
   ||||

   For example:

   ![Connection properties](./media/connectors-create-api-3270/connection-properties.png)

1. Provide the necessary information for the action:

   | Property | Required | Value | Description |
   |----------|----------|-------|-------------|
   | **Hidx Name** | Yes | <*HIDX-file-name*> | Select the 3270 HIDX file you want to use. |
   | **Method Name** | Yes | <*method-name*> | Select the method in the HIDX file to use. After you select a method, the **Add new parameter** list appears so you can select parameters to use with that method. |
   ||||

   For example:

   **Select HIDX file**

   ![Select HIDX file](./media/connectors-create-api-3270/select-hidx-file.png)

   **Select method**

   ![Select method](./media/connectors-create-api-3270/select-method.png)

   **Select parameters**

   ![Select parameters](./media/connectors-create-api-3270/add-parameters.png)

1. When you're done, save and run your logic app.

   After your logic app finishes running, the steps from the run appear. 
   Successful steps show checkmarks, while unsuccessful steps show the letter "X".

1. To review the inputs and outputs for each step, expand that step.

1. To review the outputs, choose **See raw outputs**.

# Connector reference

For technical details about triggers, actions, and limit, which are described
by the connector's OpenAPI (formerly Swagger) description, review the
connector's [reference page](/connectors/<*replace-with-api-topic-file-name*>).

# Get support

* For questions, visit the 
[Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

* To submit or vote on feature ideas, visit the [Logic Apps user feedback site](https://aka.ms/logicapps-wish).

# Next steps

* Learn about other [Logic Apps connectors](../connectors/apis-list.md)