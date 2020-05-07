---
title: Securing Azure Functions
description: Learn about how to make your function code running in Azure more secure from common attacks.
ms.date: 4/13/2020
ms.topic: conceptual

#Customer intent: As a developer, I want to understand the security features and principles of Azure Functions so that I can make my cloud-based function code as secure as possible.
---

# Securing Azure Functions

In many ways, planning for secure development, deployment, and operation of serverless functions is much the same as for any web-based or cloud hosted application. [Azure App Service](/azure/app-service/) provides the hosting infrastructure for your function apps. This article provides security strategies for running your function code, and how App Service can help you secure your functions. 

[!INCLUDE [app-service-security-intro](../../includes/app-service-security-intro.md)]

For a complete set of security recommendations for Azure Functions that follows the [Azure Security Benchmark](/azure/security/benchmarks/overview), see [Azure Security Baseline for Azure Functions](security-baseline.md).

## Secure operation 

This section provides guidance for ways to configure and run your function app as securely as possible. 

### Security Center

Security Center integrates with your function app in the portal. It provides, for free, a quick assessment of potential configuration-related security vulnerabilities. Function apps running in a dedicated plan can also leverage the real-time security features of Security Center, for an additional cost. To learn more, see [Protect your Azure App Service web apps and APIs](../security-center/security-center-app-services.md). 

### Log and monitor

One to detect attacks is through activity monitoring activity and logging analytics. Functions integrates with Application Insights to collects log, performance, and error data for your function app. Application Insights automatically detects performance anomalies and includes powerful analytics tools to help you diagnose issues and to understand how your functions are used. To learn more, see [Monitor Azure Functions](functions-monitoring.md).

Functions also integrates with Azure Monitor Logs to enable you to consolidate function app logs with system events for easier analysis. To learn more, see [Monitoring Azure Functions with Azure Monitor Logs](functions-monitor-log-analytics.md). 

