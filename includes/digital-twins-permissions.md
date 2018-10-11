---
 title: include file
 description: include file
 services: azure-digital-twins
 author: alinamstanciu
 ms.service: azure-digital-twins
 ms.topic: include
 ms.date: 09/19/2018
 ms.author: alinast
 ms.custom: include file
---

1. In the [Azure portal](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps), click **New application registration**.
    
    ![Azure Active Directory app registration new](./media/digital-twins-permissions/aad-app-reg-start.png)

1. Give a friendly name for this app registration in the **Name** field. Choose **Application type** as **_Native_**, and **Redirect URI** as **_https://microsoft.com_**. Click **Create**.

    ![Azure Active Directory app registration create](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Once complete, copy the value of the **Application ID** field (see image below) to a temporary file; this value identifies your Azure Active Directory app. You will use Application ID to configure your sample application in the proceeding sections.

1. Open your app registration pane, and click **Settings** > **Required permissions**:
    - Click **Add** on the top left to open the **Add API access** pane.
    - Click **Select an API**, search for **Azure Smart Spaces** API, select the **Azure Digital Twins (Azure Smart Spaces Service)** and click **Select**.

        ![Azure Active Directory app registration add api](./media/digital-twins-permissions/aad-app-reg-service.png)

    - Click **Select permissions**. Check the **Read/Write Access** delegated permissions box, and click **Select**.
    - Click **Done** in the **Add API access** pane.
    - In the **Required permissions** pane, click the **Grant permissions** button, and accept the acknowledgement that appears.

       ![Azure Active Directory app registration add api](./media/digital-twins-permissions/aad-app-req-permissions.png)

1. Close your app registration pane, and navigate back to your [**Azure Active Directory - Properties**](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties), and copy the **Directory ID** to a temporary file. You will use Directory ID to configure sample application in the proceeding sections.

    ![Azure Active Directory app registration sixth step](./media/digital-twins-permissions/aad-app-reg-tenant.png)
