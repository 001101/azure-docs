---
title: Overview - Automate logic app deployment with Azure Resource Manager templates - Azure Logic Apps
description: Overview for automating deployment for logic apps with Azure Resource Manager templates
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/15/2019
---

# Overview: Automate deployment for logic apps with Azure Resource Manager templates

When you're ready to automate how you create and deploy your logic app, you can expand your logic app's underlying workflow definition into an [Azure Resource Manager template](../azure-resource-manager/resource-group-overview.md). This template defines the infrastructure, resources, parameters, and other setup information for provisioning and deploying your logic app. By using Resource Manager templates, you can repeatedly and consistently deploy logic apps to any environment, Azure subscription, and Azure resource group that you want.

For example, if you're deploying to a development, test, and production environment, you're likely using different connection strings in each environment. You can provide these connection strings for your template by creating a [parameter file](../azure-resource-manager/resource-group-template-deploy.md#parameter-files).

This article provides an overview about the high-level structure and syntax for a Resource Manager template compared to a logic app's underlying workflow definition. Both the template and your workflow definition use JSON but with some differences because the workflow definition also follows the [Workflow Definition Language schema](../logic-apps/logic-apps-workflow-definition-language.md).

For general information about Resource Manager templates, see these topics:

* [Azure Resource Manager template structure and syntax](../azure-resource-manager/resource-group-authoring-templates.md)
* [Azure Resource Manager template best practices](../azure-resource-manager/template-best-practices.md)

For a sample prebuilt template for a basic logic app, see [GitHub - azure-quickstart-templates/101-logic-app-create](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create).

<a name="template-structure"></a>

## Template structure

This section summarizes Resource Manager templates at a high level, which follow this structure:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "<template-version-number>",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Here are brief details about these attributes in Resource Manager templates. For full details, see [Azure Resource Manager template structure and syntax](../azure-resource-manager/resource-group-authoring-templates.md).

| Attribute | Required | Description |
|-----------|----------|-------------|
| `$schema` | Yes | The location for the JSON schema file that specifies the version for the Resource Manager template language |
| `contentVersion` | Yes | The version for this Resource Manager template |
| `parameters` | No | The Resource Manager template parameters to use for customizing resource deployment, for example, for dev, test, and production environments. For full details, see [Parameters - Resource Manager template structure and syntax](../azure-resource-manager/resource-group-authoring-templates.md#parameters). |
| `variables` | No | The values that you construct and use for simplifying complex expressions throughout your Resource Manager template. That way, you only need to update the variable when those values change. For full details, see [Variables - Resource Manager template structure and syntax](../azure-resource-manager/resource-group-authoring-templates.md#variables). |
| `functions` | No | Any functions that you want to create and use repeatedly within your Resource Manager template. For full details, see [Functions - Resource Manager template structure and syntax](../azure-resource-manager/resource-group-authoring-templates.md#functions). |
| `resources` | Yes | The definitions for the resources that you want the Resource Manager template to deploy or update in an Azure resource group or subscription. For full details, see [Resources - Resource Manager template structure and syntax](../azure-resource-manager/resource-group-authoring-templates.md#resources). |
| `outputs` | No | The values that you want returned after deployment, for example, values from resources that the Resource Manager template deployed. For full details, see [Outputs - Resource Manager template structure and syntax](../azure-resource-manager/resource-group-authoring-templates.md#outputs). |
||||

<a name="template-parameters"></a>

## Template parameters

Your Resource Manager template's `parameters` attribute defines the parameters for the values to use when creating and deploying resources in Azure. Your template's `resources` attribute, described later in this topic, can then reference these values for your resources to use, including your logic app's workflow definition, connections, and other resources in Azure. You can then provide these values separately through a [template parameter file](#template-parameter-file).

This example template defines parameters for the name and location to use when creating and deploying your logic app. If your logic app is also linked to an integration account, the template defines the parameter for the integration account to use, for example:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppLocation": {
         "type": "string",
         "min length": 1,
         "allowedValues": [
            "[resourceGroup().location]",
           "eastasia",
           "southeastasia",
           "centralus",
           "eastus",
           "eastus2",
           "westus",
           "northcentralus",
           "southcentralus",
           "northeurope",
           "westeurope",
           "japanwest",
           "japaneast",
           "brazilsouth",
           "australiaeast",
           "australiasoutheast",
           "southindia",
           "centralindia",
           "westindia",
           "canadacentral",
           "canadaeast",
           "uksouth",
           "ukwest",
           "westcentralus",
           "westus2"            
         ],
         "defaultValue": "westus",
         "metadata": {
            "description": "Location of the logic app"
         }
      },
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "<logic-app-name>",
         "metadata": {
            "description": "The name of the logic app to create"
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/integrationAccounts/<integration-account-name>"
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

If your logic app creates connections to other services and systems by using [managed connectors](../connectors/apis-list.md), the template's `parameters` attribute defines the parameters for the values to use when creating and deploying resources for those connections. You can then provide these values, such as connection strings and access tokens, separately through a [template parameter file](#template-parameter-file).

In this example, the template's `parameters` attribute defines parameters for an Office 365 Outlook connection. The template's `resources` attribute can then reference the values, which pass through the template's parameters, for creating and deploying the resources for that connection:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "office365_1_Connection_Name": {
        "type": "string",
        "defaultValue": "office365"
      },
      "office365_1_Connection_DisplayName": {
        "type": "string",
        "defaultValue": "<office-365-account-name>"
      },
      "LogicAppLocation": {<...>},
      "LogicAppName": {<...>}
   },
   "variables": {},
   "resources": [ 
      {
         "properties": {
            "definition": {<...>},
            "parameters": {
               "$connections": {
                  "value": {
                     "office365": {
                        // References `office365` connection resource values in parameter file
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'westus', '/managedApis/', 'office365')]",
                        // References `office365_1_Connection_Name` in template parameters
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        // References `office365_1_Connection_Name` in template parameters
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            }
         },
         // Resource information for creating and deploying logic app
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            // References 'office365_1_Connection_Name` in template parameters
           "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // References 'office365_1_Connection_Name` in template parameters
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "westus",
         "properties": {
            "api": {
               // References `office365` connection resource
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'westus', '/managedApis/', 'office365')]"
             },
             // References 'office365_1_Connection_Display_Name` in template parameters
             "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
  ],
  "outputs": {}
}
```

A template that includes a logic app workflow definition has more than one `parameters` attribute, which exist at different levels:

* Your template has its own `parameters` attribute, which differs from your workflow definition's `parameters` attribute. For example, to reference template parameter values, which are evaluated at deployment, expressions follow this syntax, which uses square brackets (**[ ]**) and the `parameters()` function:

  `"<attribute-name>": "[parameters('<template-parameter-name>')]"`

  You can provide the values for template parameters by using a [parameter file](../azure-resource-manager/resource-group-template-deploy.md#parameter-files). For more information about template parameters, see these topics:

  * [Parameters - Resource Manager template structure and syntax](../azure-resource-manager/resource-group-authoring-templates.md#parameters)
  * [Best practices for template parameters](../azure-resource-manager/template-best-practices.md#parameters)

* Your workflow definition has its own `parameters` attribute, which defines the parameters for the values to use in your workflow definition during your logic app's runtime. To reference workflow definition parameter values, which are evaluated at runtime, expressions follow this syntax, which prefixes the `parameters()` function with an "at" symbol (**@**):

  `"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

  You can provide the values for workflow definition parameters by referencing your template's parameters. For more information about workflow defintion parameters, see [Parameters in logic app workflow definitions](#workflow-parameters) later in this topic.

Here are some best practices when defining parameters:

* Define parameters only for values that vary, based on the resources that you're deploying or based on the environment where you're deploying. Don't define parameters for values that always stay the same.

* Provide default values for parameters except when parameters handle information that is sensitive or require security. To hide or protect sensitive information in parameters, follow the guidance in these topics:
  
  * [Security recommendations for template parameters](../azure-resource-manager/template-best-practices.md#parameters)
  * [Pass secure parameter values with Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md)

* Differentiate template parameters from workflow definition parameters by using descriptive names.

For more information, see [Azure Resource Manager template best practices](../azure-resource-manager/template-best-practices.md).

<a name="template-parameter-files"></a>

## Template parameter files

To store and supply the values for Resource Manager template parameters, you can create a [parameter file](../azure-resource-manager/resource-group-template-deploy.md#parameter-files). That way, you can use different parameter files based on where you want to create and deploy your logic app. For example, here is a short parameter file for the template parameters described in the previous section:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "office365_1_Connection_Name": {
        "value": "office365-2"
      },
      "office365_1_Connection_DisplayName": {
        "value": "europe_division@fabrikam.com"
      },
     "LogicAppName": {
        "value": "Fabrikam-Email-Processor-Logic-App"
     },
     "LogicAppLocation": {
        "value": [
           "northeurope",
           "westeurope"
        ]
     }
   }
}
```

<a name="template-resources"></a>

## Template resources

Your Resource Manager template's `resources` attribute declares information about each resource that you want to create and deploy. This attribute references the values that pass through the template's `parameters` attribute by using the `parameters()` function with square brackets (**[]**). In the template's `resources` attribute, the `properties` attribute contains the workflow definition for your logic app and any references to resources for connections used by your logic app. Under `properties`, more attributes reference the template parameter values used for creating and deploying your logic app:

```json
"resources": {
   "properties": {
      "state": "<enabled-or-disabled>",
      "definition": {<workflow-definition>},
      "parameters": {
         "$connections" : {<references-to-template-parameters-for-connection-resources>}
      }
   },
   "name": "[parameters('LogicAppName')]",
   "type": "Microsoft.Logic/workflows",
   "location": "[parameters('LogicAppLocation')",
   "tags": {
      "displayName": "LogicApp"
   },
   "apiVersion": "2016-06-01",
   "dependsOn": [<references-to-template-parameters-for-connection-resources>]
}
```

| Attribute | Required | Description |
|-----------|----------|-------------|
| `state` | Yes | The state for your logic app at deployment where `Enabled` means your logic app is live and `Disabled` means that your logic app is inactive |
| `definition` | Yes | Contains your logic app's underlying workflow definition, which describes how the Logic Apps service runs that workflow along with definitions for the trigger, actions, workflow parameters, outputs, schema, and so on. For more information, see [Logic app workflow definition](#workflow-definition). <p><p>To view the attributes in your logic app's workflow definition, switch from "design view" to "code view" in the Azure portal or Visual Studio, or by using a tool such as [Azure Resource Explorer](http://resources.azure.com). |
attribute |
| `parameters` | No | If your logic app uses [managed connectors](../connectors/apis-list.md) for accessing other services and systems, the `$connections` attribute references the values used by the connections that your logic app creates. For more information, see [Logic app connections](#workflow-connections). |
||||

In this example, the template `resources` attribute declares resource information for an Office 365 Outlook connection and a logic app:

```json
"resources": [ 
   {
      "properties": {
         "state": "Disabled",
         "definition": {<...>},
         "parameters": {
            "$connections": {
               "value": {
                  "office365": {
                     // References `office365` connection resource values in parameter file
                     "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'westus', '/managedApis/', 'office365')]",
                     // References `office365_1_Connection_Name` in template parameters
                     "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                     // References `office365_1_Connection_Name` in template parameters
                     "connectionName": "[parameters('office365_1_Connection_Name')]"
                  }
               }
            }
         }
      },
      // Resource information for creating and deploying logic app
      "name": "[parameters('LogicAppName')]",
      "type": "Microsoft.Logic/workflows",
      "location": "[parameters('LogicAppLocation')]",
         "tags": {
           "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            // References 'office365_1_Connection_Name` in template parameters
           "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
   },
   {
      // Resource information for creating Office 365 Outlook connection
      "type": "MICROSOFT.WEB/CONNECTIONS",
      "apiVersion": "2016-06-01",
      // References 'office365_1_Connection_Name` in template parameters
      "name": "[parameters('office365_1_Connection_Name')]",
      "location": "westus",
      "properties": {
         "api": {
            // References `office365` connection resource
            "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', 'westus', '/managedApis/', 'office365')]"
          },
          // References 'office365_1_Connection_Display_Name` in template parameters
          "displayName": "[parameters('office365_1_Connection_DisplayName')]"
      }
   }
],
```

For more information about template resources, see these topics:

* [Resources - Resource Manager template structure and syntax](../azure-resource-manager/resource-group-authoring-templates.md#resources)
* [Best practices for template resources](../azure-resource-manager/template-best-practices.md#resources).

<a name="workflow-connections"></a>

## Logic app connections

Under the template's `resources` > `properties` > `parameters` attributes, the `$connections` attribute contains references to resources that securely store metadata for any connections that your logic app creates and uses through [managed connectors](../connectors/apis-list.md). This metadata can include information such as connection strings and access tokens. In your workflow definition, the trigger or action definition that creates the connection also references the corresponding resource inside `$connections`. 

In this example, the `$connections` attribute contains an `office365` attribute, which references the resource for an Office 365 Outlook connection. In the `definition` attribute, which contains for your [workflow definition](#workflow-definition), the trigger definition contains a `connection` attribute, which includes a `name` attribute that uses the `parameters()` function to reference the `office365` attribute along with its `connectionId` attribute:

```json
{
   "resources": {
      "properties": {
         "state": "Disabled",
         // Workflow definition
         "definition": {
            "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
            "actions": {<one-or-more-action-definitions>},
            "contentVersion": "1.0.0.0",
            "outputs": {},
            "parameters": {
               "$connections": {
                  "defaultValue": {},
                  "type": "Object"
               }
            },
            "triggers": {
               // Trigger definition
               "When_a_new_email_arrives": {
                   "inputs": {
                       "host": {
                          // Reference to connection resource
                          "connection": {
                             "name": "@parameters('$connections')['office365']['connectionId']"
                          }
                       },
                       <...>
                   },
                   <...>
               }
            }
         },
         "parameters": {
            "$connections": {
               "value": {
                  // References to connection resource
                  "office365": {
                     "connectionId": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/office365",
                     "connectionName": "office365",
                     "id": "/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<logic-app-location>/managedApis/office365"
                  }
               }
            }
         }
      }
   }
}
```

Each new instance for a connection creates a resource with a unique name in Azure. So, when you have multiple instances for connections to the same service or system, each resource name is appended with a number, which increments with each new instance, for example, `office365`, `office365-1`, and so on.

> [!NOTE]
> When you view the underlying JSON for your logic app outside a Resource Manager template, the `$connections` attribute appears at the same level as the `definition` attribute, which contains your [workflow definition](#workflow-definition):
>
> ```json
> {
>    "$connections": {},
>    "definition": {}
> }
> ```

<a name="workflow-definition"></a>

## Logic app workflow definition

In the `definition` attribute, your logic app's workflow definition has this high-level structure:

```json
"definition": {
  "$schema": "<workflow-definition-language-schema-version>",
  "actions": { "<action-definitions>" },
  "contentVersion": "<workflow-definition-version-number>",
  "outputs": { "<workflow-output-definitions>" },
  "parameters": { "<workflow-parameter-definitions>" },
  "staticResults": { "<static-results-definitions>" },
  "triggers": { "<trigger-definitions>" }
}
```

Here are brief details about these attributes in workflow definitions. For full details, see [Schema reference for Workflow Definition Language](../logic-apps/logic-apps-workflow-definition-language.md).

| Attribute | Required | Description |
|-----------|----------|-------------|
| `$schema` | Only when externally referencing a workflow definition | The location for the JSON schema file that describes the Workflow Definition Language version |
| `actions` | No | The definitions for one or more actions to execute at workflow runtime. For more information, see [Triggers and actions](../logic-apps/logic-apps-workflow-definition-language.md#triggers-actions). |
| `contentVersion` | No | The version number for your workflow definition |
| `outputs` | No | The definitions for the outputs to return from a workflow run. For more information, see [Outputs](../logic-apps/logic-apps-workflow-definition-language.md#outputs) |
| `parameters` | No | The definitions for one or more parameters that pass values for your workflow to use at workflow runtime. For more information, see [Parameters](../logic-apps/logic-apps-workflow-definition-language.md#parameters). |
| `staticResults` | No | The definitions for one or more static results returned by actions as mock outputs when static results are enabled on those actions. For more information, see [Static results](../logic-apps/logic-apps-workflow-definition-language.md#static-results). |
| `triggers` | No | The definitions for one or more triggers that start your workflow. You can define more than one trigger, but only with the Workflow Definition Language, not visually through the Logic Apps Designer. For more information, see [Triggers and actions](../logic-apps/logic-apps-workflow-definition-language.md#triggers-actions). |
||||

For example, here's a logic app workflow definition that has these steps:

* An [Office 365 Outlook trigger](/connectors/office365/) that fires when a new email arrives
* An [Azure Blob Storage action](/connectors/azureblob/) that creates a blob for the email body and uploads that blob to an Azure storage container

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "Create_blob": {
         "inputs": {
            "body": "@triggerBody()?['Body']",
            "host": {
               "connection": {
                  "name": "@parameters('$connections')['azureblob']['connectionId']"
               }
            },
            "method": "post",
            "path": "/datasets/default/files",
            "queries": {
               "folderPath": "/saved-emails",
               "name": "@triggerBody()?['Subject']",
               "queryParametersSingleEncoded": true
            }
         },
         "runAfter": {},
         "runtimeConfiguration": {
            "contentTransfer": {
               "transferMode": "Chunked"
            }
         },
         "type": "ApiConnection"
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {},
   "parameters": {
      "$connections": {
         "defaultValue": {},
         "type": "Object"
      }
   },
   "triggers": {
      "When_a_new_email_arrives": {
         "inputs": {
            "host": {
               "connection": {
                  "name": "@parameters('$connections')['office365']['connectionId']"
               }
            },
            "method": "get",
            "path": "/Mail/OnNewEmail",
            "queries": {
               "fetchOnlyWithAttachment": false,
               "folderPath": "Inbox",
               "importance": "Any",
               "includeAttachments": false
            }
         },
         "recurrence": {
            "frequency": "Day",
            "interval": 1
         },
         "splitOn": "@triggerBody()?['value']",
         "type": "ApiConnection"
      }
   }
}
```

<a name="workflow-parameters"></a>

## Workflow definition parameters

In your workflow definition, you can add and edit any parameters that the workflow definition uses for accepting inputs at runtime. To make sure that the Logic App Designer can correctly show those parameters when you define these workflow definition parameters, note these practices:


For example, 




* You can use logic app parameters in these kinds of triggers and actions:

  * API connection runtime URL
  * API connection path
  * API Management call
  * Azure Functions app
  * Nested or child logic app workflow

* Provide default values for parameters except when parameters handle information that is sensitive or require security. To hide or protect sensitive information in workflow definition parameters, follow the guidance in these topics:

  * [Security recommendations for action and input parameters](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters-and-inputs)
  * [Pass secure parameter values with Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md)

For more information about workflow definition parameters, see [Parameters - Workflow Definition Language](../logic-apps/logic-apps-workflow-definition-language.md#parameters).


For example, suppose your logic app's workflow definition references a resource ID that represents an Azure function or a nested logic app workflow, and you want to deploy that resource ID along with your logic app as a single deployment. You can add that ID as a resource in your template and parameterize that ID. You can use this same approach for references to custom APIs or OpenAPI endpoints (formerly "Swagger") that you want deployed with each Azure resource group.

Here's an example that shows how you can parameterize the Azure Service Bus "Send message" action:

```json
"Send_message": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['servicebus']['connectionId']"
         },
         // If the `host.runtimeUrl` property appears in your template, 
         // you can remove this property, which is optional, for example:
         "runtimeUrl": {}
      },
      "method": "POST",
      "path": "[concat('/@{encodeURIComponent(''', parameters('<Azure-Service-Bus-queue-name>'), ''')}/messages')]",
      "body": {
         "ContentData": "@{base64(triggerBody())}"
      },
      "queries": {
         "systemProperties": "None"
      }
   },
   "runAfter": {}
},
```


## Authorize OAuth connections

At deployment, your logic app works end-to-end with valid parameters. However, you must still authorize OAuth connections to generate a valid access token.

* To authorize OAuth connections, open your logic app in Logic App Designer. In the designer, authorize any required connections.

* For automated deployment, you can use a script that provides consent for each OAuth connection. Here's an example script in GitHub in the [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) project.

## Reference dependent resources

If your logic app has references to dependent resources, you can use [Azure Resource Manager template functions](../azure-resource-manager/resource-group-template-functions.md), such as `parameters()`, `variables()`, `resourceId()`, `concat()`, and so on, for referencing those dependencies.

For example, suppose your logic app references an Azure function that you want deployed with your logic app. This sample prebuilt template shows how a logic app references that function and how the template deploys that Azure function: [GitHub - azure-quickstart-templates/101-logic-app-and-function-app](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-and-function-app)

## Next steps

> [!div class="nextstepaction"]
> [Create logic app templates](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)