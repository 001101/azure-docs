---
title: Understanding how to use Azure Digital Twins Swagger | Microsoft Docs
description: How to use Azure Digital Twins Swagger
author: kingdomofends
manager: alinast
ms.service: azure-digital-twins
services: azure-digital-twins
ms.topic: conceptual
ms.date: 10/1/2018
ms.author: adgera
---

# Azure Digital Twins Swagger

Each provisioned Azure Digital Twins instance includes its own automatically generated Swagger reference documentation.

[Swagger](https://swagger.io/) (or [OpenAPI](https://www.openapis.org/)) unites complex API information into an interactive and language-agnostic reference resource. Specifically, Swagger provides critical reference material about which JSON payloads, HTTP methods, and specific endpoints to use to perform operations against an API.

## Swagger summary

Swagger provides an interactive summary of your API including:

* REST API endpoints specifying required request payloads, headers, parameters, context paths, and HTTP methods.
* Example response information for validating and confirming HTTP responses.
* Error code information.

Swagger therefore provides a convenient tool to assist with development and testing calls to the Management API.

>[!NOTE]
> No configuration is required to use your digital twin Swagger reference material.

Your generated Management API Swagger documentation can be accessed publicly at:

```plaintext
https://{{your-instance-name}}.{{your-instance-location}}.azuresmartspaces.net/management/swagger
```

## Reference material

Automatically generated reference material is supplied explaining critical concepts and Object Models.

A concise summary describing API use is provided:

![Swagger top][1]

Core API Object Models are also listed:

![Swagger models][2]

You can click into each listed Object Model for a more detailed summary of key attributes:

![Swagger model][3]

The generated Swagger Object Models is convenient to see all available Azure Digital Twins [Ontology objects](./concepts-objectmodel-spatialgraph.md). It's therefore a great resource for developers to use when building solutions on Azure Digital Twins.

## Endpoint summary

Swagger also provides a thorough overview of all endpoints that comprise the API.

Each listed endpoint also includes required request information such as:

* Required parameters.
* Required parameter data types.
* The HTTP method to access the resource.

![Swagger endpoints][4]

Each resource can be clicked to gain a more detailed overview.

## Using Swagger to test endpoints

One of the powerful functionalities Swagger provides is the ability to **Try it out** or test an API endpoint directly through the documentation UI.

After clicking into a specific endpoint, you'll see a  **Try it out** button:

![Swagger try][5]

Expanding that button brings up input fields for each required and optional parameter. Enter the values accordingly and click **Execute**:

![Swagger tried][6]

After executing the test, you can validate the response data.

## Swagger response data

Each listed endpoint also includes valid response body data to validate your development and tests. These examples include the desired status codes and JSON for successful HTTP requests.

![Swagger response][7]

The examples also include error codes to help debug or improve failing tests.

## Swagger OAuth 2.0 authorization

To interactively test requests against API resources protected by OAuth 2.0, see the [official documentation](https://swagger.io/docs/specification/authentication/oauth2/).

## Next steps

Read more about the Management API:

> [!div class="nextstepaction"]
> [Azure Digital Twins Management API](./concepts-management-api-limits.md)

Learn how to authenticate with your Management API:

> [!div class="nextstepaction"]
> [Authenticating with APIs](./security-authenticating-apis.md)

<!-- Images -->
[1]: media/how-to-use-swagger/swagger_management_top.png
[2]: media/how-to-use-swagger/swagger_management_models.png
[3]: media/how-to-use-swagger/swagger_management_model.png
[4]: media/how-to-use-swagger/swagger_management_endpoints.png
[5]: media/how-to-use-swagger/swagger_management_try.png
[6]: media/how-to-use-swagger/swagger_management_tried.png
[7]: media/how-to-use-swagger/swagger_management_response.png
