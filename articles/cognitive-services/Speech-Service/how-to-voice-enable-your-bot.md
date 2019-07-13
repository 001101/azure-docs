---
title: "Tutorial: Deploy a voice-enabled Bot using Direct Line Speech channel"
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

# Tutorial: Deploy a voice-enabled bot using Direct Line Speech channel 
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

This tutorial is targeted for developers who are just starting their journey with Bot-Framework bots, Direct Line Speech or Speech SDK, and want to quickly build a working end-to-end system with minimal coding. The focus is not on the functionality of the bot (the dialog), but rather on setting up a simple system with client app, channel and bot service communicating with each other. When configured correctly, it will demostrate the how quickly the user hears the Bot's spoken reply, the accuracy of speech recognition and the quality of Bot's voice (text-to-speech).

TODO: mention that everything can be done for free (free Azure account, free usage of TTS, STT...)

 To read more about Direct Line Speech channel and its usage in a commercial application look at [About custom voice-first virtual assistants](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/voice-first-virtual-assistants)

Here's what this tutorial covers:
> [!div class="checklist"]
> * Downloading the echo-bot sample code and modifying it to support voice output and Direct Line Speech channel
> * Deploying the bot to Azure and registering it with Direct Line Speech channel
> * Downloading Direct Line Speech client sample code and configuring it to communicate with your bot
> * Running the client application, using voice to communicate with your bot and examining the json Activity objects set by the bot
> * TODO: Custom wakeword



<!---Required:
The outline of the tutorial should be included in the beginning and at
the end of every tutorial. These will align to the **procedural** H2
headings for the activity. You do not need to include all H2 headings.
Leave out the prerequisites, clean-up resources and next steps--->


<!--- Required, if a free trial account exists
Because tutorials are intended to help new customers use the product or
service to complete a top task, include a link to a free trial before the
first H2, if one exists. You can find listed examples in
[Write tutorials](contribute-how-to-mvc-tutorial.md)
--->

<!---Avoid notes, tips, and important boxes. Readers tend to skip over
them. Better to put that info directly into the article text.--->

## Prerequisites

Let's review the software and subscription key that you'll need for this tutorial.

- [GitHub](https://github.com/) account
- [Git for Windows](https://git-scm.com/download/win)
- [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) or later
- An [Azure account with a Speech resource](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started#new-azure-account), in one of the supported [Azure regions](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/regions#voice-first-virtual-assistants) (see "Voice-first virtual assistant" regions)

<!---If you need them, make Prerequisites your first H2 in a tutorial. If
there’s something a customer needs to take care of before they start (for
example, creating a VM) it’s OK to link to that content before they
begin.--->

<!-- ## Sign in to <service/product/tool name> -->

<!-- Sign in to the [<service> portal](url). -->

<!---If you need to sign in to the portal to do the tutorial, this H2 and
link are required.--->

## Select the Azure region for your bot deployment

The Windows application you will run connects to Direct Line Speech channel (an Azure web service). The channel connects to the bot service you deploy to Azure. To reduce the overall round-trip time for a response from your bot, it's best to have these two services located in the same Azure region, and one which is closest to your location.

See the section titled ["voice-first virtual assistant"](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/regions#voice-first-virtual-assistants) for the list of regions supported by Direct Line Speech channel. You will need to pick one region, closest to you. This will also be the region you will deploy your bot. Exact location associated with Azure region
[can be found here](https://azure.microsoft.com/en-us/global-infrastructure/locations/).

One caveats: Direct Line Speech channel uses Text-to-Speech, which has "Standard Voices" and "Neural Voices". If you are interested in the higher quality Neural Voices, make sure you also look at the [Azure regions where Neural Voices is supported](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/regions#standard-and-neural-voices)

<!-- As noted in the next section, you will need to create a Cognitive Service Speech resource on Azure. If you choose the free trial subscription, it is only available in the "westus" region. -->

From this point on, we will assume the chosen region is "westus2" (which also support Neural Voices)

## Create an Azure Account with a Speech resource

You will need an Azure account to deploy your echo-bot, if you don't already have one. Follow the instructions in the section titled ["New Azure account"](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/get-started#new-azure-account).

## Create a Speech resource and get your speech subscription key

You will need to create an Azure Speech resource, in order to get a subscription key, used to handle the Speech-To-Text and Text-to-Speech functionality in the Direct Line speech channel.  

<!---Required:
Tutorials are prescriptive and guide the customer through an end-to-end
procedure. Make sure to use specific naming for setting up accounts and
configuring technology.
Don't link off to other content - include whatever the customer needs to
complete the scenario in the article. For example, if the customer needs
to set permissions, include the permissions they need to set, and the
specific settings in the tutorial procedure. Don't send the customer to
another article to read about it.
In a break from tradition, do not link to reference topics in the
procedural part of the tutorial when using cmdlets or code. Provide customers what they need to know in the tutorial to successfully complete
the tutorial.
For portal-based procedures, minimize bullets and numbering.
For the CLI or PowerShell based procedures, don't use bullets or
numbering.
--->

Include a sentence or two to explain only what is needed to complete the
procedure.

1. Step 1 of the procedure
1. Step 2 of the procedure
1. Step 3 of the procedure
   ![Browser](media/contribute-how-to-mvc-tutorial/browser.png)
   <!---Use screenshots but be judicious to maintain a reasonable length. 
   Make sure screenshots align to the
   [current standards](https://review.docs.microsoft.com/help/contribute/contribute-how-to-create-screenshot?branch=master).
   If users access your product/service via a web browser the first 
   screenshot should always include the full browser window in Chrome or
   Safari. This is to show users that the portal is browser-based - OS 
   and browser agnostic.--->
1. Step 4 of the procedure

## Procedure 2

Include a sentence or two to explain only what is needed to complete the procedure.

1. Step 1 of the procedure
1. Step 2 of the procedure
1. Step 3 of the procedure

## Procedure 3

Include a sentence or two to explain only what is needed to complete the
procedure.
<!---Code requires specific formatting. Here are a few useful examples of
commonly used code blocks. Make sure to use the interactive functionality
where possible.

For the CLI or PowerShell based procedures, don't use bullets or
numbering.
--->

Here is an example of a code block for Java:

```java
cluster = Cluster.build(new File("src/remote.yaml")).create();
...
client = cluster.connect();
```

or a code block for Azure CLI:

```azurecli-interactive 
az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --admin-username azureuser --admin-password myPassword12
```

or a code block for Azure PowerShell:

```azurepowershell-interactive
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -IpAddressType Public
```


## Clean up resources

If you're not going to continue to use this application, delete
<resources> with the following steps:

1. From the left-hand menu...
2. ...click Delete, type...and then click Delete

<!---Required:
To avoid any costs associated with following the tutorial procedure, a
Clean up resources (H2) should come just before Next steps (H2)
--->

## Next steps

Advance to the next article to learn how to create...
> [!div class="nextstepaction"]
> [Next steps button](contribute-get-started-mvc.md)

<!--- Required:
Tutorials should always have a Next steps H2 that points to the next
logical tutorial in a series, or, if there are no other tutorials, to
some other cool thing the customer can do. A single link in the blue box
format should direct the customer to the next article - and you can
shorten the title in the boxes if the original one doesn’t fit.
Do not use a "More info section" or a "Resources section" or a "See also
section". --->