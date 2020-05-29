---
title: Get Started With SMS
description: TODO
author: mikben    
manager: jken
services: azure-project-spool

ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-project-spool

---
# Quickstart: Send an SMS message with .NET (C#)
Azure Communication Services lets you easily send and receive SMS messages. In this quick start, learn how to use Communication Services to send SMS messages using the .NET SDK in C#.

You can receive SMS messages in two ways explorered in seperate pages:

1. Using the `GetSMSMessages()` API, this is striaghtforward but requires your application to poll ACS.
2. Using ACS' EventGrid integration to subscribe to webhooks, and have ACS call your service when an SMS message is received. See the [EventGrid concept for more information.](../concepts/acs-event-grid.md)

## Prerequisites

If you don’t already have Visual Studio 2019 installed, you can download and use the **free** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Make sure that you enable **Azure development** during the Visual Studio setup.

This quick start also requires:
- **Deployed Azure Communication Service resource.** Check out the quick start for making an ACS resource in the Azure portal.
- **An ACS configured telephone number.** Sending SMS messages requires a telephone number, which ACS can help you obtain easily. Check out the quick start for telephone number management for more information. 

## Obtain a connection string
Connection strings provide addressing and key information necessary for service clients to connect and authenticate to Azure Communication Services to drive activity. You can get connection strings from the Azure portal or programmatically with Azure Resource Management (ARM) APIs.

In the Azure Portal, use the `Keys` page in `Settings` to generate keys.

![Screenshot of Key page](../media/keys.png)


## Create a .NET console application and download the SMS .NET SDK
This quick start is simple enough that we will leverage the .NET console application template in Visual Studio to get started. 

1. Start Visual Studio.
2. Create a new .NET console project

![Screenshot of Visual Studio console app template](../media/console-app-template.png)

The ACS SMS .NET SDK is available in GitHub as source code, and on nuget as a built assembly. Visual Studio's nuget package manager makes it easy to find and downlaod packages:

![Screenshot of Visual Studio Manage Package link in solution explorer](../media/solution-explorer-nuget-link.png)

3. Find the ACS SMS SDK in nuget package manager by searching for Azure.Communication.Sms
4. Accept the license and load dependencies. Visual Studio and nuget will automatically load dependencies for `Azure.Communication.Sms` such as  `Azure.Core`.

![Screenshot of Visual Studio nuget package manager](../media/nuget-package-sms.PNG)

> [!NOTE]
> During the private preview, `.nupkg` files are not available in public nuget, but must be download from Azure Dev Ops or GitHub. Once locally downloaded to [your local nuget package source location](https://stackoverflow.com/questions/10240029/how-do-i-install-a-nuget-package-nupkg-file-locally) you can follow these same steps to include your project.


## Authenticate the SMS client and send a SMS message
Now that we have the SDK included in our project we can send an SMS message with a few lines of code:

    ```cs
    using System;
    using Azure.Communication.Sms;

    namespace SMSSend
    {
        class Program
        {
            static void Main(string[] args)
            {
                string connectionString = "<connectionString>";
                SmsClient sms = new SmsClient(connectionString);
                sms.Send("1-555-111-1111", "1-555-222-2222", "Hello World via SMS");          
            }
        }
    }
    ```

Step by step:

1. Include the ACS namespaces with `using Azure.Communication.SMS;`
2. Initialize the `SMSClient` using the connection string retrieved in the Azure Portal. It is important to protect connection strings, they should only be used by trusted services and devices.
3. Use the `SmsClient.Send()` API to send an SMS message using a number allocated to the Azure Communication Service resource, in this case 1-555-111-1111, to a destination phone number 1-555-222-2222. To programmatically list allocated phoned numbers and acquire new ones, check out the allocate phone number quick start.

