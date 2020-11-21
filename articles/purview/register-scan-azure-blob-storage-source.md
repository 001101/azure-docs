---
title: 'How to scan Azure storage blob'
description: Learn how to scan Azure blob storage in your Azure Purview data catalog. 
author: SunetraVirdi
ms.author: suvirdi
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: how-to
ms.date: 11/05/2020
---

# Register and scan Azure Blob Storage

This article outlines how to register an Azure Blob Storage account in Purview and set up a scan.

## Supported capabilities

Azure Blob Storage supports full and incremental scans to capture the metadata and apply classifications on the metadata, based on system and customer classifications.

## Prerequisites

- Before registering data sources, create an Azure Purview account. For more information on creating a Purview account, see [Quickstart: Create an Azure Purview account](create-catalog-portal.md).
- You need to be a Catalog Admin or Data Source Admin

## Register an Azure Blob Storage account

To register a new ADLS Gen1 account in your data catalog, do the following:

1. Navigate to your Purview Data Catalog.
1. Select **Management center** on the left navigation.
1. Select **Data sources** under **Sources and scanning**.
1. Select **+ New**.
1. On **Register sources**, select **Azure Data Lake Storage Gen1**. Select **Continue**.

:::image type="content" source="media/register-scan-azure-blob-storage-source/register-new-data-source.png" alt-text="register new data source" border="true":::

On the **Register sources (Azure Blob Storage)** screen, do the following:

1. Enter a **Name** that the data source will be listed with in the Catalog. 
1. Choose how you want to point to your desired storage account:
   1. Select **From Azure subscription**, select the appropriate subscription from the **Azure subscription** drop down box and the appropriate storage account from the **Storage account name** drop down box.
   1. Or, you can select **Enter manually** and enter a service endpoint (URL).
1. **Finish** to register the data source.

:::image type="content" source="media/register-scan-azure-blob-storage-source/register-sources.png" alt-text="register sources options" border="true":::

## Set up authentication for a scan

There are four ways to set up authentication for Azure blob storage:

- Managed Identity
- Account Key
- SAS URL
- Service Principal

### Managed Identity

When you choose **Managed Identity**, you must let Catalog's managed identity authenticate with your Azure Blob account. To set up the connection, you must first give your Catalog the permission to scan the data source. Please see steps in the article here.

### Account Key

When authentication method selected is **Account Key**, select **Enter manually** option and paste the access key from Azure Blob storage account within settings.

### SAS URL

To use a SAS URL, you need to generate one. To learn more about how to do this, see this [Storage SAS Overview](../storage/common/storage-sas-overview.md)

### Service principal

To use a service principal, you must first create one following these steps:

1. Navigate to the [Azure portal](https://portal.azure.com).

1. Select **Azure Active Directory** from the left-hand side menu.

1. Select **App registrations**.

1. Select **+ New application registration**.

1. Enter a name for the **application** (the service principal name).

1. Select **Accounts in this organizational directory only**.

1. For Redirect URI select **Web** and enter any URL you want; it doesn't
have to be real or work.

1. Then select **Register**.

1. Copy the values from both the display name and the application ID.

1. Add your service principal to a role on the Azure Blob storage account that you would like to scan. You do this in the Azure portal. For more information about service principals, see [Acquire a token from Azure AD for authorizing requests from a client application](../storage/common/storage-auth-aad-app.md)

1. Once your Service Principal is set, connect your Purview to your Azure Blob store using client ID and secret key and check your connect as shown in the screenshot below.

   :::image type="content" source="./media/register-scan-azure-blob-storage-source/service-principal-auth.png" alt-text="Screenshot showing service principal authorization":::

## Firewall setting in Azure Blob Storage account

> [!NOTE]
> This applies to authorization using Managed Identity only

1. Go into your storage account in Azure portal, **click on Settings > Firewalls and virtual networks** and select **Allow access from Selected Networks**.
1. In the **Firewall** section, select **Allow trusted Microsoft services to access this storage account** and hit **Save**

:::image type="content" source="./media/register-scan-azure-blob-storage-source/firewall-setting.png" alt-text="Screenshot showing firewall setting":::

## Create and run a scan

### Scope your scan

The next screen here is to scope the scan. Please select the folders you want to scan and select continue (by default all the folders will be selected)

:::image type="content" source="./media/register-scan-azure-blob-storage-source/scope-scan.png" alt-text="Screenshot scope scans":::

The next screen is where you set your scan trigger, telling the system how often you would like to scan.

> [!NOTE]
> Once means no schedule, which is an indication to the system that the scan should only run once.

Here are some examples of triggers that are set up on a monthly cadence below. You can select the time it starts at and define the recurrence for a particular day of the month, and a time on that day of your choosing. You can also choose to specify an end date or not (meaning the recurrence of the scan will happen indefinitely).

You can also set up a trigger on a weekly cadence with an option to choose the day of the week.

### Set scan rule set

Select a scan rule set to be used by your scan from the list of available

:::image type="content" source="./media/register-scan-azure-blob-storage-source/select-scan-rule-set.png" alt-text="Screenshot showing scan rule set":::

### Review your scan

When you click Continue, you will be presented with scan summary page, where you can view all the settings for your scan.

### Edit a scan

Select a scan and click **Edit** to edit the selected scan. You can only edit one scan at a time.

### Remove a scan

To remove a scan, select one or more scans from the list, then select **Remove**.

### Scan history

Select any scan in the list to get to the scan history page. This page will show you whether your scan was scheduled or manual, how many assets had classifications applied, how many total assets were discovered, the start and end time of the scan and the total duration.

### Run a scan manually

From the **Scan History page**, you can choose **Run Scan now** to launch a new scan immediately. This action will run a full scan, not an incremental scan.

### Cancel scans in progress

Select one or more scans that are in progress by selecting the checkbox for each.

Then select **Cancel Scan** to stop all the selected scans from running.

## Next steps

- [Browse the Azure Purview Data catalog](how-to-browse-catalog.md)
- [Search the Azure Purview Data Catalog](how-to-search-catalog.md)
