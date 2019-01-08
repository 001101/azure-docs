---
title: Postman FHIR server in Azure tutorial - Microsoft Healthcare APIs
description: This article describes how to access a FHIR API with Postman.
services: healthcare-apis
ms.service: healthcare-apis
ms.topic: tutorial
ms.reviewer: dseven
ms.author: mihansen
author: hansenms
ms.date: 02/11/2019
---

# Tutorial: Access FHIR API with Postman

A client application would access a FHIR API through a [REST API](https://www.hl7.org/fhir/http.html). You may also want to interact directly with the FHIR server as you build applications, for example, for debugging purposes. In this tutorial, we will walk through the steps needed to use [Postman](https://www.getpostman.com/) to access the FHIR server. Postman is a tool often used for debugging when building applications that access APIs.

## Prerequisites

- A FHIR endpoint in Azure. You can set that up using the Microsoft Healthcare APIs for FHIR. There are quickstarts available for [Azure portal](fhir-oss-portal-quickstart.md), [PowerShell](fhir-oss-powershell-quickstart.md), or [Azure CLI](fhir-oss-cli-quickstart.md).
- Postman installed. You can get it from [https://www.getpostman.com](https://www.getpostman.com)

## FHIR server and authentication details

In order to use Postman, you will need to know the following details:

- Your FHIR server URL, for example, `https://MYFHIRSERVICE.azurewebsites.net` or `https://MYACCOUNT.microsofthealthcare-apis.com/fhir`
- The identity provider `Authority` for your FHIR server, for example, `https://login.microsoftonline.com/{TENANT-ID}`
- The configured `audience`, which would be set in the [Azure AD resource application registration](register-resource-azure-ad-client-app.md).
- The `client_id` (or application ID) of the [client application](register-confidential-azure-ad-client-app.md) you will be using to access the FHIR service.
- The `client_secret` (or application secret) of the client application.

Finally, you should check that `https://www.getpostman.com/oauth2/callback` is a registered reply URL for your client application.

## Connect to FHIR server

Using Postman, do a `GET` request to `https://fhir-server-url/metadata`:

![Postman Metadata Capability Statement](media/tutorial-postman/postman-metadata.png)

In this example, the FHIR server URL is `https://fhirdocsmsft.azurewebsites.net` and the capability statement of the server is available at `https://fhirdocsmsft.azurewebsites.net/metadata`. That endpoint should be accessible without authentication.

If you attempt to access restricted resources, you should then an "Authentication failed" response:

![Authentication Failed](media/tutorial-postman/postman-authentication-failed.png)

## Obtaining an access token

To obtain a valid access token, select "Authorization" and pick TYPE "OAuth 2.0":

![Set OAuth 2.0](media/tutorial-postman/postman-select-oauth2.png)

Hit "Get New Access Token" and a dialog appears:

![Request New Access Token](media/tutorial-postman/postman-request-token.png)

You will need to some details:
| Field                 | Example Value                                                                                                   | Comment                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Token Name            | MYTOKEN                                                                                                         | A name you choose          |
| Grant Type            | Authorization Code                                                                                              |                            |
| Callback URL          | `https://www.getpostman.com/oauth2/callback`                                                                      |                            |
| Auth URL              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=https://MYFHIRSERVICE.azurewebsites.net` | resource is the `audience` |
| Access Token URL      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                      |                            |
| Client ID             | `2a73e546-XXX-XXXX-XXXX-35d04139f7cc`                                                                            | Application ID             |
| Client Secret         | `XXXXXXXX`                                                                                                        | Secret client key          |
| State                 | `1234`                                                                                                            |                            |
| Client Authentication | Send client credentials in body                                                                                 |                 

Hit "Request Token" and you will be guided through the Azure Active Directory Authentication flow and a token will be returned to Postman. If you run into problems open the Postman Console (from the "View->Show Postman Console" menu item).

Scroll down on the returned token screen and hit "Use Token":

![Use Token](media/tutorial-postman/postman-use-token.png)

The token should now be populated in the "Access Token" field and you can select tokens from "Available Tokens". If you "Send" again to repeat the `Patient` resource search, you should get a Status `200 OK`:

![200 OK](media/tutorial-postman/postman-200-OK.png)

In this case, there are no patients in the database and the search is empty.

If you inspect the access token with a tool like [https://jwt.ms](https://jwt.ms), you should see content like:

```json
{
  "aud": "https://FHIRSERVER-AUDIENCE.azurewebsites.net",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "2a73e546-7a7f-4dc3-92e2-35d04139f7cc",
  "appidacr": "1",

  ...// Truncated
}
```

In troubleshooting situations, validating that you have the correct audience (`aud` claim) is a good place to start

## Inserting a patient

Now that you have a valid access token. You can insert a new patient. Switch to method "POST" and add the following JSON document in the body of the request:

[!code-json[](samples/sample-patient.json)]

Hit "Send" and you should see that the patient is successfully created:

![Patient Created](media/tutorial-postman/postman-patient-created.png)

If you repeat the patient search, you should now see the patient record:

![Patient Created](media/tutorial-postman/postman-patient-found.png)

## Next steps

In this tutorial, you've accessed a FHIR API using postman. Read about the supported API features in Microsoft FHIR server for Azure.
 
>[!div class="nextstepaction"]
>[Supported FHIR features](fhir-features-supported.md)