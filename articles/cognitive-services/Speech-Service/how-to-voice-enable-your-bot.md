---
title: "Tutorial: Deploy a voice-enabled Echo-Bot using Direct Line Speech channel"
titleSuffix: Azure Cognitive Services
description: TODO
services: cognitive-services
author: dargilco
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/11/2019
ms.author: dcohen
---

<!---Recommended: Remove all the comments in this template before you
sign-off or merge to master.--->

<!---Tutorials are scenario-based procedures for the top customer tasks
identified in milestone one of the
[Content + Learning content model](contribute-get-started-mvc.md).
You only use tutorials to show the single best procedure for completing
an approved top 10 customer task.
--->

# Tutorial: Deploy a voice-enabled echo-bot using Direct Line Speech channel 
<!---Required:
Starts with "Tutorial: "
Make the first word following "Tutorial:" a verb.
--->

You can now use the power of Microsoft's Cognitive Speech Services to easily make any chat bot accessible to users by voice.

<!---Required:
Lead with a light intro that describes, in customer-friendly language,
what the customer will learn, or do, or accomplish. Answer the
fundamental “why would I want to do this?” question.
--->

In this tutorial, you will modify the simple echo-bot sample code, deploy it to Azure, and register it with the Bot-Framework Direct Line Speech channel. You will then run and configure a sample client application for Window (Direct Line Speech Client), allowing you to talk to your bot and hear it speak back to you.

This tutorial is targeted for developers who are just starting their journey with Azure, Bot-Framework bots, Direct Line Speech or Speech SDK, and want to quickly build a working end-to-end system with no coding. No experience is needed in any of those areas. The focus is not on the functionality of the bot (the dialog), but rather on setting up a simple system with client app, channel and bot service communicating with each other. When the services are deployed to an Azure region near you, it will demonstrate how quickly the user hears the Bot's spoken reply.

TODO: mention that everything can be done for free (free Azure account, free usage of TTS, STT...)

 To read more about Direct Line Speech channel and its usage in a commercial application look at [About custom voice-first virtual assistants](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/voice-first-virtual-assistants)

Here's what this tutorial covers:
> [!div class="checklist"]
> * Step-by-step instructions on creating the necessary Azure resources to depoly your bot 
> * Downloading the echo-bot sample code, building it and testing it locally
> * Deploying the bot to Azure and registering it with Direct Line Speech channel
> * Downloading Direct Line Speech client sample code and configuring it to communicate with your bot
> * Running the client application, using voice to communicate with your bot and examining the json Activity objects sent by the bot
> * Selecting a different text-to-speech voice
> * TODO: Custom wakeword


## Prerequisites

Let's review the hardware and software that you'll need for this tutorial.