For more security recommendations for observability, see the [Azure security baseline for Azure Functions](security-baseline.md#logging-and-monitoring). 

### Require HTTPS

By default, clients can connect to function endpoints by using both HTTP or HTTPS. We recommend redirecting HTTP to HTTPs because HTTPS uses the SSL/TLS protocol to provide a secure connection, which is both encrypted and authenticated. To learn how, see [Enforce HTTPS](../app-service/configure-ssl-bindings.md#enforce-https).

When you require HTTPS, you should also Require the latest TLS version. To learn how, see [Enforce TLS versions](../app-service/configure-ssl-bindings.md#enforce-tls-versions).

For more information see [Secure connections (TSL)](../app-service/overview-security.md#https-and-certificates).

### Function access keys

[!INCLUDE [functions-authorization-keys](../../includes/functions-authorization-keys.md)]

To learn more about access keys, see the [HTTP trigger binding article](functions-bindings-http-webhook-trigger.md#obtaining-keys).

### Authentication/authorization

While function keys can provide some mitigation for unwanted access, the only way to truly secure your function endpoints is by implementing positive authentication of clients accessing your functions. You can then make authorization decisions based on identity.  

[!INCLUDE [functions-enable-auth](../../includes/functions-enable-auth.md)]

### Permissions

As with any application or service, the goal is run your function app with the lowest possible permissions. 

#### Least privilege 

Functions supports build-in [Azure role-based access control (RBAC)](../role-based-access-control/overview.md). RBAC roles supported by Functions are [Contributor](../role-based-access-control/built-in-roles.md#contributor), [Owner](../role-based-access-control/built-in-roles.md#owner), and [Reader](../role-based-access-control/built-in-roles.md#owner). 

Permissions are effective at the function app level. 

>[!TODO:] What is the least privilege to perform key Functions tasks (create,publish,restart, etc.)

#### Managed identities

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

For more information, see [How to use managed identities for App Service and Azure Functions](../app-service/overview-managed-identity.md?toc=%2fazure%2fazure-functions%2ftoc.json).

#### Restrict CORS access

[Cross-origin resource sharing (CORS)](https://en.wikipedia.org/wiki/Cross-origin_resource_sharing) is a way to allow web apps running in another domain to make requests to your HTTP trigger endpoints. App Service provides built-in support for handing the required CORS headers in HTTP requests. CORS rules are defined on a function app level.  

While it's tempting to use a wildcard that allow all sites to access your endpoint, this defeats the purpose of CORS, which is to help prevent cross-site scripting attacks. You should instead add a separate CORS entry to for the domain of each web app that must access your endpoint. 

### Managing secrets 

To be able to connect to the various services and resources need to run your code, function apps need to be able to access secrets, such as connection strings and service keys. This section describes how to store secrets required by your functions.

Never store secrets in your function code. 

#### Application settings

By default, you store connection strings and secrets used by your function app and bindings as application settings. This makes these credentials available to both your function code and the various bindings used by the function. The application setting (key) name is used to retrieve the actual value, which is the secret. 

For example, every function app requires an associated storage account, which is used by the runtime. By default, the connection to this storage account is stored in an application setting named `AzureWebJobsStorage`.

App settings and connection strings are stored encrypted in Azure, and they're decrypted only before being injected into your app's process memory when the app starts. The encryption keys are rotated regularly. If you prefer to instead manage the secure storage of your secrets, the app setting should instead be references to Azure Key Vault. 

#### Key Vault references

While application settings are sufficient for most many functions, you may want to share the same secrets across multiple services. In this case, redundant storage of secrets results in more potential vulnerabilities. A more secure approach is to a central secret storage service and use references to this service instead of the secrets themselves.      

[Azure Key Vault](../key-vault/general/overview.md) is a service that provides centralized secrets management, with full control over access policies and audit history. You can use a Key Vault reference in the place of a connection string or key in your application settings. To learn more, see [Use Key Vault references for App Service and Azure Functions](../app-service/app-service-key-vault-references.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### Set usage quotas

Consider setting a usage quota on functions running in a Consumption plan. When you set a daily GB-sec limit on the sum total execution of functions in your function app, execution is stopped when the limit is reached. This could potentially help mitigate against malicious code executing your functions. To learn how to estimate consumption for your functions, see [Estimating Consumption plan costs](functions-consumption-costs.md). 

### Data validation

The triggers and bindings used by your functions don't provide any additional data validation. You should validate any data received from a trigger or input binding. If an upstream service is compromised, you don't want unvalidated inputs flowing through your functions. For example, if your function stores data from an Azure Storage queue in a relational database, you must validate the data and parameterize your commands to avoid SQL injection attacks. 

Never assume that the data coming into your function has already been validated or sanitized. It's also a good idea to verify that the data being written to output bindings is valid. 

### Handle errors

While it seems basic, it's important to write good error handling in your functions. Unhandled errors bubble-up to the host and are handled by the runtime. Different bindings handle processing of errors differently. To learn more, see [Azure Functions error handling](functions-bindings-error-pages.md).

### Disable remote debugging

Make sure that remote debugging is disabled, except when you are actively debugging your functions. You can disable remote debugging in the **General Settings** tab of your function app **Configuration** in the portal. 

### Restrict CORS access

[!INCLUDE [functions-cors](../../includes/functions-cors.md)]

Don't use wildcards in your allowed origins list. Instead, list the specific domains from which you expect to get requests.

### Store data encrypted

[!INCLUDE [functions-storage-encryption](../../includes/functions-storage-encryption.md)]

## Secure deployment

Azure Functions tooling an integration make it easy to publish local function project code to Azure. It's important to understand how deployment works when considering security for an Azure Functions topology.   

### Deployment credentials

App Service deployments require a set of deployment credentials. These deployment credentials are used to secure your function app deployments. Deployment credentials are managed by the App Service platform and are encrypted at rest. 

There are two kinds of deployment credentials:

[!INCLUDE [app-service-deploy-credentials](../../includes/app-service-deploy-credentials.md)]

At this time, Key Vault isn't supported for deployment credentials. To learn more about managing deployment credentials, see [Configure deployment credentials for Azure App Service](../app-service/deploy-configure-credentials.md).

### Disable FTP

By default, each function app has an FTP endpoint enabled. The FTP endpoint is accessed using deployment credentials. 

FTP isn't recommended for deploying your function code. FTP deployments are manual, and they require you to synchronize triggers. To learn more, see [FTP deployment](functions-deployment-technologies.md#ftp). 

When you're not planning on using FTP, you should disable it in the portal. If you do choose to use FTP, you should [enforce FTPS](../app-service/deploy-ftp.md#enforce-ftps).

### Secure the scm endpoint

Every function app has a corresponding `scm` service endpoint that used by the Advanced Tools (Kudu) service for deployments and other App Service [site extensions](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions). The scm endpoint for a function app is always a URL in the form `https://<FUNCTION_APP_NAME.scm.azurewebsites.net>`. When you use network isolation to secure your functions, you must also account for this endpoint. 

By having a separate scm endpoint, you can control deployments and other advanced tools functionalities for function app that are isolated or running in a virtual network. The scm endpoint supports both basic authentication (using deployment credentials) and single sign-on with your Azure portal credentials. To learn more, see [Accessing the Kudu service](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service). 

## Network security

Restricting network access to your function app lets you control who can access your functions endpoints. Functions leverages App Service infrastructure to enable your functions to access resources without using internet-routable addresses or to restrict internet access to a function endpoints. To learn more about these networking options, see [Azure Functions networking options](functions-networking-options.md).

### Set access restrictions

Access restrictions allow you to define lists of allow/deny rules to control traffic to your app. Rules are evaluated in priority order. If there are no rules defined then your app will accept traffic from any address.To learn more, see [Azure App Service Access Restrictions #](../app-service/app-service-ip-restrictions.md?toc=%2fazure%2fazure-functions%2ftoc.json).

### Private site access

[!INCLUDE [functions-private-site-access](../../includes/functions-private-site-access.md)]

### Deploy your function app in isolation

[!INCLUDE [functions-deploy-isolation](../../includes/functions-deploy-isolation.md)]

### Use a gateway service

Gateway services, such as [Azure Application Gateway](../application-gateway/overview.md) and [Azure Front Door](../frontdoor/front-door-overview.md) let you setup a Web Application Firewall (WAF). WAF rules are used to monitor or block detected attacks, which provides an extra layer of protection for your functions. To setup a WAF, your function app needs to be running in an ASE or using Private Endpoints (preview). To learn more, see [Using Private Endpoints](../app-service/networking/private-endpoint.md).    

## Next steps

+ [Azure Security Baseline for Azure Functions](security-baseline.md)
+ [Azure Functions diagnostics](functions-diagnostics.md)
        