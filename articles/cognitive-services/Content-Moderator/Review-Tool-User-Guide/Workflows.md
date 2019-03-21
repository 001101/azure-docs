---
title: Define and use content moderation workflows - Content Moderator
titlesuffix: Azure Cognitive Services
description: You can use the Azure Content Moderator workflow designer and APIs to define custom workflows and thresholds based on your content policies.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/12/2019
ms.author: sajagtap

---

# Define, test, and use moderation workflows

Content workflows are cloud-based customized filters that you can use to handle content more efficiently. Workflows can connect to a variety of services to filter content in different ways. This guide shows you how to use the Content Moderator connector (which is included by default) to filter content and set up human reviews in a typical moderation scenario.

## Create a new workflow

Go to the [Content Moderator Review tool](https://contentmoderator.cognitive.microsoft.com/) and sign in. On the **Settings** tab, select **Workflows**.

![Workflows setting](images/2-workflows-0.png)

On the next screen, select **Add Workflow**.

![Add a workflow](images/2-workflows-1.png)

### Assign a name and description

Name your workflow, enter a description, and choose whether the workflow will handle images or text.

![Workflow name and description](images/image-workflow-create.PNG)

### Define evaluation criteria

On the next screen, go to the **If** section. In the top dropdown menu, choose **Condition**. This will allow you to configure the condition on which the workflow will take action. If you want to use multiple conditions, choose **Combination** instead. 

Next, select a connector. This example uses **Content Moderator**. Depending on the connector you choose, you will get different options for data output.

![Select workflow connector](images/image-workflow-connect-to.PNG)

Choose the desired output to use and set the conditions to check it against.

![Define workflow condition](images/image-workflow-condition.PNG)

### Define the action

Go to the **Then** section, where you select an action. The following example creates an image review and assigns a tag. Optionally, you can add an alternative (Else) path and set an action for that as well.

![Define workflow action](images/image-workflow-action.PNG)

### Save the workflow

Note the workflow name; you need the name to start a moderation job with the Workflow API (see below). Finally, save the workflow using the **Save** button at the top of the page.

## Test the workflow

Now that you have defined a custom workflow, test it with sample content. Go to **Workflows** and select the corresponding **Execute Workflow** button.

![Workflow test](images/image-workflow-execute.PNG)

Save this [sample image](https://moderatorsampleimages.blob.core.windows.net/samples/sample3.png) to your local drive. Then select **Choose File(s)** and upload the image to the workflow.

![a woman in a bathing suit](images/sample-racy.PNG)

### Track the workflow

You can view the progress of the workflow in the next popup window.

![Track workflow execution](images/image-workflow-job.PNG)

### Verify workflow action

Go to the **Image** tab under **Review** and verify that there is a newly created image review.

![Review images](images/image-workflow-review.PNG)

## Use the Workflow API

The Workflow APIs provide a programming interface for the workflow capabilities. You can create workflows, get workflow details, and update workflow definitions programmatically using the Workflow API.

### Create or update workflow programmatically

The [Workflow - Create or Update](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) operation accepts the following inputs:

- **team**: The team ID that you created when you set up your [Review tool](https://contentmoderator.cognitive.microsoft.com/) account.
- **workflowname**: The name of your workflow. Use a new name to create a new workflow.
- **Ocp-Apim-Subscription-Key**: Your Content Moderator key (located on the **Settings** tab).

If the operation succeeds, the **Response status** is `200 OK` and the **Response content** box displays `true`.

### Get workflow details

The **Workflow-Get** operation accepts the following inputs:

- **team**: The team ID that you created when you set up your [Review tool](https://contentmoderator.cognitive.microsoft.com/) account.
- **workflowname**: The name of your workflow.
- **Ocp-Apim-Subscription-Key**: Your Content Moderator key (located on the **Settings** tab).

If the operation succeeds, the **Response status** is `200 OK` and the **Response content** box displays the workflow definition in JSON format.

## Next steps

See the Jobs guide [Workflow API console](../try-review-api-job.md) to test the workflow APIs in the web console.

To invoke the workflow from code, use custom workflows with the [`Job` API console quickstart](../try-review-api-job.md) and the [.NET SDK quickstart](../moderation-jobs-quickstart-dotnet.md).
