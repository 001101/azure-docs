---
title: Shifting from Express.js to Azure Functions
description: Refactoring Express.js endpoints to Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 07/23/2020
ms.author: cshoe
---

# Shifting from Express.js to Azure Functions

Express.js is one of the most popular Node.js frameworks for web developers and remains an excellent choice for building apps that serve API endpoints.

When migrating code to a serverless architecture, refactoring Express.js endpoints affects the following areas:

- **Middleware**: Express.js features a robust collection of middleware. Many middleware modules are no longer required in light of Azure Functions and [Azure API Management](../api-management/api-management-key-concepts.md) capabilities. Ensure you can replicate or replace any logic handled by essential middleware before migrating endpoints.

- **Differing APIs**: The API used to process both requests and responses differs among Azure Functions and Express.js. The following example details the required changes.

- **Default route**: By default, Azure Functions endpoints are exposed under the `api` route. Routing rules are configurable via [`routePrefix` in the _host.json_ file](./functions-bindings-http-webhook-output.md#hostjson-settings).

- **Configuration and conventions**: There are a few configuration and conventions you need to consider as you move to Azure Functions. A Functions app uses the _function.json_ file to define HTTP verbs, define security policies, and can configure the function's [input and output](./functions-triggers-bindings.md). Also, by convention, the folder name, which contains the function files defines the endpoint name.

## Example

### Express.js

The following example shows a typical Express.js `GET` endpoint.

```javascript
// server.js
app.get('/hello', (req, res) => {
  try {
    res.send("Success!");
  } catch(error) {
    const err = JSON.stringify(error);
    res.status(500).send(`Request error. ${err}`);
  }
});
```

When a `GET` request is sent to `/hello`, an `HTTP 200` response containing "Success" is returned. If the endpoint returns an error, the response is an `HTTP 500` with the error details.

### Azure Functions

The following example implements the same result as the Express.js endpoint, but with Azure Functions.

# [JavaScript](#tab/javascript)

```javascript
// hello/index.js
module.exports = async function (context, req) {
  try {
    context.res = { body: "Success!" };
  } catch(error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`
    };
  }
};
```

# [TypeScript](#tab/typescript)

```typescript
// index.ts
import { AzureFunction, Context, HttpRequest } from "@azure/functions";

const httpTrigger: AzureFunction = async function (
  context: Context,
  req: HttpRequest
): Promise<void> {
  try {
    context.res = { body: "Success!" };
  } catch (error) {
    const err = JSON.stringify(error);
    context.res = {
      status: 500,
      body: `Request error. ${err}`,
    };
  }
};

export default httpTrigger;
```

---

When moving to Functions, the following changes are made:

- **Module:** The function code is implemented as a JavaScript module that requires you to provide a value for `module.exports`.

- **Context  and response object**: The [`context`](./functions-reference-node.md#context-object) allows you to communicate with the Function's runtime. From the context, you can read request data and set the function's response. Synchronous code requires you to call `context.done()` to complete execution, while `asyc` functions resolve the request implicitly.

- **Naming convention**: The folder name used to contain the Azure Functions files is used as the endpoint name by default (this can be overridden in the _function.json_).

- **Configuration**: You define the HTTP verbs in the *function.json* file such as `POST` or `PUT`.

The following _function.json_ file holds configuration information for the function.

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["get"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

By defining `get` in the `methods` array, the function is available to HTTP `GET` requests. If you want to your api to accept support `POST` requests, you can add `post` to the array as well.

## Next steps

- Learn more with the interactive tutorial [Refactor Node.js and Express APIs to Serverless APIs with Azure Functions](https://docs.microsoft.com/learn/modules/shift-nodejs-express-apis-serverless/)
