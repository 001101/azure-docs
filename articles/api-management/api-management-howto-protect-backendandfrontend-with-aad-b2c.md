---
title: Protect a Single Page App backend with OAuth 2.0 by using AAD B2C, API Management and Easy Auth. | Microsoft Docs
description: Protect an API with OAuth 2.0 by using AAD B2C, API Management and Easy Auth in order to be able to call it from a JS SPA

.
services: api-management, aad-b2c, app-service
documentationcenter: ''
author: wieastbu
manager: karldb
editor: ''	

ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2019
ms.author: wieastbu
---

# Protect a Single Page App backend with OAuth 2.0 by using AAD B2C, API Management and Easy Auth.

This scenario shows you how to configure your Azure API Management instance to protect an API, by using the OAuth 2.0 protocol with Azure Active Directory (Azure AD) B2C and Azure API management to be used calling and securing a backend Azure Function. 

## Aims
The idea of this document is to show how API Management can be used in a real world scenario with the Azure Functions and AAD B2C services.

The basic premise is that we have a JavaScript SPA calling through and retrieving data from an API, signing in via AAD B2C and then passing through API Management to secure the API and validate the JWT in flight.
The ‘Signed in as’ is picked up from the client-side token at sign in to show that we can decipher and use the token.

## Prerequisites
To follow the steps in this article, you must have:
* An Azure v2 Storage Account to host the frontend JS Single Page App
* An API Management instance
* An empty Function app (v2 runtime) to host the called API
* An Azure AD B2C tenant, linked to a subscription 

## Overview
Here is a quick overview of the steps:

1. Create the AAD Calling (Frontend, API Management) and API Applications with scopes and grant API Access
2. Create the Sign up or sign in policies to allow users to sign in with AAD B2C
3. Configure API Management with the new AAD B2C Client IDs and keys to Enable OAuth 2.0 user authorization in the Developer Console
4. Build the Function API
5. Configure the Function API to enable EasyAuth with the new AAD B2C Client ID’s and Keys and lock down to APIM VIP 
6. Build the API Definition in API Management
7. Set up Oauth2 for the API Management API configuration
8. Setup the **CORS** policy and add the **validate-jwt** policy to validate the OAuth token for every incoming request
9. Setup the custom claim data
10. Test the API from the APIM Portal 
11. Build the calling application to consume the API
12. Upload the JS SPA Sample
13. Configure the Sample JS Client App with the new AAD B2C Client ID’s and keys 
14. Configure the Function API to serve as a useful reminder countdown
15. Test the Client Application

## Create the AAD B2C configuration
1. Select the **Applications** tab 
2. Click the 'Add' button and create three applications
* The Frontend Client, 
* The Backend Function API,
* The API Management developer portal (unless your're running APIM in consumption mode)

3. Use placeholders for the reply urls for now, we’ll update those urls later.
4. Now set the App ID URI, choose something unique and relevant to the service being created.
5. Set WebApp/ Web API and Allow Implicit flow to yes
6. Record the AppID URI, name and Application ID for later use for all three apps.

7. Open the Backend API from the list of applications and select the *Keys* tab (under General) to generate an auth key
8. Record the key somewhere safe for later use
9. Now select the *Published Scopes* Tab (Under API Access)
10. Create and name a scope for your Function API 

> [!NOTE]
> AAD B2C scopes are effectively permissions within your API that other applications can request access to via the API access blade from their applications, effectively you just created and assigned application permissions for your calling API.

11. Open the other two applications and then look under the *API Access* tab, grant them access to the backend API scope (that you just created) and the default one that was already there ("login as user") 
12. Generate them a key each (select the *Keys* tab (under General) to generate an auth key) and record those keys somewhere safe too.

## Create a "Signup or signin" policy to allow users to sign in with AAD B2C
1. Return to the root of the AAD B2C Blade 
2. Then select “Sign-up or Sign-in Policies” and click ‘add’
3. Give the policy a name (and record it for later) and select 'Identity providers', then check User ID sign up and click OK. 
4. Select 'Sign-up attributes' and choose the registration options that you want your customers to enter (At a minimum, choose Email, Display Name and Country/Region), then click OK.
5. Select 'Application Claims' and choose 'Country/Region', 'Display Name', 'User's Object ID' and 'User is new', then click OK.
6. Click OK again to return to the main AAD B2C blade, now select the policy that you created in the list (to reopen it), then record the address of the b2clogin.com domain.
7. Click on the link at the top for the well-known openid configuration endpoint and record the authorization_endpoint and token_endpoint values from the opened document.

> [!NOTE]
> B2C Policies allow you to expose the AAD B2C login endpoints to be able to capture different data components and log in users in different ways. 
> In this case we configured a sign up and sign in endpoint, which exposed a well-known condfiguration endpoint, specifically our created policy was identified in the URL by the p= parameter.
> Once this is done – you now have a functional Business to Consumer identity platform that will sign users into multiple applications. 
> If you want to you can click 'run now' here (to go through the sign up and sign in process) and get a feel for what it will do in practice, but the redirection step at the end will fail as the app has not yet been deployed.

## [Optional] Configure Oauth2 for the API Management Developer Console

1. Switch back to your standard AAD tenant in the Azure portal and open the *API management blade*, then open *your instance*.
2. Note down the *Virtual IP (VIP) address* of the instance, and optionally the *developer portal URL* and record them for later.
3. Next, Select the Oauth 2.0  blade from the Security Tab, and click 'Add'
4. Give sensible values for *Display Name* and *Description*
5. You can enter any value in the Client registration page URL, as this won't be used.
6. Check the *Authorization code* and *Implicit Auth* Grant types
7. In the *Authorization* and *Token* endpoint fields enter the values you captured from the well-known configuration xml document earlier.
8. Scroll down and populate an *Additional body parameter* called 'resource' with the 
Function API client ID from the AAD B2C App registration
9. Set the Client credentials section upSet the Client ID to the APIM Developer console app's application ID and set the Client Secret to the APIM developer console app's Key) 
10. Lastly, now record the redirect_uri of the auth code grant from APIM under the 'This is what the redirect_uri for authorization code grant looks like' entry for later use.

