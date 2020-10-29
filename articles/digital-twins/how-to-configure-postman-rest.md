---
title: How to configure Postman - Azure Digital Twins | Microsoft Docs
description: Learn how to configure and use Postman to test the Azure Digital Twins APIs.
ms.author: baanders
author: baanders
ms.service: digital-twins
services: digital-twins
ms.topic: how-to
ms.date: 09/09/2020
---

# How to configure Postman for Azure Digital Twins

[Postman](https://www.getpostman.com/) is a REST testing tool that provides key HTTP request functionalities in a desktop and plugin-based GUI. You can use it to craft HTTP requests and submit them to the [Azure Digital Twins REST APIs](how-to-use-apis-sdks.md).

This article describes how to configure the [Postman REST client](https://www.getpostman.com/) to interact with the Azure Digital Twins APIs. Specifically, it describes:

* How to configure an Azure Active Directory application to use the OAuth 2.0 implicit grant flow.
* How to use the Postman REST client to make token-bearing HTTP requests to your APIs.
* How to use Postman to make multipart POST requests to your APIs.

## Prerequisites

To proceed with using Postman to access the Azure Digital Twins APIs, you need to...
* Create an Azure Digital Twins instance 
* Download the desktop version of the Postman client. Navigate to [*www.getpostman.com/apps*](https://www.getpostman.com/apps) and follow the prompts to download the app.

## Configure Azure Active Directory to use the OAuth 2.0 implicit grant flow

1. Follow the steps in [*How-to: Create an app registration*](how-to-create-app-registration.md) to create and configure an app registration in Azure Active Directory. Alternatively, you can reuse an existing app registration.

    :::image type="content" source="media/how-to-configure-postman/authentication-redirect-uri.png" alt-text="Configure a new Postman Redirect URI" lightbox="media/how-to-configure-postman/authentication-redirect-uri.png" :::

1. Now, add a **Redirect URI** to `https://www.getpostman.com/oauth2/callback`.

1. Select the **Implicit grant** > **Access tokens** check box to allow the OAuth 2.0 implicit grant flow to be used. Select **Configure**, then **Save**.

1. Copy the **Client ID** of your Azure Active Directory app.

## Obtain an OAuth 2.0 token

In the examples below, `YOUR_MANAGEMENT_API_URL` refers to the URI of the Digital Twins APIs:

```URL
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0
```

| Name | Replace with |
| --- | --- |
| YOUR_INSTANCE_NAME | The name of your Azure Digital Twins instance |
| YOUR_LOCATION | The region your instance is hosted on |

Set up and configure Postman to obtain an Azure Active Directory token. Afterwards, make an authenticated HTTP request to Azure Digital Twins using the acquired token:

1. Verify that your **Authorization URL** is correct. It should take the format:

    ```plaintext
    https://login.microsoftonline.com/YOUR_AZURE_TENANT.onmicrosoft.com/oauth2/authorize?resource=0b07f429-9f4b-4714-9392-cc5e8e80c8b0
    ```

    | Name  | Replace with | Example |
    |---------|---------|---------|
    | YOUR_AZURE_TENANT | The name of your tenant or organization. Use the human-friendly name instead of the alphanumeric **Tenant ID** of your Azure Active Directory app registration. | `microsoft` |

1. Go to [www.getpostman.com](https://www.getpostman.com/) to download the app.

1. We want to make GET request. Select the **Authorization** tab, select OAuth 2.0, and then select **Get New Access Token**.

    | Field  | Value |
    |---------|---------|
    | Grant Type | `Implicit` |
    | Callback URL | `https://www.getpostman.com/oauth2/callback` |
    | Auth URL | Use the **Authorization URL** from **step 1** |
    | Client ID | Use the **Application ID** for the Azure Active Directory app that was created or reused from the previous section |
    | Scope | Leave blank |
    | State | Leave blank |
    | Client Authentication | `Send as Basic Auth header` |

1. The client should now appear as:

    :::image type="content" source="media/how-to-configure-postman/configure-postman-oauth-token.png" alt-text="Postman client token example" lightbox="media/how-to-configure-postman/configure-postman-oauth-token.png":::

1. Select **Request Token**.
  
1. Scroll down, and select **Use Token**.

## Make a multipart POST request

After completing the previous steps, configure Postman to make an authenticated HTTP multipart POST request:

1. Under the **Headers** tab, add an HTTP request header key **Content-Type** with value `multipart/mixed`.

   :::image type="content" source="media/how-to-configure-postman/configure-postman-content-type.png" alt-text="Specify content type multipart/mixed" lightbox="media/how-to-configure-postman/configure-postman-content-type.png":::

1. Serialize non-text data into files. JSON data would be saved as a JSON file.
1. Under the **Body** tab, select `form-data`. 
1. Add each file by assigning a **key** name, selecting `File`.
1. Then, select each file through the **Choose File** button.

   :::image type="content" source="media/how-to-configure-postman/configure-postman-form-body.png" alt-text="Postman client form body example" lightbox="media/how-to-configure-postman/configure-postman-form-body.png":::

   >[!NOTE]
   > * The Postman client does not require that multipart chunks have a manually assigned **Content-Type** or **Content-Disposition**.
   > * You do not need to specify those headers for each part.
   > * You must select `multipart/mixed` or another appropriate  **Content-Type** for the entire request.

1. Lastly, select **Send** to submit your multipart HTTP POST request. A status code of `200` or `201` indicates a successful request. The appropriate response message will appear in the client interface.

1. Validate your HTTP POST request data by calling the API endpoint: 

   ```URL
   YOUR_MANAGEMENT_API_URL/spaces/blobs?includes=description
   ```

## Next steps

- To learn about the Digital Twins APIs, and how to use them, read [*How-to: Use the Azure Digital Twins APIs and SDKs](how-to-use-apis-sdks.md).