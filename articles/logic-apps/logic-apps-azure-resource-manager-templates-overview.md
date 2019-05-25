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
ms.date: 05/28/2019
---

# Overview: Automate deployment for logic apps with Azure Resource Manager templates

When you're ready to automate creating and deploying your logic app, you can expand your logic app's underlying workflow definition into an [Azure Resource Manager template](../azure-resource-manager/resource-group-overview.md). This template defines the infrastructure, resources, parameters, and other information for provisioning and deploying your logic app. By defining parameters for values that vary at deployment, also known as *parameterizing*, you can repeatedly and consistently deploy logic apps based on different deployment needs.

For example, if you deploy to environments for development, test, and production, you likely use different connection strings for each environment. You can define template parameters that accept different connection strings and then store those strings in a separate [parameter file](../azure-resource-manager/resource-group-template-deploy.md#parameter-files). For parameter values that are sensitive or must be secured, such as passwords and secrets, you can store those values in [Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md) and have your parameter file retrieve those values. That way, you can change those values without having to update and redeploy the template.

This overview describes the attributes in a Resource Manager template that includes a logic app workflow definition. Both the template and your workflow definition use JSON syntax, but some differences exist because the workflow definition also follows the [Workflow Definition Language schema](../logic-apps/logic-apps-workflow-definition-language.md). For example, template expressions and workflow definition expressions differ in how they [reference parameters](#parameter-references) and the values that they can handle.

> [!TIP]
> For the easiest way to get a valid parameterized template for your logic app that's mostly ready to deploy, either download your logic app from the Azure portal into Visual Studio where you've installed the Azure Logic Apps Tools for Visual Studio extension, or use the PowerShell module for creating logic app templates. For more information, see [Create Azure Resource Manager templates for logic apps](../logic-apps/logic-apps-create-azure-resource-manager-templates.md).

The example logic app in this topic uses an [Office 365 Outlook trigger](/connectors/office365/) that fires when a new email arrives and an [Azure Blob Storage action](/connectors/azureblob/) that creates a blob for the email body and uploads that blob to an Azure storage container. The examples also show how to parameterize values that vary at deployment.

For more information about Resource Manager templates, see these topics:

* [Azure Resource Manager template structure and syntax](../azure-resource-manager/resource-group-authoring-templates.md)

* [Azure Resource Manager template best practices](../azure-resource-manager/template-best-practices.md)

For sample templates, see these examples:

* [Full template](#full-example-template) that's used for this topic's examples

* [Sample quickstart logic app template](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create) in GitHub

<a name="template-structure"></a>

## Template structure

At the top level, a Resource Manager template follows this structure, which is fully described in the [Azure Resource Manager template structure and syntax](../azure-resource-manager/resource-group-authoring-templates.md) topic:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

For logic app deployment, you primarily work with the `parameters` and `resources` attributes:

| Attribute | Description |
|-----------|-------------|
| `parameters` | Defines the template parameters for accepting the values to use when creating and customizing resources for deployment in Azure. For example, these parameters accept the values for your logic app's name and location, connections, and other resources necessary for your deployment environment. You can store the values for these parameters in a [parameter file](#template-parameter-files), which is described later in this topic. For general details, see [Parameters - Resource Manager template structure and syntax](../azure-resource-manager/resource-group-authoring-templates.md#parameters). |
| `resources` | Defines the resources that your template deploys or updates inside an Azure resource group, such as your workflow definition, connections, storage accounts, and so on. For general details, see [Resources - Resource Manager template structure and syntax](../azure-resource-manager/resource-group-authoring-templates.md#resources). |
||||

> [!IMPORTANT]
> Template syntax is case-sensitive so make sure that you use consistent casing.

<a name="template-parameters"></a>

## Template parameters

Your logic app's template has multiple `parameters` attributes that exist at different levels and perform different functions. At the template's top level, the `parameters` attribute defines parameters for the values to use when creating and deploying resources in Azure, for example:

* The Azure resource for your logic app

* The Azure resources for the connections in your logic app that access other services and systems through [managed connectors](../connectors/apis-list.md)

* Other Azure resources that your logic app requires at deployment

  For example, if your logic app uses an [integration account](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) for business-to-business (B2B) scenarios, the template's `parameters` attribute defines the parameter that accepts the resource ID for that integration account.

Here is an example that shows just the template parameters that accept the values to use for creating and deploying these resources in Azure:

* Name and location to use for creating and deploying a logic app

* Values to use for connecting to an Office 365 Outlook account

* Values to use for connecting to an Azure Blob storage account and access key

* ID to use for an integration account that's linked to the logic app

Except for the parameter that has `securestring` type because the value is an access key, all the other parameters include `defaultValue` attributes, although in some cases, the attributes specify an empty value:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   // Template parameters
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "min length": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "functions": [],
   "resources": [],
   "outputs": {}
}
```

Other attributes in your template use the values that pass through these template parameters, for example:

* Your [template's `resources` attribute](#template-resources), covered later in this topic, defines each resource that you want to create and deploy in Azure. In this `resources` attribute, the `properties` attribute includes a `parameters` attribute that specifies your workflow definition's parameter values, which are used by your logic app at runtime, by referencing your template's parameters.

* At a deeper level in your template's `resources` attribute, inside the `definition` attribute that describes your workflow definition, another `parameters` attribute defines your workflow definition's parameters for the values used by your logic app at runtime. These values are specified in the `parameters` attribute, which is described in the preceding paragraph, that appears outside your workflow definition but still inside the `resources` attribute.

> [!NOTE]
> Keep in mind that template parameters are evaluated at deployment, while workflow definition parameters are evaluated at your logic app's runtime. As a result, template parameters can use only values available at deployment, while workflow definition parameters can use values available at both deployment and runtime.

This template structure shows how the different `parameters` attributes relate to each other:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         // Logic app resource information
         "properties": {
            <...>,
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               <other-workflow-definition-attributes>,
               "parameters": {<workflow-definition-parameters>},
               <other-workflow-definition-attributes>
            },
            "parameters": {<workflow-definition-parameter-values>},
            <...>
         },
         <...>
      },
      <other-resource-information>
   ],
   "outputs": {}
}
```

For more information about parameters, see:

* [Template parameters](../azure-resource-manager/resource-group-authoring-templates.md#parameters)

* [Workflow definition parameters](#workflow-parameters)

<a name="best-practices-parameters"></a>

## Best practices for parameters

Here are some best practices for defining parameters:

* Define parameters only for values that vary, based on your deployment needs. Don't define parameters for values that stay the same across different deployment requirements.

* Include the `defaultValue` attribute, which can specify empty values, for all parameters except for values that are sensitive or must be secured. Always use secured parameters for user names, passwords, and secrets. To hide or protect sensitive parameter values, follow the guidance in these topics:
  
  * [Security recommendations for template parameters](../azure-resource-manager/template-best-practices.md#parameters)

  * [Pass secure parameter values with Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md)

* To differentiate template parameter names from workflow definition parameter names, you can use descriptive template parameter names, for example: `TemplateLogicAppName`

For more template best practices, see [Best practices for template parameters](../azure-resource-manager/template-best-practices.md#parameters).

<a name="template-parameter-files"></a>

## Template parameter files

To provide the values for template parameters, store those values in a [parameter file](../azure-resource-manager/resource-group-template-deploy.md#parameter-files), which uses this file name syntax:

**<*logic-app-name*>.parameters.json**

That way, you can use different parameter files based on where you want to create and deploy the resources for your logic app.  

Here is the syntax for a parameter file, including an example for [passing a secure parameter value with Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md):

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
      "<parameter-name-1>": {
         "value": "<parameter-value>"
      },
      "<parameter-name-2>": {
         "value": "<parameter-value>"
      },
      "<securestring-parameter-name>": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/<key-vault-name>",
            },
            "secretName: "<secret-name>"
         }
      },
      <other-parameter-values>
   }
}
```

This example parameter file provides values for the template parameters defined earlier in this topic:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
   "contentVersion": "1.0.0.0",
   // Template parameter values
   "parameters": {
     "LogicAppName": {
        "value": "Email-Processor-Logic-App"
     },
     "LogicAppLocation": {
        "value": "westeurope"
     },
      "office365_1_Connection_Name": {
        "value": "office365-email-processor"
      },
      "office365_1_Connection_DisplayName": {
        "value": "fabrikam-marketing@fabrikam.com"
      },
      "azureblob_1_Connection_Name": {
         "value": "azureblob-email-processor"
      },
      "azureblob_1_Connection_DisplayName": {
         "value": "fabrikam-marketing@fabrikam.com"
      },
      "azureblob_1_accountName": {
         "value": "fabrikamemailstorage",
      },
      "azureblob_1_accessKey": {
         "reference": {
            "keyVault": {
               "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/Microsoft.KeyVault/vaults/fabrikam-key-vault"
            },
            "secretName": "fabrikam-email-storage-key"
         }
      }
 }
}
```

<a name="template-resources"></a>

## Template resources

Your template has a `resources` attribute, which is an array that contains definitions for each resource that you want to create and deploy in Azure, such as your logic app's resource and other resources that your logic app needs for deployment, such as connection resources.

```json
{
   <other-template-attributes>
   "resources": [
      // Start logic app resource definition
      {
         "properties": {
            "definition": {<workflow-definition>},
            // Parameter values to use at logic app runtime
            "parameters": {
               // Connection values to use at logic app runtime
               "$connections": {},
            },
         },
         <other-logic-app-resource-attributes>
      },
      // End logic app resource definition
      // Start connection resource definitions
      {
         <connection-resource-definition>
      }
   ],
   "outputs": {}
}
```

### Logic app resource definition

Your logic app's resource definition starts with the `properties` attribute, which specifies your logic app's state at deployment, the ID for any integration account used by your logic app, a `definition` attribute that contains your workflow definition, and a `parameters` attribute that specifies the workflow definition parameter values to use at runtime, and other resource information for your logic app. Outside the `properties` attribute but still inside the `resources` attribute, the template describes other resources that your logic app needs, such as connection resources.

```json
{
   <other-template-attributes>
   "resources": [
      // Start logic app resource definition
      {
         "properties": {
            "state": <"Enabled" or "Disabled">,
            "integrationAccount": {<integration-account-ID>},
            "definition": {<workflow-definition>},
            // Parameter values to use at logic app runtime
            "parameters": {
               "$connections": {<connection-values-to-use-at-logic-app-runtime>},
            },
            "accessControl": {}
         },
         <other-logic-app-resource-attributes>
      },
      // End logic app resource definition
      // Start connection resource definitions
      {
         <connection-resource-definition>
      }
   ],
   "outputs": {}
}
```

Here are the attributes in your logic app resource:

| Attribute | Required | Description |
|-----------|----------|-------------|
| `state` | Yes | The state for your logic app at deployment where `Enabled` means your logic app is live and `Disabled` means that your logic app is inactive. For example, if you're not ready for your logic app to go live but want to deploy a draft version, you can use the `Disabled` option. |
| `integrationAccount` > `id` | No | The ID for the integration account, if used by your logic app for business-to-business (B2B) scenarios |
| `definition` | Yes | Your logic app's underlying workflow definition, which describes how the Logic Apps service runs that workflow along with definitions for the trigger, actions, workflow parameters, outputs, schema, and so on. For more information, see [Logic app workflow definition](#workflow-definition). <p><p>To view the attributes in your logic app's workflow definition, switch from "design view" to "code view" in the Azure portal or Visual Studio, or by using a tool such as [Azure Resource Explorer](http://resources.azure.com). |
| `parameters` | No | If your logic app uses [managed connectors](../connectors/apis-list.md) for accessing other services and systems, this attribute uses the `$connections` attribute to specify values used by the connectors in your logic app. For more information, see [Logic app connections](#workflow-connections). |
| `accessControl` | No | For specifying security attributes for your logic app, such as restricting IP access to request triggers or run history inputs and outputs. For more information, see [Secure access to logic apps](../logic-apps/logic-apps-securing-a-logic-app.md). |
||||

> [!NOTE]
> Templates can include resource definitions for multiple logic apps, so make sure that all your logic app resources specify the same Azure resource group. When you deploy the template to an Azure resource group by using Visual Studio, you're prompted for which logic app that you want to open. Also, your Azure resource group project can contain more than one template, so make sure that you select the correct parameter file when prompted.

This example shows how the `resources` attribute defines the resources to create and deploy for a logic app. These resource definitions reference the template parameters defined earlier in this topic:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<previously-defined-template-parameters},
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "<logic-app-deployment-state>",
            // Reference template parameter for integration account ID
            "integrationAccount": {
               "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {<logic-app-workflow-definition>},
            "parameters": {
               // Values for workflow definition definition parameters
               "$connections": {
                  "value": {
                     "office365": {
                        // Connector ID, which must use same location as logic app
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        // Reference template parameter for connection resource name
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        // Reference template parameter for connection resource name
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     },
                     "azureblob": {
                        // Connector ID, which must use same location as logic app
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        // Reference template parameter for connection resource name
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        // Reference template parameter for connection resource name
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                    }
                  }
               }
            },
            // Other logic app resource information that references template parameters
            "name": "[parameters('LogicAppName')]",
            "type": "Microsoft.Logic/workflows",
            "location": "[parameters('LogicAppLocation')]",
            "tags": {
               "displayName": "LogicApp"
            },
            "apiVersion": "2016-06-01",
            "dependsOn": [
               // Resource dependencies, which reference template parameters for connection resource names
               "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
               "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]"
            ]
         }
      },
      // Office 365 Outlook API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Reference template parameter for connection resource name
         "name": "[parameters('office365_1_Connection_Name')]",
         // Connection resource location, which must use same location as logic app
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID, which must use same location as logic app
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            // Reference template parameter for connection display name
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      // Azure Blob Storage API connection resource definition
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         // Reference template parameter for connection resource name
         "name": "[parameters('azureblob_1_Connection_Name')]",
         // Connection resource location, which must use same location as logic app
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               // Connector ID, which must use same location as logic app
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
             // Reference template parameter for connection display name
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            // Reference template parameters for account name and access key
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

For more information about resources and their attributes, see these topics:

* [Resources - Resource Manager template structure and syntax](../azure-resource-manager/resource-group-authoring-templates.md#resources)

* [Best practices for template resources](../azure-resource-manager/template-best-practices.md#resources).

<a name="workflow-connections"></a>

## Connection resources

Your template's `resources` attribute defines the resources that you want to create and deploy, including resources for any connections that your logic app creates and uses. This attribute has a `properties` attribute that defines your logic app resource and includes a `parameters` attribute in which the `$connections` attribute specifies the values for any connections that your workflow definition uses at runtime. These values use template expressions that reference resources that securely store metadata for the connections that your logic app creates and uses through [managed connectors](../connectors/apis-list.md). This metadata can include information such as connection strings and access tokens, which you can provide in a parameter file and pass through your template's parameters for use at deployment.

However, the `$connections` parameter definition itself appears *inside* your workflow definition's `definition` attribute, which has a `parameters` attribute that defines parameters for the values that your workflow definition accepts at runtime. The difference between whether parameter values are available at deployment versus runtime affect the syntax that you use for expressions that [reference parameters](#parameter-references) and the values that they can access.

This structure shows the different locations where the `$connections` parameter is defined and where the `$connection` parameter values are specified:

```json
{
   <other-template-attributes>
   "resources": [
      {
         // Logic app resource definition starts here
         "properties": {
            "definition": {
               <other-workflow-definition-attributes>,
               // Parameter definitions for values to use at runtime
               "parameters": {
                  // Parameter definitions for connection values to use at runtime
                  "$connections": {
                    "defaultValue": {},
                    "type": "Object"
                  }
               },
               <other-workflow-definition-attributes>
            },
            // Parameter values for use at runtime by workflow definition
            "parameters": {
               // Connection values to use at runtime by workflow definition
               "$connections": {},
            },
         },
         <logic-app-resource-definition>
      },
      // Logic app resource definition ends here
      // Connection resource definitions start here
      {
         <connection-resource-definition>
      }
   ],
   "outputs": {}
}
```

In your template, the `$connections` attribute appears in a different location compared to when you use code view in the Azure portal or Visual Studio to view the `$connections` attribute. In code view, the `$connections` attribute still appears outside the `definition` attribute for your workflow definition but at the same level:

```json
{
   "$connections": {<workflow-definition-parameter-values-for-connections>},
   "definition": {<workflow-definition>}
}
```

> [!NOTE]
> Make sure that connections in your template use the same Azure resource group and location as your logic app. Also, each connection that you create has a unique name in Azure. When you create multiple connections to the same service or system, each connection name is appended with a number, which increments with each new connection created, for example, `office365`, `office365-1`, and so on.

This example just shows the workflow definition parameter value for the Office 365 Outlook connection, the logic app's resource definition, which specifies a dependency on that connection, and the connection's resource definition:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {},
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Enabled",
            "definition": {
            },
            "parameters": {
               // Connection values for workflow definition parameters
               "$connections": {
                  "value": {
                     // Office 365 Outlook connection values for workflow definition parameters
                    "office365": {
                        // Connector ID, which must use same location as logic app
                        // To reference the resource group location instead, use resourceGroup().location
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        // Reference the template parameter for `office365_1_Connection_Name`
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        // Reference the template parameter for `office365_1_Connection_Name`
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            }
         },
         // Logic app resource information
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            // Reference the template parameter for 'office365_1_Connection_Name`
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         // Office 365 Outlook API connection resource definition
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      }
   ],
   "outputs": {}
}
```

For a connection that requires authentication, the resource definition includes a `parameterValues` attribute, which specifies the authentication values in name-value pair format to use. You can then provide those values in the [template parameter file](#template-parameter-files).

Here is an example that specifies the account name and access key for an Azure Blob storage account connection:

```json
// Azure Blob Storage API connection resource definition
{
   "type": "MICROSOFT.WEB/CONNECTIONS",
   "apiVersion": "2016-06-01",
   "name": "[parameters('azureblob_1_Connection_Name')]",
   "location": "[parameters('LogicAppLocation')]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
      },
      "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
      // Reference the template parameters for the authentication values to use
      "parameterValues": {
         "accountName": "[parameters('azureblob_1_accountName')]",
         "accessKey": "[parameters('azureblob_1_accessKey')]"
      }
   }
}
```

<a name="workflow-definition"></a>

## Workflow definition

In your template, your logic app's workflow definition appears in the `definition` attribute, which is inside the `properties` attribute that defines your logic app resource in the template's `resources` attribute. The `definition` attribute is the same attribute that appears in code view when you switch from designer view in the Azure portal and is fully described in the [Schema reference for Workflow Definition Language](../logic-apps/logic-apps-workflow-definition-language.md) topic.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Enabled",
            // Workflow definition starts here
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {<action-definitions>},
               "parameters": {<workflow-definition-parameters>},
               "triggers": {<trigger-definition>},
               "contentVersion": "1.0.0.0",
               "outputs": {<workflow-outputs>}
            },
            // Workflow definition ends here
            "parameters": {<workflow-definition-parameter-values>}
         }
      }
   ],
   "outputs": {}
}
```

Here is an example workflow definition that corresponds with the other examples in this topic:

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   // Workflow action definitions
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
               "folderPath": "/emails",
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
   // Workflow definition parameters
   "parameters": {
      "$connections": {
         "defaultValue": {""},
         "type": "Object"
      }
   },
   // Workflow trigger definition
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
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
},
```

<a name="workflow-parameters"></a>

## Workflow definition parameters

In your workflow definition's `parameters` attribute, you can add or edit any parameters that your workflow definition uses for accepting inputs at runtime. By default, this `parameters` attribute is empty unless your logic app creates and uses connections to other services and systems through [managed connectors](../connectors/apis-list.md). You can specify values for workflow definition parameters inside the upper-level `parameters` attribute that's outside your workflow definition but still inside the template's `resources` attribute.

This example includes only the `$connections` workflow definition parameter that accepts the runtime values for the connections used by your logic app's workflow definition.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "variables": {},
   "functions": [],
   "resources": [
      {
         "properties": {
            "state": "Enabled",
            // Workflow definition starts here
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {},
               // Workflow definition parameters
               "parameters": {
                  // Workflow definition parameters for connections
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {},
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            // Workflow definition ends here
            "parameters": {
               "$connections": {
                  "value": {
                     "<connection-values-for-workflow-definition-parameters>"
                  }
               },
               <other-workflow-definition-parameter-values>
            }
         }
      }
   ],
   "outputs": {}
}
```

To make sure that the Logic App Designer can correctly show these parameters, note these practices:

* You can define parameters for these kinds of triggers and actions:

  * Azure Functions app

  * Nested or child logic app workflow

  * API Management call

  * The runtime URL for an API connection

  * The `path` attribute for an APIConnection trigger or action

* Include the `defaultValue` attribute, which can specify empty values, for all parameters except for values that are sensitive or must be secured. Always use secured parameters for user names, passwords, and secrets. To hide or protect sensitive parameter values, follow the guidance in these topics:

  * [Security recommendations for action and input parameters](../logic-apps/logic-apps-securing-a-logic-app.md#secure-action-parameters-and-inputs)

  * [Pass secure parameter values with Azure Key Vault](../azure-resource-manager/resource-manager-keyvault-parameter.md)

For more information about workflow definition parameters, see [Parameters - Workflow Definition Language](../logic-apps/logic-apps-workflow-definition-language.md#parameters).

<a name="parameter-references"></a>

## References to parameters

To reference template parameters, you use template expressions and [template functions](../azure-resource-manager/resource-group-template-functions.md). Template expressions use square brackets (**[]**), for example:

`"<attribute-name>": "[parameters('<template-parameter-name>')]"`

To reference workflow definition parameters, you use [Workflow Definition Language expressions and functions](../logic-apps/workflow-definition-language-functions-reference.md). However, you might notice that some template functions and workflow definition functions have the same name. Workflow definition expressions use the "at" symbol (**@**), for example:

`"<attribute-name>": "@parameters('<workflow-definition-parameter-name>')"`

For more information about functions, see:

* [Template functions](../azure-resource-manager/resource-group-template-functions.md)

* [Workflow definition functions](../logic-apps/workflow-definition-language-functions-reference.md)

Although you might want to provide the values for your workflow definition's parameters by referencing your template's parameters, using template expressions inside your workflow definition can result in more complicated code. Also, template expressions are evaluated at deployment, while workflow definition expressions are evaluated at runtime.

Instead, in your template's `resources` attribute, define your workflow definition's parameters inside the `parameters` attribute *within* the `definition` attribute for your workflow. You can then specify values for your workflow definition's parameters inside the upper-level `parameters` attribute that's outside your workflow definition but still inside the template's `resources` attribute.

<a name="full-example-template"></a>

## Full example template

Here is the parameterized sample template that's used by this topic's examples:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
   "parameters": {
      "LogicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "defaultValue": "MyLogicApp",
         "metadata": {
            "description": "The resource name to use for the logic app"
         }
      },
      "LogicAppLocation": {
         "type": "string",
         "min length": 1,
         "defaultValue": "[resourceGroup().location]",
         "metadata": {
            "description": "The resource location to use for the logic app"
         }
      },
      "office365_1_Connection_Name": {
         "type": "string",
         "defaultValue": "office365",
         "metadata": {
            "description": "The resource name to use for the Office 365 Outlook connection"
         }
      },
      "office365_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "The display name to use for the Office 365 Outlook connection"
         }
      },
      "azureblob_1_Connection_Name": {
         "type": "string",
         "defaultValue": "azureblob",
         "metadata": {
            "description": "The resource name to use for the Azure Blob storage account connection"
         }
      },
      "azureblob_1_Connection_DisplayName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         },

      },
      "azureblob_1_accountName": {
         "type": "string",
         "defaultValue": "",
         "metadata": {
            "description": "Name of the storage account the connector should use."
         }
      },
      "azureblob_1_accessKey": {
         "type": "securestring",
         "metadata": {
            "description": "Specify a valid primary/secondary storage account access key."
         }
      },
      "LogicAppIntegrationAccount": {
         "type":"string",
         "minLength": 1,
         "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/fabrikam-integration-account-rg/providers/Microsoft.Logic/integrationAccounts/fabrikam-integration-account",
         "metadata": {
            "description": "The ID to use for the integration account"
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "properties": {
            "state": "Enabled",
            "integrationAccount": {
              "id": "[parameters('LogicAppIntegrationAccount')]"
            },
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
               "actions": {
                  "Create_blob": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['azureblob']['connectionId']"
                           }
                        },
                     },
                     "method": "post",
                     "body": "@triggerBody()?['Body']",
                     "path": "/datasets/default/files",
                     "queries": {
                        "folderPath": "/emails",
                        "name": "@triggerBody()?['Subject']",
                        "queryParametersSingleEncoded": true
                     },
                     "runAfter": {},
                     "runtimeConfiguration": {
                        "contentTransfer": {
                           "transferMode": "Chunked"
                        }
                     }
                  }
               },
               "parameters": {
                  "$connections": {
                     "defaultValue": {},
                     "type": "Object"
                  }
               },
               "triggers": {
                  "When_a_new_email_arrives": {
                     "type": "ApiConnection",
                     "inputs": {
                        "host": {
                           "connection": {
                              "name": "@parameters('$connections')['office365']['connectionId']"
                           }
                        },
                        "method": "get",
                        "path": "/Mail/OnNewEmail",
                        "queries": {
                           "folderPath": "Inbox",
                           "importance": "Any",
                           "fetchOnlyWithAttachment": false,
                           "includeAttachments": false
                        }
                     },
                     "recurrence": {
                        "frequency": "Day",
                        "interval": 1
                     },
                     "splitOn": "@triggerBody()?['value']"
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "$connections": {
                  "value": {
                     "azureblob": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
                        "connectionName": "[parameters('azureblob_1_Connection_Name')]"
                     },
                     "office365": {
                        "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]",
                        "connectionId": "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]",
                        "connectionName": "[parameters('office365_1_Connection_Name')]"
                     }
                  }
               }
            },
            "accessControl": {}
         },
         "name": "[parameters('LogicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('LogicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "dependsOn": [
            "[resourceId('Microsoft.Web/connections', parameters('azureblob_1_Connection_Name'))]",
            "[resourceId('Microsoft.Web/connections', parameters('office365_1_Connection_Name'))]"
         ]
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('office365_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
                "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'office365')]"
            },
            "displayName": "[parameters('office365_1_Connection_DisplayName')]"
         }
      },
      {
         "type": "MICROSOFT.WEB/CONNECTIONS",
         "apiVersion": "2016-06-01",
         "name": "[parameters('azureblob_1_Connection_Name')]",
         "location": "[parameters('LogicAppLocation')]",
         "properties": {
            "api": {
               "id": "[concat(subscription().id, '/providers/Microsoft.Web/locations/', parameters('LogicAppLocation'), '/managedApis/', 'azureblob')]"
            },
            "displayName": "[parameters('azureblob_1_Connection_DisplayName')]",
            "parameterValues": {
               "accountName": "[parameters('azureblob_1_accountName')]",
               "accessKey": "[parameters('azureblob_1_accessKey')]"
            }
         }
      }
   ],
   "outputs": {}
}
```

## Authorize OAuth connections

At deployment, your logic app works end-to-end with valid parameters. However, you must still authorize OAuth connections to generate a valid access token. Here are ways that you can authorize OAuth connections:

* For automated deployment, you can use a script that provides consent for each OAuth connection. Here's an example script in GitHub in the [LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth) project.

* To manually authorize OAuth connections, open your logic app in Logic App Designer. In the designer, authorize any required connections.

## Next steps

> [!div class="nextstepaction"]
> [Create logic app templates](../logic-apps/logic-apps-create-azure-resource-manager-templates.md)