> [!NOTE]
> Now we have an API Management instance that knows how to get access tokens from AAD B2C to authorize requests through the developer portal for testing.

## Build the Function API 
1. Go to the Function Apps blade of the Azure portal, open the function app and create a new http triggered C# function,
2. Set it’s name to HttpTriggerC# and it’s auth level to Anonymous (we'll secure this function, but not by using a function key or admin key).
3. Paste the sample code from below into Run.csx over the existing code that appears.

```c#

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;


public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    return (ActionResult)new OkObjectResult($"Hello World");
}

```

> [!NOTE]
> The c# script function code you just pasted simply logs a line to the console, and returns the text "Hello World".

4. Select “Integrate” from the left-hand blade, then select ‘Advanced Editor’ in the top-right-hand corner of the pane.
5. Paste the sample code below over the existing json.


```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "methods": [
        "get"
      ],
      "route": "hello"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

> [!NOTE]
> The bindings you just created simply tell Functions to respond on anonymous http GET requests to the URI https://functionappname.azurewebsites.net/api/celebration 
>
> Now we have a scalable serverless https API, that is capable of returning a very simple payload.

## Configure and secure the Function API to enable EasyAuth and IP restrictions 
1. Two extra areas in the function app need to be configured (Auth and Network Restrictions).
2. Firstly Let's configure Authentication / Authorization, so click on the name of the function app (next to the <Z> icon to show the overview page.
3. Next Select the 'Platform features' tab and select 'Authentication / Authorization'.
4. Turn on the App Service Authentication feature, but leave anonymous requests allowed (We’ll block them, but don’t want random redirects interfering with debugging at the moment)
5. Under 'Authentication Providers' choose ‘Azure Active Directory’, and choose ‘Advanced’ from the Management Mode switch.
6. Paste the BACKEND API's application ID (from AAD B2C into the ‘Client ID’ box)
7. Paste the Well-known open-id configuration from the signup/signin policy into the Issuer URL box (we recorded this configuration earlier).

> [!NOTE]
> Now your Function API is deployed and should throw 401 or 403 errors for unauthorized requests, and should return data when a valid request is presented.
> But we still have no IP security, if you have a valid token you can call this from anywhere - we want to force all requests to come via API Management.

8. Close the 'Authentication / Authorization' blade 
9. Select 'Networking' and then select 'IP Restrictions'
10. We need to lock down the IP ranges of the allowed callers to the function app to be the IP Address of the API Management instance VIP (Found on the API management overview page in the portal) and if you want to interact with the functions portal, you'll need to add your public IP address / range here too.
11. Once theres an allow entry in the list, theres an implicit deny rule to block all other addresses, note that you have to add CIDR blocks of addresses to the IP restrictions panel, so if you need to add a single address (such as the API Management VIP), you need to add it in the format xx.xx.xx.xx/32

> [!NOTE]
> Now your Function API should not be callable from anywhere other than via API management, or your address.

## Import the Function App Definition into API Management
1. Open the API Management portal blade and select your API Management instance.
2. Select the APIs Blade from the API Management section of your instance.
3. From the 'Add a New API' pane, choose 'Function App', then select 'Full' from the top of the popup.
4. Click Browse, choose the function app you're hosting the API inside, and click select.
5. Give the api a sensible set of names and descriptions and add it to the ‘unlimited’ Product.
6. Make sure you record the base URL for later use and then click create.

# Set up Oauth2 for the API Management API configuration
1. Your API will appear on the left-hand side of the portal under the 'All APIs' section, open your API by clicking on it.
2. Select the 'Settings' Tab.
3. Update your settings by selecting “Oauth 2.0” from the user authorization radio button.
4. Select the OAuth server that you defined earlier.
5. Check the ‘Override scope’ checkbox and enter the scope you recorded for the BACKEND API call earlier on.

# Configure the redirect URIs for the Application Registrations in B2C
1. Open the AAD B2C blade and navigate to the application registration for the Developer Portal
2. Set the 'Reply URL' entry to the one you noted down when you configured the redirect_uri of the auth code grant in API Management earlier.

> The following section does not apply to the **Consumption** tier, which does not support the developer portal.

Now that the OAuth 2.0 user authorization is enabled on the `Echo API`, the Developer Console obtains an access token on behalf of the user, before calling the API.

1. Browse to any operation under the `Echo API` in the developer portal, and select **Try it** to bring you to the Developer Console.
2. Note a new item in the **Authorization** section, corresponding to the authorization server you just added.
3. Select **Authorization code** from the authorization drop-down list, and you're prompted to sign in to the Azure AD tenant. If you're already signed in with the account, you might not be prompted.
4. After successful sign-in, an `Authorization: Bearer` header is added to the request, with an access token from Azure AD B2C encoded in Base64. 
5. Select **Send** and you can call the API successfully.

> [!NOTE]
> Now API management is able to acquire tokens for the developer portal to test your API and is able to understand it's definition and render the appropriate test page in the dev portal.

# Set up the **CORS** policy and add the **validate-jwt** policy to validate the OAuth token for every incoming request.
1. Switch back to the design tab and choose “All Operations”, then click the code view button to show the policy editor.
2. In the inbound section after <base/>, paste the following (but edit the url to match your well known B2C endpoint for the 
sign in and sign up policy, and edit the claim value to match the valid application ID (or client ID) for the BACKEND API APPLICATION).

```xml
       <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://tenant.b2clogin.com/tenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_MyDefaultPolicy" />
            <required-claims>
                <claim name="aud">
                    <value>your-backend-api-application-client-id</value>
                </claim>
            </required-claims>
        </validate-jwt>
        <cors>
            <allowed-origins>
                <origin>*</origin>
            </allowed-origins>
            <allowed-methods>
                <method>GET</method>
            </allowed-methods>
            <allowed-headers>
                <header>*</header>
            </allowed-headers>
            <expose-headers>
                <header>*</header>
            </expose-headers>
        </cors>
