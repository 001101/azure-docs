---
title: Integrate Power Virtual Agent - QnA Maker
description: Improve the quality of your knowledge base with active learning. Review, accept or reject, add without removing or changing existing questions.
ms.topic: conceptual
ms.date: 03/11/2020
---

# How to add a QnA Maker knowledge base to Power Virtual Agent

Extend your [Power Virtual Agent](https://powervirtualagents.microsoft.com/) bot to provide answers from your knowledge base. You can configure your bot topic _conversation path_ to send the user's text, as the _trigger phrase_, to your knowledge base, then return the answer to your user shown in a message.

## Use QnA Maker knowledge base with a Power Virtual Agent

QnA Maker is a cloud-based API service that lets you create a conversational question-and-answer layer over your existing data. Use it to build a knowledge base by extracting questions and answers from your semi-structured content, including FAQs, manuals, and documents. Answer questions from the QnA sets in your knowledge base. Your knowledge base gets smarter, too, as it continually learns from user behavior.

## Power Virtual agent

A Power Virtual Agent is created with a series of topics (subject areas), in order to answer user questions by performing actions. If an answer can't be found, a system fallback can return an answer.

You can send the question to your knowledge base as part of a topic's action or as part of the System Fallback topic path. They both use the same mechanism of an action to connect to your knowledge base and return an answer.

## Power Automate connects to GenerateAnswer action

To connect your agent to your knowledge base, use Power Automate to create the action. Power Automate provides a process **flow**, which connects to QnA Maker's GenerateAnswer API.

Once the **flow** is designed and saved, it is available from a Power Automate **Solution**.  Once the GenerateAnswer flow is added to a solution, use that solution as an action in your agent.

## Process steps to connect an agent to your knowledge base

Steps to use a Power Virtual agent with QnA Maker:
* In [QnA Maker](https://www.qnamaker.ai/) portal
    * Build and publish knowledge base
    * Copy knowledge base details including knowledge base ID, runtime endpoint key, and runtime endpoint host.
* In [Power Virtual Agent](https://powerva.microsoft.com/) portal
    * Build agent topic
    * Call an action (to Power Automate Flow)
* In [Power Automate](https://us.flow.microsoft.com/) portal
    * Build a flow with connector to [QnA
    Maker's GenerateAnswer](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)
        * QnA Maker published knowledge base information
            * Knowledge base ID
            * QnA Maker resource endpoint host
            * QnA Maker resource endpoint key
        * Input - user query
        * Output - knowledge base answer
    * Create solution and add flow
* Return to Power Virtual Agent
    * Select solution's output as message for topic

## Get your published knowledge base information

Before you build your agent, create and publish your knowledge base. Next, find the endpoint key, endpoint host, and knowledge base ID on the **Settings** page in the QnA Maker portal.

You will need this information for the [Power Automate step](#create-power-automate-flow-to-connect-to-your-knowledge-base) to configure your QnA Maker GenerateAnswer connection.

## Create Power Virtual Agent's System fallback topic

The first step is to configure an agent topic. While it can be any topic, this procedure uses the System **Fallback** topic. If the agent can't find an answer, it should pass the answer to QnA Maker's GenerateAnswer API.

1. In the [Power Virtual Agents](https://powerva.microsoft.com/#/) portal, on the top-right corner of the navigation, select the **Settings** page. The icon for this page is the gear. Select **System Fallback**.

    > [!div class="mx-imgBorder"]
    > ![Power Virtual agent menu item for System Fallback](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. On the pop-up **Settings** window, select **+ Add** to add a System Fallback topic.

    > [!div class="mx-imgBorder"]
    > ![On Settings window, add fallback topic.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. After the topic is added, select **Go to Fallback topic** to author the Fallback topic on the authoring canvas.

    > [!TIP]
    > If you need to return to the Fallback topic, it is available in the **Topics** section, as part of the **System** topics.

## Use authoring canvas to add an action

Use the Power Virtual Agent's authoring canvas to connect the Fallback topic to your knowledge base. The topic starts with the **unrecognized user text**. You need to add an action that passes that text to QnA Maker, then shows the answer as a message. The last step of displaying an answer is handled as a separate step later.

1. The new Fallback action may already have conversation flow elements. Delete all items in the flow except for the first item, **Trigger Phrases**, with the `Unrecognized user input`.

    > [!div class="mx-imgBorder"]
    > ![Start fallback action with trigger phrases](../media/how-to-integrate-power-virtual-agent/fallback-action-start-trigger-phrases.png)

1. Select the **+** connector flowing from the **Trigger Phrases** box, then select **Call an action**.

    > [!div class="mx-imgBorder"]
    > ![Call an action](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Select **Create a flow**. The process takes you to **Power Automate**, a different browser-based portal.

    > [!div class="mx-imgBorder"]
    > ![Call an action](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## Create Power Automate Flow to connect to your knowledge base

The following procedure creates an action that:
* takes the incoming user text
* sends it to QnA Maker
* assigns the QnA Maker response to a variable
* sends only the top answer as the response back to your agent

1. In **Power Automate**, the **Flow Template** is started. On the **Power Virtual Agents** item, select **Edit** to configure the input variable coming from your agent to your knowledge base. The text-based input variable is the user-submitted text question from your agent.

    > [!div class="mx-imgBorder"]
    > ![Configure your input variable as a text string](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Add a text input and name the variable `InputText` with a description of `IncomingUserQuestion`. This naming helps distinguish the input text from the output text you create later.

    > [!div class="mx-imgBorder"]
    > ![Add a text input and name the variable `InputText` with a description of `UserQuestion`](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Select the **+** connector flowing from the **Power Virtual Agents** box, to insert a new step in the flow (before the **Return value(s) to Power Virtual Agent**), then select **Add an action**.

1. Search for `Qna` to find the **QnA Maker** actions, then select **Generate answer**.

    > [!div class="mx-imgBorder"]
    > ![Search for `Qna` to find the **QnA Maker** actions, then select **Generate answer**](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    The connections settings for QnA Maker appear in the action.

    > [!div class="mx-imgBorder"]
    > ![The connections settings for QnA Maker appear in the action.](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Configure the action with your knowledge base ID, endpoint host and endpoint key. These are found on the **Settings** page of your knowledge base, in the QnA Maker portal.

    > [!div class="mx-imgBorder"]
    > ![Enter your published knowledge base settings found on the **Settings** page in the [QnA Maker](https://www.qnamaker.ai/) portal.](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. To configure the **Question**, select the text box, then select the  `InputText` from the list.

1. Select the **+** connector flowing from the **Generate answer** action box to insert a new step in the flow, then select **Add an action**.

1. To add a variable to capture the answer text returned from GenerateAnswer, search for and select the `Initialize variable` action.

    Set the name of the variable to `OutgoingQnAAnswer`, and select the type as **String**. Don't set the **Value**.

    > [!div class="mx-imgBorder"]
    > ![Set the name of the variable to `QnAAnswer`, and select the type as **String**](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Select the **+** connector flowing from the **Initialize variable** action box to insert a new step in the flow, then select **Add an action**.

1. To set the knowledge base answer to the variable, search for and select the`Apply to each` action.

    Select the GenerateAnswer `answers`, which is the entire JSON from the GenerateAnswer API call. In the same **Apply to each** box, select **Add an action**. This subsequent action gets only the top answer and sets that to the variable.

1. In the **Set variable** box, select the text box for **Name**, then select **OutgoingQnAAnswer** from the list.

    Select the text box for **Value**, then select **Answers Answer** from the list.

    > [!div class="mx-imgBorder"]
    > ![Set the name and value for the variable ](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. To return the variable (and its value), select the **Return value(s) to Power Virtual Agent** then select **Add an output**. Select a **Text** output type then enter the **Title** of `FinalAnswer`. Select the text box for the **Value**, then select the `OutgoingQnAAnswer` variable.

    > [!div class="mx-imgBorder"]
    > ![Set the return value](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. Select **Save** to save the Flow.

## Create solution and add flow

In order for the Power Virtual Agent to find and connect to the flow, the flow must be included in a Power Automate Solution.

1. While still in Power Automate, select **Solutions** from the left-side navigation.

1. Select **+ New solution** and configure the solution. Select **Create** to finish the process.

    > [!div class="mx-imgBorder"]
    > ![Create new solution](../media/how-to-integrate-power-virtual-agent/power-automate-create-new-solution.png)

1. In the list of solutions, select the solution you just created. It should be at the top of the list.

1. In the solution, select **+ Add existing**, then select **Flow** from the list.

1. Select the **Outside solutions** tab, then select the flow you just created. Select **Add** to finish the process.

    > [!div class="mx-imgBorder"]
    > ![Add flow to solution](../media/how-to-integrate-power-virtual-agent/power-automate-add-flow-to-solution.png)

## Add solution's flow to Power Virtual Agent

1. Return to the browser tab with your Power Virtual Agent. The authoring canvas should still be open.

1. Select the **+** connector under the **Trigger Phrases** action box to insert a new step in the flow, then select **Call an action**.

    > [!div class="mx-imgBorder"]
    > ![Call an action from the authoring canvas](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-call-an-action.png)

1. In the new action, select the input value of **UnrecognizedTriggerPhrase**.

    > [!div class="mx-imgBorder"]
    > ![In the new action, select the input value of **UnrecognizedTriggerPhrase**.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Select the **+** connector under the **Action** box to insert a new step in the flow, then select **Show a message**.

1. Enter the message text and the `FinalAnswer` from the Power Automate Flow.

    > [!div class="mx-imgBorder"]
    > ![Enter the message text and the `FinalAnswer` from the Power Automate Flow.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. Select **Save** from the context toolbar to save the authoring canvas details for the topic.

The final canvas is shown below.

> [!div class="mx-imgBorder"]
> ![Final agent canvas](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## Test Power Virtual Agent

Test the agent by typing in a question that your knowledge base can answer. The authoring canvas reports the successful steps with a green check mark.

> [!div class="mx-imgBorder"]
> [![Test you Power Virtual Agent with a query meant for your knowledge base.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test.png)](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test.png#lightbox)

## Next step

[Get analytics on your knowledge base](get-analytics-knowledge-base.md)

Learn more about:
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [QnA Maker connector](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) and the [settings for the connector](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)