- Windows 10 PC with a working microphone and speakers (or headphones)
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) or higher
- A [GitHub](https://github.com/) account
- [Git for Windows](https://git-scm.com/download/win) installed

## Select the Azure region for your bot deployment

TODO: This needs to change to reflect the free speech subscription (available only in one region?)

The Windows application you will run connects to Direct Line Speech channel (an Azure web service). The channel connects to the bot service you deploy to Azure. To reduce the overall round-trip time for a response from your bot, it's best to have these two services located in the same Azure region, and one which is closest to your location.

See the section titled ["voice-first virtual assistant"](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/regions#voice-first-virtual-assistants) for the list of regions supported by Direct Line Speech channel. You will need to pick one region, closest to you. This will also be the region you will deploy your bot. Exact location associated with Azure region
[can be found here](https://azure.microsoft.com/en-us/global-infrastructure/locations/).

One caveats: Direct Line Speech channel uses Text-to-Speech, which has "Standard Voices" and "Neural Voices". If you're interested in the higher quality Neural Voices, make sure you also look at the [Azure regions where Neural Voices is supported](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)

<!-- Discuss this: As noted in the next section, you will need to create a Cognitive Service Speech resource on Azure. If you choose the free trial subscription, it is only available in the "westus" region. -->

From this point on, we will assume the chosen region is "West US" <!-- which also support Neural Voices :-( -->

## Create an Azure Account 

You will need an Azure account to deploy your echo-bot, if you don't already have one. Follow the instructions in the section titled ["New Azure account"](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started#new-azure-account).

## Create an Azure Resource Group

<!-- note the text describing what a Resource Group is was taken from the Azure portal when you create a new Resource Group-->
You will need to create an Azure Resource Group if you don't already have one. Resource Group is a container that holds related resources for an Azure solution. The resource group can include all the resources for the solution, or only those resources that you want to manage as a group. You decide how you want to allocate resources to resource groups based on what makes the most sense for you ([learn more]([https://docs.microsoft.com/en-us/azure/azure-resource-manager/resource-group-overview#resource-groups)). In this tutorial, we will create a Resource Group named "SpeechEchoBotTutorial-ResourceGroup" and put all the resources related to the tutorial in this group. This will allow easy clean-up at the end by deleting the Resource Group. 
- In the [Azure portal](https://portal.azure.com), select "Resource groups" in the left-side menu.
- Click on "Add" to create a new Resource Group
- In "Project details", 
    - leave "Subscription" as "Free Trial"
    - in "Resource group" enter "SpeechEchoBotTutorial-ResourceGroup"  
- In "Region", open the drop-down list and select "(US) West US" (or another [Azure region of your choice](#Select-the-Azure-region-for-your-bot-deployment))
- Click on "Review + create". You should see a green "Validation passed" banner at the top.
- Click on "Create". 
- It may take a minute to create the group. Click on "Refresh" and you should see the new group. 

For this new resource, as well as all others created below, it's good to "Pin to dashboard" to make it easy to find by clicking "Dashboard" in the left-side menu in the Azure portal.

## Create an Azure Cognitive-Services Speech resource

You now need to create an Azure Speech resource. It is needed to enable the Speech-To-Text and Text-to-Speech functionality in the Direct Line speech channel. Follow the instruction in [Create a Speech resource in Azure](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure). For consistency, select the following for the Speech resource:
- Name it "SpeechEchoBotTutorial-Speech"
- Location should be "(US) West US" <!-- internal link to section: (or another [Azure region of your choice](#Select-the-Azure-region-for-your-bot-deployment)) -->
- Pricing tier - F0
- Resource group - Select the existing one created above (SpeechEchoBotTutorial-ResourceGroup).
Make sure you know how to find the two subscription keys associates with your new Speech resource -- you will need one of them further down. You can always come back and look at them again if needed.

At this point, check that your Resource Group "SpeechEchoBotTutorial-ResourceGroup" has one resource:

| NAME | TYPE  | LOCATION |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | App Service plan | West US |

## Create an Azure App Service Plan

The next step is to create an App Service Plan. See [Azure App Service plan overview](https://docs.microsoft.com/en-us/azure/app-service/overview-hosting-plans).

- In the [Azure portal](https://portal.azure.com), click on "Create a resource" 
- Type "app service" in the search bar, and select the "App Service plan" option
- In "Project Details"
    - Subscription: Keep the default selection of "Free Trail"
    - Resource Group: Select SpeechEchoBotTutorial-ResourceGroup from the drop-down list 
- In the "App Service Plan details:
    - Name: enter "SpeechEchoBotTutorial-AppServicePlan"
    - Operating System: Select "Windows"
    - Region: Select "West US" 
- Pricing Tier: Leave the default selection ("Standard S1)"
- Press "Review and create"
- Verify your selection and press "Create"

At this point, check that your Resource Group "SpeechEchoBotTutorial-ResourceGroup" has two resource:

| NAME | TYPE  | LOCATION |
|------|-------|----------|
| SpeechEchoBotTutorial-AppServicePlan | App Service plan | West US |
| SpeechEchoBotTutorial-Speech | Cognitive Services | West US |

<!-- When I deployed to West US 2 I got an error "The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option". I had to change everything to West US. But Neural TTS is not available there... -->

## Get the echo-bot sample from GitHub, build and deploy it to Azure

The echo-bot is a very simple bot that replies back echoing the text it got as input. That is all we need to demonstrated how a deployed bot can listen and speak to you. There is a custom version of the echo-bot that is already configured to work with Direct Line Speech channel, and no code changes will be needed on your part. Find it in the experimental folder in the [BotBuilder-Samples GitHub repo](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot). 

Follow the instructions in the [REAME.md file](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/README.md) to achieve the following:
- Clone the BotBuilder-samples repo
- Build EchoBot.cproj in the folder *experimental\directline-speech\csharp_dotnetcore*
- Deploy it locally and test it using Bot-Framework Emulator 

## Deploy your echo-bot to a new Azure App Service

The next step is deploying your bot to Azure. This can be done using Azure Command-Line Interface (CLI) as [described here](https://docs.microsoft.com/en-us/azure/bot-service/bot-builder-deploy-az-cli), using [deployment templates](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/DeploymentTemplates). If you are new to Azure, you may find it more educational to use Visual Studio's "Publish" feature and do it manually: 
- Open the project experimental\directline-speech\csharp_dotnetcore\02.echo-bot\EchoBot.csproj in Visual Studio
- In the Solution Explorer, right click the "EoBot" solution and select "Publish"
- A new window titled "Pick a publish target" will open, with a default selection of "App Service", and "Create New" Azure App Service
- Click on "Publish".
- In the "Create App Service" window:
    - Click "Add an account", and sign in with your Azure account credentials. If you're already signed in, select the account you want from the drop-down list.
    - In the "App Name" you will need to enter a globally unique app name for your Bot. This will determine your unique bot URL. A default value of "EchoBot##############" (where '#' are digits) will be populated from your current clock time. You can leave the default or change it. When you enter your app name, there will check to see if it is globally unique. In this tutorial, we will assume the app name is **SpeechEchoBotTutorial**, and use that from now on. It is likley that this name will be taken by the time you do this tutorial.
    - In "Subscription" leave the default "Free Trail"
    - In "Resource Group" select "SpeechEchoBotTutorial-ResourceGroup" (it should be the default value if that is the only Azure resource group you have)
    - In "Hosting Plan" select "SpeechEchoBotTutorial-AppServicePlan" (it will be the default if that's the only app service plan you have)
- Click on "Create"

If all goes well, the Azure App Service will be created and the bot will be deployed. You should see a success message similar to this one in the Visual Studio
output window:
```
Publish Succeeded.
Web App was published successfully https://speechechobottutorial.azurewebsites.net/
```

Click on the URL to open it in your default browser. You should see a webpage with "Your bot is ready!".

At this point, check your Resource Group "SpeechEchoBotTutorial-ResourceGroup" in the Azure portal. It should now show three resource:

| NAME | TYPE  | LOCATION |
|------|-------|----------|
| SpeechEchoBotTutorial | App Service | West US |
| SpeechEchoBotTutorial-AppServicePlan | App Service plan | West US |
| SpeechEchoBotTutorial-Speech | Cognitive Services | West US |

<!-- TOOD: This anchor does not work.Why -->
<!-- <a name="ToggleWebSocket"/> -->

At this point one small configuration change is needed to your newly created App Service (SpeechEchoBotTutorial), so your bot can communicate with Direct Line Speech channel using websocket protocol:
- In the [Azure portal](https://portal.azure.com), go to your new app service (named "SpeechEchoBotTutorial")
- On the left-side menu, under "Settings", click "Configuration"
- Switch to the "General settings" tab
- Toggle the "Web sockets" to On (by default it is Off)
- Click Save.

Note the controls at the top of your Azure App Service page. You can stop or restart a running App Service. Restarting may come in handy as part of troubleshooting. 

## Create an Azure Bot Channels Registration 

You have created an Azure App Service hosting your Bot. Now you need to register it with Direct Line Speech channel. Bot-Framework channel registration and configuration is done using the Azure Resource named "Bot Channels Registration". Create one now:
- In the [Azure portal](https://portal.azure.com), click on "Create a resource"
- In the search bar type "bot", and select the option "Bot Channels Registration".
- Click on "Create"
- Fill in the details
    - Bot name - Enter a name for this Azure resource. To keep up with the naming convention in the Tutorial enter "SpeechEchoBotTutorial-BotRegistration" (note the name must be between 4 and 42 characters)
    - Subscription - Leave the default "Free Trail"
    - Resource group - Select "SpeechEchoBotTutorial-ResourceGroup" 
    - Location - Enter "West US"
    - Pricing tier - Select F0 
    - Messaging endpoint - This is the URL where your bot will receive messages from the channel. Enter the URL of your web app with the /api/messages path appended at the end: https://speechechobottutorial.azurewebsites.net/api/messages/
    - Application insights - you can turn this off for now
    - Ignore "Auto create App ID and password".
    - Back in the "Bot Channels Registration" blade, click "Create" at the bottom.

At this point, check your Resource Group "SpeechEchoBotTutorial-ResourceGroup" in the Azure portal. It should now show four resource:

| NAME | TYPE  | LOCATION |
|------|-------|----------|
| SpeechEchoBotTutorial | App Service | West US |
| SpeechEchoBotTutorial-AppServicePlan | App Service plan | West US |
| SpeechEchoBotTutorial-BotRegistration | Bot Channels Registration | global
| SpeechEchoBotTutorial-Speech | Cognitive Services | West US |

Note: it wil say "global" under Location for your Bot Channels Registration, even though you selected "West US" when creating it.

TODO: Why?

<!-- TODO: This anchor does not work. Why? -->
<!-- <a name="BotChannelRegistration"/> -->
## Register your bot with Direct Line Speech channel

- In the [Azure portal](https://portal.azure.com), open your SpeechEchoBotTutorial-BotRegistration resource.
- Click on "Channels" on the left-side menu. 
    - Find the "Direct Line Speech" channel under the "More channels" options and click on it.
    - Read the text in the "Configure Direct line Speech (Preview)" page and click "Save"
    - Two "Secret keys" were generated for you. These keys are unique to your bot. When you write a client application using the [Speech SDK](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/), you will provide one of those keys in order to establish a connection between the client application, Direct Line Speech channel, and your bot service. In this tutorial we use the C# WPF sample code "Direct Line Speech Client" as the client. It uses the Speech SDK, and one of those keys will be needed in order to configure it.
    - Click "Show" and copy one of those keys. You can always come back and look at them again if needed.  
- Click on "Settings" on the left-side menu
    - Check the box titled "Enable Streaming Endpoint". This is needed to enable the websocket protocol between the bot and Direct Line Speech channel
    - Click "Save"

See also [Connect a bot to Direct Line Speech (Preview)](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0)

## Test your echo-bot with Direct Line Speech Client

Direct Line Speech Client is a Windows Presentation Foundation (WPF) application in C# that makes it easy to test interactions with your bot before creating a custom client application. It demonstrates how to use the [Azure Speech Services Speech SDK](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk) to manage communication with your Azure Bot-Framework bot. It has simple UI for configuration, viewing Bot-Framework Activities received from the Bot, Adaptive Cards and custome wakeword support. Here you wil use it to test the echo-bot you just deployed.
- Before you start, make sure your microphone and speakers (or headpones) are working properly and set to a good level (Windows Settings --> System --> Sound)
- Got to the [GitHub repository of Direct Line Speech Client](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client) and read the text in the front page ([README.md](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/README.md))
- Follow the [Quickstart](https://github.com/Azure-Samples/Cognitive-Services-Direct-Line-Speech-Client/blob/master/README.md#quickstart) there to:
    - Clone the repo
    - Build the project in Visual Studio
    - Run the executable DLSpeechClient.exe
    - Configure the settings page with 
        - The speech subscription key that you received while creating the Azure Cognitive-Services Speech resource
        - The Azure region associated with the speech subscription key. Use the "Speech SDK Parameter" format, as indicated in the [Speech SDK region table](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/regions#speech-to-text-text-to-speech-and-translation). For West US, enter "westus"
    - In the top of the main application window, enter the "Bot Secret". This is one of two secret keys you got when you registered your bot with Direct Line Speech channel. 
       
Click on "Reconnect" and make sure you see the message "Press the mic button, or type to start talking to your bot" at the bottom bar on the Window.
Press the microphone and say a few words in English. The recognized text will appear at the bottom bar as you speak, and after you are done, the bot will reply with the same words, prefixed with "echo". You also have the option to type text to communicate with the bot.

### Troubleshooting errors in Direct Line Speech Client

Error messages will be shown in red in the main application window.

| Error | What should you do? |
|-------|----------------------|
|App Error (see log for details): Microsoft.CognitiveServices.Speech.csharp : Value cannot be null. Parameter name: speechConfig | This is a client application error. Make sure you have a non-empty value for *Bot Secret* in the main app window | 
|Error AuthenticationFailure : WebSocket Upgrade failed with an authentication error (401). Please check for correct subscription key (or authorization token) and region name| In the Settings page of the application, make sure you entered the Speech Subscription key and its region correctly.<br>Make sure your Bot Secret was entered correctly. |
|Error ConnectionFailure : Connection was closed by the remote host. Error code: 1011. Error details: We could not connect to the bot before sending a message | Make sure you [checked the "Enable Streaming Endpoint"](#BotChannelRegistration) box and/or [toggled "Web sockets"](#ToggleWebSocket) to On.<br>Make sure your Azure App Service is running. If it is, try restarting your App Service.|
|Error ConnectionFailure : Connection was closed by the remote host. Error code: 1011. Error details: Response status code does not indicate success: 500 (InternalServerError)| Your bot specified a Neural Voice in its output Activity [Speak](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) field, but the Azure region associated with your Speech subscription key does not support Neural Voices. See [Standard and neural voices](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/regions#standard-and-neural-voices).|
|Error ConnectionFailure : Connection was closed by the remote host. Error code: 1000. Error details: Exceeded maximum websocket connection idle duration(> 300000ms)| This is an expected error when the client left the connection to the channel open for more than 5 minutes without any activity |

See also [Voice-first virtual assistants Preview: Frequently asked questions](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/faq-voice-first-virtual-assistants)

### Seeing Bot-Framework Activities

In the "Activity Log" window of the Direct Line Speech client application (top right region in the main Window) you will see a time-stamped log of Activities the application received from the bot. Selecting one of them will show the associated Activity in its JSON format. For example, below is the echo-bot's Activity reply to the input "Hello and welcome". Read about the different [fields in the Activity](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md). In particual, notice the [Text](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text)  and [Speak](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak) fields.
```json
{
    "attachments":[],
    "channelData":{
        "conversationalAiData":{
             "requestInfo":{
                 "interactionId":"8d5cb416-73c3-476b-95fd-9358cbfaebfa",
                 "version":"0.2"
             }
         }
    },
    "channelId":"directlinespeech",
    "conversation":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79",
        "isGroup":false
    },
    "entities":[],
    "from":{
        "id":"SpeechEchoBotTutorial-BotRegistration"
    },
    "id":"89841b4d-46ce-42de-9960-4fe4070c70cc",
    "inputHint":"acceptingInput",
    "recipient":{
        "id":"129ebffe-772b-47f0-9812-7c5bfd4aca79|0000"
    },
    "replyToId":"67c823b4-4c7a-4828-9d6e-0b84fd052869",
    "serviceUrl":"urn:botframework:websocket:directlinespeech",
    "speak":"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'><voice name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>Echo: Hello and welcome.</voice></speak>",
    "text":"Echo: Hello and welcome.",
    "timestamp":"2019-07-19T20:03:51.1939097Z",
    "type":"message"
}
```

## What made the bot speak?

In your clone of the BotBuilder-Samples repo, open the file *\experimental\directline-speech\csharp_dotnetcore\02.echo-bot\Bots\echo-bot.cs* ([GitHub link](https://github.com/microsoft/BotBuilder-Samples/blob/master/experimental/directline-speech/csharp_dotnetcore/02.echo-bot/Bots/EchoBot.cs))

The method *OnMessageActivityAsync* handles the response of the bot to input text (user-typed or recognized speech). It uses a helper method *Speak* to populate both the *[Text](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#text)* and *[Speak](https://github.com/microsoft/botframework-sdk/blob/master/specs/botframework-activity/botframework-activity.md#speak)* fields of the returned Activity object. Spoken text is described using [Speech Synthesis Markup Language](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/speech-synthesis-markup) (SSML): 
 ```csharp
             string body = @"<speak version='1.0' xmlns='https://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
              <voice name='Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)'>" +
              $"{message}" + "</voice></speak>";
 ```
You can recognize the string <b>Microsoft Server Speech Text to Speech Voice (en-US, JessaRUS)</b> as one of the [standard Text-to-speech voices](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/language-support#standard-voices). Experiment by changing the SSML:

TODO: example of different voice, and possibly also change intonation, speed.. 

## Try another language (?)

TODO 

## Make a change to the Bot and redeploy it

TODO

## Try out the high-quality Neural Text-to-speech voices

TODO

## Add Custom Wakeword

TODO

## Debug your bot locally

TODO

## Clean up resources

If you're not going to continue using the echo-bot deployed in this tutorial, you can remove it and all its associated Azure resources by simply deleting the Azure Resource group SpeechEchoBotTutorial-ResourceGroup
1. In the [Azure portal](https://portal.azure.com), click on "Resource Groups" from the left-side menu 
1. Find the one named SpeechEchoBotTutorial-ResourceGroup, and click on the three dots (...) on the right side
1. Select "Delete resource group".

## Next steps

Build your own client application with the Speech SDK:
* [C# (UWP)](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/quickstart-virtual-assistant-csharp-uwp), [Java (Windows, macOS, Linux)](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/quickstart-virtual-assistant-java-jre), or [Java (Android)](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/quickstart-virtual-assistant-java-jre) Quickstart documentation
* [Speech SDK sample code](https://github.com/Azure-Samples/cognitive-services-speech-sdk#voice-first-virtual-assistants-quickstarts) for C#(UWP) and Java (Windows, macOS, Linux)

Build and deploy your own voice-enabled Bot:
* Build a [Bot-Framework Bot](https://dev.botframework.com/). Register it with [Direct Line Speech channel](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-channel-connect-directlinespeech?view=azure-bot-service-4.0) and [customize your bot for voice](https://docs.microsoft.com/en-us/azure/bot-service/directline-speech-bot?view=azure-bot-service-4.0). 
* Explore existing [Bot-Framework solutions](https://github.com/microsoft/botframework-solutions): Build a [custom voice-first virtual assistant](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/voice-first-virtual-assistants) and [voice-enable it](https://github.com/microsoft/botframework-solutions/blob/master/docs/howto/assistant/csharp/speechenablement.md).



