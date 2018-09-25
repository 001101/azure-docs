---
title: Using Managed Identities to Authenticate Azure Stream Analytics Jobs to Azure Data Lake Storage Gen 1 Output (Preview)
description: 
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/25/2018
---

# Using Managed Identities to Authenticate Azure Stream Analytics Jobs to Azure Data Lake Storage Gen 1 Output (Preview)

Azure Stream Analytics supports managed identity authentication with Azure Data Lake Storage Gen 1 output. The identity is a managed application registered in Azure Active Directory that represents a given Stream Analytics job, which can be used to authenticate to a targeted resource. Managed identities eliminate the limitations of the user-based authentication method, like needing to re-authenticate due to password changes or user token expirations that occur every 90 days. Additionally, it allows the automation of Stream Analytics job deployments that output to Azure Data Lake Storage Gen 1.

Visit the [Azure portal](https://ms.portal.azure.com/?feature.canmodifyextensions=true&Microsoft_Azure_StreamAnalytics_msiSettings=true#blade/HubsExtension/ArtBrowseBlade/resourceType/Microsoft.Resources%2Fresources) to preview the Azure Stream Analytics managed identity for Azure Data Lake Storage feature.

This article shows you two ways to enable managed identity for an Azure Stream Analytics job that outputs to an Azure Data Lake Storage Gen 1: using the Azure portal and using Azure Resource Manager (ARM) template deployment. 

## Enabling Managed Identity for a Stream Analytics job using Azure portal:

1. Start by creating a new Stream Analytics job or by opening an existing. From the menu bar located on the left side of the screen, select **Managed Identity (preview)**, an option that is available under **Configure**.

![Configure Stream Analytics managed identity preview](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Select **Use System-assigned Managed Identity (preview)** from the window that appears on the right and click **Save**. A service principal is created for the identity of the Stream Analytics job in Azure Active Directory. The life cycle of the newly created identity will be managed by Azure. When the Stream Analytics job is deleted, the associated identity (i.e., the Service Principal) is automatically deleted by Azure.

When the configuration is saved, the Object ID (OID) of the service principal is listed as the Principal ID as shown below:

![Stream Analytics Principal ID](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
The service principal has the same name as the Stream Analytics job. For example, if the name of your job is **MyASAJob**, the name of the service principal created is also **MyASAJob**.

3. In the output properties window of the Azure Data Lake Storage (ADLS) Gen 1 output sink, click the Authentication mode drop-down, and select **Managed Identity (preview)**.

4. Fill out the rest of the properties. To learn more about creating an ADLS output, see [Create a Data lake Store output with stream analytics](../data-lake-store/data-lake-store-stream-analytics.md). When you are finished, click **Save**.

![Configure Azure Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Navigate to the Overview page of your Data Lake Storage Gen1 and click on **Data explorer**.

![Configure Data Lake Storage Overview](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. In the Data explorer pane, select **Access**, and click **Add** in the Access pane.

![Configure Data Lake Storage Access](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. In the text box on the **Select user or group** pane, type the name of the service principal. Remember that the name of the service principal is the name of the corresponding Stream Analytics job. As you begin typing the principal name, it will appear below. Choose the service principal name and click **Select**.

![Select a service principal name](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. In the **Permissions** pane, check the **Write** and **Execute** permissions and assign it to **This Folder and all children**. Click **Ok**.

![Select a permissions](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. The Service Principal is listed under Assigned Permissions on the Access Pane as shown below. You can now go back and start your Stream Analytics job.

![Access list](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

To learn more about Data Lake Storag Gen 1 file system permissions, please see [Access Control in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md).

## Azure Resource Manager (ARM) template deployment

1. You can create a *Microsoft.StreamAnalytics/streamingjobs* resource with a managed identity by including the following property in the resource section of your Azure Resource Manager (ARM) template:

```json
"Identity": {
"Type": "SystemAssigned",
},
```

This property tells ARM to create and manage the identity for your Azure Stream Analytics job.

### Sample job json

```json
{ 
"Name": "AsaJobWithIdentity", 
"Type": "Microsoft.StreamAnalytics/streamingjobs", 
"Location": "West US",
"Identity": {
  "Type": "SystemAssigned", 
  }, 
"properties": {
  "sku": {
    "name": "standard"
    },
"outputs": [
      {
        "name": "string",
        "properties": {
          "datasource": {      	
"type": "Microsoft.DataLake/Accounts",
      		"properties": {     
         	  "accountName": “myDataLakeAccountName",
	      	  "filePathPrefix": “cluster1/logs/{date}/{time}",
	      	  "dateFormat": "YYYY/MM/DD",
	      	  "timeFormat": "HH",
		  "authenticationMode": "Msi"
	  }
    }
}
```
  
### Sample job response

```json
{ 
"Name": "mySAJob", 
"Type": "Microsoft.StreamAnalytics/streamingjobs", 
"Location": "West US",
"Identity": {
"Type": "SystemAssigned",
	"principalId": "GUID", 
	"tenantId": "GUID", 
}, 
"properties": {
        "sku": {
          "name": "standard"
        },
}
```

Take note of the Principal ID from the job response to grant access to the required ADLS resource.

The **Tenant ID** is the ID of the Azure Active Directory tenant where the service principal is created. The service principal is created in the Azure tenant that is trusted by the subscription.

The **Type** indicates the type of managed identity as explained in types of managed identities. Only the System Assigned type is supported.

2. Provide Access to the Service Principal using PowerShell. To give access to the Service Principal via PowerShell, execute the following command:

```powershell
Set-AzureRmDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
```

The **PrincipalId** is the Object ID of the service principal and is listed on the portal screen once the service principal is created. If you created the job using an ARM template deployment, then the Object ID is listed in the Identity property of the job response.

### Example

```powershell
PS > Set-AzureRmDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
```

To learn more about the above power shell command, please refer to the [Set-AzureRmDataLakeStoreItemAclEntry](https://docs.microsoft.com/powershell/module/azurerm.datalakestore/set-azurermdatalakestoreitemaclentry?view=azurermps-6.8.1&viewFallbackFrom=azurermps-4.2.0#optional-parameters) documentation.

## Next steps