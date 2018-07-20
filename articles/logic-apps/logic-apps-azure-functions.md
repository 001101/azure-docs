---
title: Add and run custom code in Azure Logic Apps with Azure Functions | Microsoft Docs
description: Learn how to add and run custom code snippets in Azure Logic Apps with Azure Functions
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.topic: article
ms.date: 07/20/18
ms.reviewer: klam, LADocs
ms.suite: integration
---

# Add and run custom code snippets in Azure Logic Apps with Azure Functions

When you want to create and run only enough code 
that addresses a specific problem in your logic apps, 
you can create your own functions by using 
[Azure Functions](../azure-functions/functions-overview.md). 
This service provides the capability for creating and running 
custom code snippets written with Node.js or C# in your logic 
apps without worrying about creating an entire app or the 
infrastructure for running your code. Azure Functions 
provides serverless computing in the cloud and is useful 
for performing tasks, such as these examples:

* Extend your logic app's behavior with functions supported by Node.js or C#.
* Perform calculations in your logic app workflow.
* Apply advanced formatting or compute fields in your logic apps.

You can also [call logic apps from inside Azure functions](#call-logic-app).

## Prerequisites

To follow this article, here are the items you need:

* If you don't have an Azure subscription yet, 
<a href="https://azure.microsoft.com/free/" target="_blank">sign up for a free Azure account</a>. 

* The logic app where you want to add the function

  If you're new to logic apps, review 
  [What is Azure Logic Apps](../logic-apps/logic-apps-overview.md) 
  and [Quickstart: Create your first logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* A [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) 
as the first step in your logic app 

  Before you can add actions for running functions, 
  your logic app must start with a trigger.

* An Azure function app, which is a container for Azure functions, 
and your Azure function. Your function app must belong to the same 
Azure subscription as your logic app. Also, when you include a Swagger 
description with your function, the Logic Apps Designer can provide 
a richer experience for you when working with function parameters.

  If you don't have a function app, you must 
  [create your function app first](../azure-functions/functions-create-first-azure-function.md). 
  You can then create your function either 
  [separately outside your logic app](#create-function-external), 
  or [from inside your logic app](#create-function-designer) 
  in the Logic App Designer.

<a name="create-function-external"></a>

## Create functions outside logic apps

In the <a href="https://portal.azure.com" target="_blank">Azure portal</a>, 
create your Azure function app, and then create your Azure function. 
If you're new to Azure Functions, learn how to 
[create your first function in the Azure portal](../azure-functions/functions-create-first-azure-function.md), 
but note these requirements for creating Azure functions 
that you can add and call from logic apps.

* Make sure you use one of these function templates, 
which can accept content that has `application/json` 
type from your logic app:

  * **Generic webhook - JavaScript** 
  * **Generic webhook - C#**

  These templates also help the Logic App Designer find 
  and show the custom functions that you create with these 
  templates when you add those functions to your logic apps.

  ![Generic webhook - JavaScript or C#](./media/logic-apps-azure-functions/generic-webhook.png)

* After you create your Azure function, check that the 
**Mode** and **Webhook type** properties are set correctly.

  1. In the **Function Apps** list, expand your function, 
  and select **Integrate**. 

  2. Check that your template's **Mode** property 
  is set to **Webhook** and that the **Webhook type** 
  property is set to **Generic JSON**. 

     ![Your function's "Integrate" properties](./media/logic-apps-azure-functions/function-integrate-properties.png)

### Access property values inside HTTP requests

Webhook functions can accept HTTP requests as inputs and pass those 
requests to other functions. For example, although Logic Apps has 
[functions that convert DateTime values](../logic-apps/workflow-definition-language-functions-reference.md), 
this basic sample JavaScript function shows how you can access a property 
inside a request object that's passed to the function and perform operations 
on that property value. To access properties inside objects, this example uses the 
[dot (.) operator](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Operators/Property_accessors): 

```javascript
function convertToDateString(request, response){
   var data = request.body;
   response = {
      body: data.date.ToDateString();
   }
}
```

Here's what happens inside this function:

1. The function creates a `data` variable and assigns the 
`body` object inside the `request` object to that variable. 
The function uses the dot (.) operator to reference the 
`body` object inside the `request` object: 

   ```javascript
   var data = request.body;
   ```

2. The function can now access the `date` property 
through the `data` variable, and convert that property 
value from DateTime type to DateString type by calling 
the `ToDateString()` function. The function also returns 
the result through the `body` property in the function's response: 

   ```javascript
   body: data.date.ToDateString();
   ```

Now that you've created your Azure function, 
follow the steps for how to 
[add functions to logic apps](#add-function-logic-app).

<a name="create-function-designer"></a>

## Create functions inside logic apps

Before you can create an Azure function starting 
from inside your logic app in the Logic App Designer, 
you must first have an Azure function app, 
which is a container for your functions. 
If you don't have a function app, 
create that function app first. See 
[Create your first function in the Azure portal](../azure-functions/functions-create-first-azure-function.md). 

1. In the <a href="https://portal.azure.com" target="_blank">Azure portal</a>, 
open your logic app in the Logic App Designer. 

2. Under the step where you want to create and add the function, 
choose **New step** > **Add an action**. 

3. In the search box, enter "azure functions" as your filter.
From the actions list, select this action: 
**Choose an Azure function - Azure Functions** 

   ![Find "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. From the function apps list, select your function app. 
After the functions list opens, select this action: 
**Create New Function  - Azure Functions**

   ![Select your function app](./media/logic-apps-azure-functions/select-function-app-create-function.png)

5. In the function definition editor, define your function:

   1. In the **Function name** box, 
   provide a name for your function. 

   2. In the **Code** box, replace the template code 
   with your function code, including the response 
   and payload you want returned to your logic app 
   after your function finishes running. 
   The `context` object in the template code 
   contains the message and content that your 
   logic app passes to your function, for example:

      ![Define your function](./media/logic-apps-azure-functions/function-definition.png)

   3. When you're done, choose **Create**.

6. In the **Request Body** box, specify the context object 
to pass as your function's input, which must be formatted 
in JavaScript Object Notation (JSON). 

   When you click in the **Request Body** box, 
   the dynamic content list opens so you can select 
   tokens for properties available from previous steps. 
   This example passes in the **From** property from the email trigger:  

   !["Request Body" example - context object payload](./media/logic-apps-azure-functions/function-request-body-example.png)

   Based on the content in the context object, 
   the Logic App Designer generates a function 
   template that you can then edit inline. 
   Logic Apps also creates variables based on 
   the input context object. Inside your function, 
   you can reference the content in the context object 
   by using this syntax, based on this example:

   ```text
   context.body.content
   ```

   In this example, the context object isn't cast as a string, 
   so the content gets directly added to the JSON payload. 
   However, if the object isn't a JSON token, that is, 
   either a string, a JSON object, or a JSON array, you get an error. 
   To cast the context object as a string, add double-quotation marks, 
   for example:

   ![Cast object as string](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

7. To specify other details such as the method to use, 
request headers, or query parameters, choose **Show advanced options**.

<a name="add-function-logic-app"></a>

## Add existing functions to logic apps

To call existing Azure functions from your logic apps, 
you can add Azure functions like any other action 
in the Logic App Designer. 

1. In the <a href="https://portal.azure.com" target="_blank">Azure portal</a>, 
open your logic app in the Logic App Designer. 

2. Under the step where you want to add the function, 
choose **New step** > **Add an action**. 

3. In the search box, enter "azure functions" as your filter.
From the actions list, select this action: 
**Choose an Azure function - Azure Functions** 

   ![Find "Azure functions"](./media/logic-apps-azure-functions/find-azure-functions-action.png)

4. From the function apps list, select your function app. 
After the functions list appears, select your function: 

   ![Select your function app and Azure function](./media/logic-apps-azure-functions/select-function-app-existing-function.png)

5. In the **Request Body** box, specify the context object 
to pass as your function's input, which must be formatted 
in JavaScript Object Notation (JSON). This context object 
contains the message and content that your logic app 
sends to your function. 

   !["Request Body" example - context object payload](./media/logic-apps-azure-functions/function-request-body-example.png)

   Based on the content in the context object, 
   the Logic App Designer generates a function 
   template that you can then edit inline. 
   Logic Apps also creates variables based on 
   the input context object. Inside your function, 
   you can reference the content in the context object 
   by using this syntax, based on this example:

   ```text
   context.body.content
   ```

   In this example, the context object isn't cast as a string, 
   so the content gets directly added to the JSON payload. 
   However, if the object isn't a JSON token, that is, 
   either a string, a JSON object, or a JSON array, you get an error. 
   To cast the context object as a string, add double-quotation marks, 
   for example:

   ![Cast object as string](./media/logic-apps-azure-functions/function-request-body-string-cast-example.png)

6. To specify other details such as the method to use, 
request headers, or query parameters, choose **Show advanced options**.

<a name="call-logic-app"></a>

## Call logic apps from functions

To trigger a logic app from inside an Azure function, 
that logic app must have a callable endpoint, 
or more specifically, a **Request** trigger. 
Then, from inside your function, send an HTTP POST 
request to the URL for that **Request** trigger and 
include the payload you want that logic app to process. 
For more information, see [Call, trigger, or nest logic apps](../logic-apps/logic-apps-http-endpoint.md). 

## Get support

* For questions, visit the [Azure Logic Apps forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* To submit or vote on feature ideas, visit the [Logic Apps user feedback site](http://aka.ms/logicapps-wish).

## Next steps

* Learn about [Logic Apps connectors](../connectors/apis-list.md)
