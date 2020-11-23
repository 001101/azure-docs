---
title: 'Register and scan Azure Data Lake Storage (ADLS) Gen2'
description: This tutorial describes how to scan Azure Data Lake Storage Gen2. 
author: prmujumd
ms.author: prmujumd
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: how-to
ms.date: 11/17/2020
# Customer intent: As a data steward or catalog administrator, I need to understand how to scan data from Azure Data Lake Storage Gen2 into the catalog.
---
# Register and scan Azure Data Lake Storage Gen2

This article outlines how to register Azure Data Lake Storage Gen2 as data source in Azure Purview and set up a scan on it.

## Supported capabilities

The Azure Data Lake Storage Gen2 data source supports the following functionality:

- **Full and incremental scans** to capture metadata and classification in Azure Data Lake Storage Gen2

- **Lineage** between data assets for ADF copy/dataflow activities

## Prerequisites

Before registering data sources, create an Azure Purview account. For more information on creating a Purview account, see [Quickstart: Create an Azure Purview account](create-catalog-portal.md).

## Register Azure Data Lake Storage Gen2 data source

To register a new ADLS Gen2 account in your data catalog, do the following:

1. Navigate to your Purview Data Catalog.
1. Select **Management center** on the left navigation.
1. Select **Data sources** under **Sources and scanning**.
1. Select **+ New**.
1. On **Register sources**, select **Azure Data Lake Storage Gen2**. Select **Continue**.

:::image type="content" source="media/register-scan-adls-gen2/register-new-data-source.png" alt-text="register new data source" border="true":::

On the **Register sources (Azure Data Lake Storage Gen2)** screen, do the following:

1. Enter a **Name** that the data source will be listed with in the Catalog.
1. Choose how you want to point to your desired storage account:
   1. Select **From Azure subscription**, select the appropriate subscription from the **Azure subscription** drop down box and the appropriate storage account from the **Data Lake Store account name** drop down box.
   1. Or, you can select **Enter manually** and enter a service endpoint (URL).
1. **Finish** to register the data source.

:::image type="content" source="media/register-scan-adls-gen2/register-sources.png" alt-text="register sources options" border="true":::

## Setting up authentication for a scan

The following authentication methods are supported for Azure Data Lake Storage Gen2:

- Managed Identity
- Service principal
- Account Key

### Authentication using the Data Catalog's MSI

For ease and security, you may want to use the Catalog's Managed Service Identity (MSI) to authenticate with your data store.

To set up a scan using the Data Catalog's Managed Identity, you must *first* give it permissions (meaning add the identity to the correct role) to whatever resources you are trying to scan. This step must be done *before* you set up your scan or your scan will fail.

### Adding the catalog MSI to an Azure Data Lake Gen2 account

You can add the Catalog's MSI at the Subscription, Resource Group, or Resource level, depending on what you want it to have scan permissions on.

> [!Note]
> You need to be an owner of the subscription to be able to add a managed identity on an Azure resource.

1. From the [Azure portal](https://portal.azure.com), find either the
    Subscription, Resource Group, or Resource (for example, an Azure Data Lake Storage Gen2 storage
    account) that you would like to allow the catalog to scan.

1. Choose **Access control (IAM)**.

   :::image type="content" source="./media/register-scan-adls-gen2/access-control.png" alt-text="Choose access control":::

1. The **Add role assignment** page opens.

   :::image type="content" source="./media/register-scan-adls-gen2/add-role-assignment.png" alt-text="Add role assignment":::

1. Fill in the form as follows:

   1. Under **Role**, Choose **Storage Blob Data Reader** from the list.

      :::image type="content" source="./media/register-scan-adls-gen2/storage-blob-data-reader.png" alt-text="Storage Blob Data Reader":::

   1. In the **Assign access to** box, select **Azure AD user, group, or service principal**. It should be the default option.

      :::image type="content" source="./media/register-scan-adls-gen2/assign-access.png" alt-text="Assign access ":::

   1. In the **Select** box, start typing the name of *your* catalog and you should see it in the list for you to select.

      :::image type="content" source="./media/register-scan-adls-gen2/select-catalog.png" alt-text="Select your catalog":::

   1. Select **Save**.

> [!Note]
> Once you have added the catalog's MSI on the data source, wait up to 15 minutes for the permissions to propagate before setting up a scan.

After about 15 minutes, the catalog should have the appropriate permissions to be able to scan the resource(s).

### Authentication using account key

If you choose to use the account key for authorization, use the Azure portal to search for your data source, and select **Settings** > **Access keys**, copy the first key in the list.

### Authentication using service principal

To use a service principal, you must first create one, by following these steps:

1. Navigate to [Azure portal](https://portal.azure.com).

1. Select **Azure Active Directory** from the left-hand side menu.

1. Select **App registrations**.

1. Select **+ New application registration**.

1. Enter a name for the **application** (the service principal name).

1. Select **Accounts in this organizational directory only (Microsoft
only - Single Tenant)**.

1. For **Redirect URI** select **Web** and enter any URL you want; it doesn't have to be real or work.

1. Then select **Register**.

1. Copy the values for both the display name and the application ID.

1. Add your service principal to a role on the data stores that you would like to scan. Do this in the Azure portal.

   For more information about service principals, see [Acquire a token from Azure AD for authorizing requests from a client application](../storage/common/storage-auth-aad-app.md).

## Creating and running a scan

1. Navigate to the management center. Select **Data sources** under **Sources and scanning**, then select the **Azure Data Lake Storage Gen2** data source.

2. Select **+ New scan**. Choose the authentication method that you want to use and provide required details.

   :::image type="content" source="media/register-scan-adls-gen2/choose-authentication.png" alt-text="Choose scan authentication method":::

3. If you plan to scan the entire server, we do not provide scope functionality. If you did however provide a database name, then you can scope your scan to specific tables.

4. Choose your scan trigger. You can set up a schedule or ran the scan once.

   :::image type="content" source="media/register-scan-adls-gen2/setup-scan-trigger.png" alt-text="Setup scan trigger":::

5. The select a scan rule set for you scan. You can choose between the system default, the existing custom ones or create a new one inline.

   :::image type="content" source="media/register-scan-adls-gen2/select-scan-rule-set.png" alt-text="Select scan rule set":::

6. Review your scan and select **Save** and run.

## Viewing your scans and scan runs

1. Navigate to the management center. Select **Data sources** under the **Sources and scanning** section select the **Azure Data Lake Storage Gen2** data source.

2. Select the scan result that you want to view.

3. You can view all the scan runs along with metrics and status for each scan run.

## Manage your scans

1. Navigate to the management center. Select **Data sources** under the Sources and scanning, select the **Azure Data Lake Storage Gen2** data source.

2. Select the scan you would like to manage. You can edit the scan by select **Edit**.

   :::image type="content" source="media/register-scan-adls-gen2/edit-scan.png" alt-text="edit scan":::

3. You can delete your scan by selecting **Delete**.

## Next steps

- [Browse the Azure Purview Data catalog](how-to-browse-catalog.md)
- [Search the Azure Purview Data Catalog](how-to-search-catalog.md)