```

> [!NOTE]
> Now API management is able respond to cross origin requests to JS SPA apps, and it will perform throttling, rate-limiting and pre-validation of the JWT auth token being passed BEFORE 
> forwarding the request on to the Function API.

> The following section does not apply to the **Consumption** tier, which does not support the developer portal.

## Test the API from the API Management Developer Portal
1. From the overview blade of the API Management section of the Azure portal, click 'Developer Portal'
2. 
be automatically signed into the developer portal as an administrator of the API, where you and other selected consumers of your API can test and call them without needing to build client software.
3. Select ‘Products’, then choose ‘Unlimited’, then choose the API we created earlier and click ‘TRY IT’
4. Unhide the API subscription key, and copy it somewhere safe along with the request url, you'll need it later when we configure the application, 
5. Also select Implicit, from the oauth auth dropdown and you may have to authenticate here with a popup.
6. Click ‘Send’ and if all is well, your Function App should respond back with a hello message via API management with a 200 OK message and some JSON.

> [!NOTE]
> Congratulations, you now have AAD B2C, API Management and Azure Functions working together to publish, secure AND consume an API. 
> You might have noticed that the API is in fact secured twice using this method, once with the API Management Ocp-Subscription-Key Header, and once with the Authorization: Bearer JWT.
> You would be correct, as this example is a JavaScript Single Page Application, we use the API Management Key only for rate-limiting and billing calls.
> The actual Authorization and Authentication is handled by AAD B2C, and is encapsulated in the JWT, which gets validated twice, once by API Management, and then by App Service.
> If you have a secure channel for credentials (A JS SPA is *not* considered a secure channel) then in some circumstances just using the API Key to secure server to server communications may be enough, depending on your security requirements.

## Build the calling JavaScript Single Page Application to consume the API
1. Open the storage accounts blade in the Azure portal
2. Create a new General Purpose V2 Storage Account
3. Select the account you created and select the 'Static Website' blade from the Settings section 
4. Set the static web hosting feature to 'enabled'
5. Note down the contents of the Primary Endpoint, as this location is where the frontend site will be hosted.

> [!NOTE]
> You could use either Azure Blob Storage + CDN rewrite, or Azure App Service - but Blob Storage's Static Website hosting feature gives us a default container to serve static web content / html / js / css from Azure Storage and will infer a default page for us for zero work.

## Upload the JS SPA Sample
1. Still in the storage account blade, select the 'Blobs' blade from the Blob Service section and click on the $web container that appears in the right-hand pane.
2. Save the code below to a file locally on your machine as index.html and then upload the file index.html to the $web container.

```html








