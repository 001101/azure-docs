---
title: Create a sensitivity label and related reports (Preview)
description: Learn how to create custom sensitivity labels for your content and scan your content for sensitive data.
author: batamig
ms.author: bagol
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: how-to
ms.date: 10/28/2020
---

# Create a sensitivity label scan your data

This article describes how to create custom sensitivity labels to apply to your data in Babylon, and then how to scan your content for sensitive data.

Babylon sensitivity labels are created in Microsoft 365. Sensitivity labels enable you to classify and protect your organization's data, while ensuring that user productivity and collaboration can continue to flow.

## What are sensitivity labels? 

To get work done, people in your organization collaborate with others both inside and outside the organization. Data doesn't always stay in your cloud, and often roams everywhere, across devices, apps, and services. 

When your cloud data roams, you want it to do so in a secure, protected way that meets your organization's business and compliance policies.

Sensitivity labels in Babylon enable you to:

- Enforce protection settings such as encryption or watermarks on labeled data stores. 

- Protect data in apps and services across different platforms. 

- Protect data stored in different formats such as Azure blob storage, Azure files, and SQL columns. 

- Automatically recognize labels on pre-labelled content.

- Classify data without using any protection settings. 

With sensitivity labels, you can classify data across your data estate and then enforce protection settings based on that classification.

## Create sensitivity labels in Microsoft 365

During public preview, sensitivity labels for Babylon are created and managed using Microsoft 365, so an active M365 license is required. Sensitivity labels created in Babylon are available for the following data types: 

**Automatic labeling of files:**
- Azure Blob Storage
- Azure files

**Automatic labeling of SQL columns:**
- SQL server
- Azure SQL database
- Azure SQL Database Manage instance

Once labeled, Babylon labeling reports provide deep insights on all of your labeled content within Babylon.   

## Create or modify a sensitivity label

If you don't already have sensitivity labels, you'll need to create them. Existing sensitivity labels will also need to be modified to include the new data asset types within Babylon. Perform both of these activities in Microsoft 365 Security and Compliance Center. 

Use of this feature requires an active M365 license. 

- **To create a new sensitivity label or modify an existing label:** 

    1. Open the Microsoft 365 Security and Compliance Center. 

    1. Under **Solutions**, select **Information protection**, then select **Create sensitivity label**. 

    1. Name the label, and in the Scope options, select **files and emails** and **SQL Columns**.
    
    :::image type="content" source="media/create-sensitivity-label/m365-create-label-scope.png" alt-text="Create your label in the Microsoft 365 Security and Compliance Center":::

- **To define autolabeling rules for files,** follow these [M365 autolabeling instructions](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps). 

    :::image type="content" source="media/create-sensitivity-label/m365-create-auto-labeling-rules-files.png" alt-text="Define autolabeling rules for files in the Microsoft 365 Security and Compliance Center":::

- **To define autolabeling rules for SQL columns:**

    1. Within the create sensitivity label wizard, under the **Azure asset** option, select the **Auto Labeling for Azure SQL Column** checkbox and add the relevant sensitive information types for the label. 
        
    :::image type="content" source="media/create-sensitivity-label/m365-create-auto-labeling-rules-sql.png" alt-text="Define autolabeling rules for SQL columns  in the Microsoft 365 Security and Compliance Center":::

    1. Save and close the modification or addition of your new label. 

## Search for files based on labels

Once you've modified or created new sensitivity labels, view the labels automatically applied to your content.

Use the **Label** filtering options to show files with specific labels only. For example: 

:::image type="content" source="media/create-sensitivity-label/filter-search-results.png" alt-text="Search for assets by label":::

In Babylon, files with sensitivity labeling are marked as **Microsoft extended**. For example:

- [Labeled files in Azure Blob Storage](#labeled-files-in-azure-blob-storage)
- [Labeled files in Azure files](#labeled-files-in-azure-files)
- [Labeled files in SQL tables](#labeled-files-in-sql-tables)

### Labeled files in Azure Blob Storage

:::image type="content" source="media/create-sensitivity-label/view-labeled-files-blob-storage.png" alt-text="View a sensitivity label on a file in your Azure Blob Storage":::

### Labeled files in Azure files

:::image type="content" source="media/create-sensitivity-label/view-labeled-files-azure.png" alt-text="View a sensitivity label on a file in your Azure file storage":::

### Labeled files in SQL tables

:::image type="content" source="media/create-sensitivity-label/view-labeled-files-sql.png" alt-text="View a sensitivity label on an SQL table":::

> [!NOTE]
> Sensitivity labels that are applied to specific SQL columns are displayed at the SQL table level.

## Next steps:

> [!div class="nextstepaction"]
> [Classification reporting](classification-insights.md)

> [!div class="nextstepaction"]
> [Create a custom classification](./create-a-custom-classification.md)