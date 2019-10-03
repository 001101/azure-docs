---
author: mmacy
ms.service: active-directory-b2c
ms.topic: include
ms.date: 09/27/2019
ms.author: marsma
# Used by the identity provider (IdP) setup articles under "Custom policy"
---
1. Sign in to the [Azure portal](https://portal.azure.com).
1. Select the **Directory + subscription** filter in the top menu, and then select the directory that contains your Azure AD B2C tenant.
1. In the left menu, select **Azure AD B2C**. Or, select **All services** and search for and select **Azure AD B2C**.
1. Select **Applications (Preview)**, and then select **New registration**.
1. Enter a **Name** for the application. For example, *testapp1*.
1. Select **Accounts in any organizational directory or any identity provider**.
1. Under **Redirect URI**, select **Web**, and then enter `https://jwt.ms` in the URL text box.
1. Under **Permissions**, select *Grant admin consent to openid and offline_access permissions* check box.
1. Select **Register**.

Once the application registration is complete, enable the implicit grant flow:

1. Under **Manage**, select **Authentication**.
1. Select **Try out the new experience** (if shown).
1. Under **IMPLICIT GRANT**, select both the **Access tokens** and **ID tokens** check boxes.
1. Select **Save**.