```

3. Browse to the Static Website Primary Endpoint you stored earlier in the last section.

[!NOTE]
> Congratulations, you just deployed a JavaScript Single Page App to Azure Storage
> Since we haven’t configured the JS app with your keys for the api or configured the JS app with your AAD B2C details yet – the page will look a little strange when you open it, nothing will be populated yet!

## Configure the Sample JS Client App with the new AAD B2C Client ID’s and keys 
1. Now we know where everything is: we can configure the SPA with the appropriate API Management API address and the correct AAD B2C application / client IDs
2. Go back to the Azure portal storage blade and click on index.html, then choose ‘Edit Blob’ 
3. Scroll down to line 52 and update the details to match your FRONT END application that you registered in AAD B2C, noting that the 'b2cScopes' values are for the associated backend application that is being called (the API).
4. It should look something like the below code:-  

```
var applicationConfig = {			
clientID: 'the registered application ID of the CLIENT OR FRONTEND Application in B2C’, // This represents the app obtaining the token
            	authority: "https://{tenant name}.b2clogin.com/tfp/{tenant URI}/{signinsignuppolicyname}", 
            	b2cScopes: [{an array of the scopes that you configured in AAD B2C for the BACKEND API}], // These are permissions you're asking for (to be able to call in the backend API)
	webApi: ‘{the actual address of the API when called through api management}’, // This was ‘request url’ in the developer portal
	subKey: '{the api-m subscription key you want to call them with}' // this was the value in ‘ocp-apim-subscription-key
        };
```

## Configure the redirect URIs for the Application Registrations in B2C
1. Open the AAD B2C blade and navigate to the application registration for the JavaScript Frontend Application
2. Set the redirect URL to the one you noted down when you previously set up the static website primary endpoint above

> [!NOTE] 
> This configuration will result in a client of the frontend application receiving an access token with appropriate claims from AAD B2C 
> The SPA will be able to add this as a bearer token in the https header in the call to the backend API.
> API Management will pre-validate the token, and rate-limit calls to the endpoint by the subscriber key, before passing through the request to the receiving Azure Function API.
> The SPA will render the response in the browser.
> *Congratulations, you’ve configured Azure Active Directory B2C, Azure API Management, Azure Functions, Azure App Service Authorization to work in perfect harmony, only one thing remains.*

## Configure the Function API to serve as a useful reminder countdown
1. Go back to the Function Apps blade in the Azure portal.
2. Open the Function App by selecting it from the list.
3. Open the source code for the function HttpTriggerC# in the editor by clicking on the name of the function.

string json = "Hello API Management";


> [!NOTE]
> Now we have a simple app with an API that has a purpose, let's test it.

## Test the Client Application
1. Open the sample app URL that you noted down from the storage account you created earlier
2. Click “Login” in the top-right-hand corner, this will pop-up your B2C sign in / up profile.
3. Once complete the ‘logged in as’ section of the screen will be populated.
4. Now Click ‘Call Web Api’, and you should get a popup alert with the address of your API in it.
5. OK that and the screen should update with  a rolling countdown to your event

## And we're done 
The steps above can be adapted and edited to allow many different uses of Azure AD B2C with API Management.

## Next steps
* Learn more about [Azure Active Directory and OAuth2.0](../active-directory/develop/authentication-scenarios.md).
* Check out more [videos](https://azure.microsoft.com/documentation/videos/index/?services=api-management) about API Management.
* For other ways to secure your back-end service, see [Mutual Certificate authentication](api-management-howto-mutual-certificates.md).
* Consider using the AAD Graph API to assign custom claims and using an API Management policy to validate they are present in the token.

* [Create an API Management service instance](get-started-create-service-instance.md).

* [Manage your first API](import-and-publish.md).
