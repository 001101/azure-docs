---
title: Authenticate with Mobile Engagement REST APIs - manual setup
description: Describes how to manually setup authentication for Mobile Engagement REST APIs
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''

ms.assetid: 2e79f9c9-41e4-45ac-b427-3b8338675163
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo

---
# Authenticate with Mobile Engagement REST APIs - manual setup
This documentation is an appendix to [Authenticate with Mobile Engagement REST APIs](mobile-engagement-api-authentication.md). Make sure you read it first to understand the context. It also describes an alternate way to do the one-time setup for authentication for the Mobile Engagement REST APIs using the Azure portal.

> [!NOTE]
> The following instructions are based on this [Active Directory guide](../azure-resource-manager/resource-group-create-service-principal-portal.md) and customized for what is required for authentication for Mobile Engagement APIs. Refer to it if you want to understand the following steps in detail.

1. Sign in to your Azure Account through the [Azure portal](https://portal.azure.com/).
2. Select **Active Directory** from the left pane.

     ![Select Active Directory][1]

3. To view the applications in your directory, select **App registrations**.

     ![View applications][3]

4. Select **New application registration**.

     ![Add application][4]

5. Fill in the name of the application. Leave the type of application as **Web app/API**, and then select the **Next** button. You can provide any dummy URLs for **SIGN-ON URL**. They are not used for this scenario and the URLs themselves are not validated.

After you finish, you have an Azure AD app with the name you provided. It is your **AD\_APP\_NAME**, so be sure to make a note of it.

     ![App name][8]

7. Select the app name.

8. Find **Application ID** and make a note of it. It is be the CLIENT ID that is to be used as **CLIENT\_ID** for your API calls.

     ![Configure app][10]

9. Find the **Keys** section on the right.

     ![Configure app][11]

10. Create a new key, and then immediately copy it and save it for use. It will never be shown again.

     ![Configure app][12]

    > [!IMPORTANT]
    > This key expires at the end of the duration that you specified. Make sure to renew it when the time comes, otherwise your API authentication won't work anymore. You can also delete and recreate this key if you think that it has been compromised.
    >
    >
11. Select **Endpoints** button at the top of the page. Then copy the **OAUTH 2.0 TOKEN ENDPOINT**.

    ![][14]

16. This endpoint is in the following form, where the GUID in the URL is your **TENANT_ID**: `https://login.microsoftonline.com/<GUID>/oauth2/token`

17. Now we  proceed to configure the permissions on this app. For this, you  have to open up the [Azure portal](https://portal.azure.com).

18. Select **Resource Groups**, and then find the **Mobile Engagement** resource group.

    ![][15]

19. Select the **Mobile Engagement** resource group, and then navigate to the **Settings** section.

    ![][16]

20. Select **Users** in the **Settings** section, and then select **Add** to add a user.

    ![][17]

21. Click **Select a role**.

    ![][18]

22. Select **Owner**.

    ![][19]

23. Search for the name of your application, **AD\_APP\_NAME**, in the Search box. You will not see this by default here. After you find it, select it. Then click **Select** at the bottom of the section.

    ![][20]

24. On the **Add Access** section, it appears as **1 user, 0 groups**. Select **OK** in this section to confirm the change.

    ![][21]

You have now completed the required Azure AD configuration and you are all set to call the APIs.

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication-manual/active-directory.png
[2]: ./media/mobile-engagement-api-authentication-manual/active-directory-details.png
[3]: ./media/mobile-engagement-api-authentication-manual/view-applications.png
[4]: ./media/mobile-engagement-api-authentication-manual/add-icon.png
[5]: ./media/mobile-engagement-api-authentication-manual/what-do-you-want-to-do.png
[6]: ./media/mobile-engagement-api-authentication-manual/tell-us-about-your-application.png
[7]: ./media/mobile-engagement-api-authentication-manual/app-properties.png
[8]: ./media/mobile-engagement-api-authentication-manual/aad-app.png
[9]: ./media/mobile-engagement-api-authentication-manual/configure-menu.png
[10]: ./media/mobile-engagement-api-authentication-manual/client-id.png
[11]: ./media/mobile-engagement-api-authentication-manual/client-secret.png
[12]: ./media/mobile-engagement-api-authentication-manual/keys.png
[13]: ./media/mobile-engagement-api-authentication-manual/view-endpoints.png
[14]: ./media/mobile-engagement-api-authentication-manual/app-endpoints.png
[15]: ./media/mobile-engagement-api-authentication-manual/resource-groups.png
[16]: ./media/mobile-engagement-api-authentication-manual/resource-groups-settings.png
[17]: ./media/mobile-engagement-api-authentication-manual/add-users.png
[18]: ./media/mobile-engagement-api-authentication-manual/add-role.png
[19]: ./media/mobile-engagement-api-authentication-manual/select-role.png
[20]: ./media/mobile-engagement-api-authentication-manual/add-user-select.png
[21]: ./media/mobile-engagement-api-authentication-manual/add-access-final.png



