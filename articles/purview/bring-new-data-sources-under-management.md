---
title: Bring new data sources under management
description: Learn how to add, view, edit, and delete data sources from the Purview Management Center.
author: chandrakavya
ms.author: kchandra
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: how-to
ms.date: 10/12/2020
---
# Bring new data sources under management

In this article, you learn how to add, view, edit, and delete data sources from the Purview Management Center. A data source is the top-level data store you want to scan, such as:

- An ADLS Gen1 account.
- An ADLS Gen2 account.
- An Azure Blob Storage account.
- An Azure Data Explorer cluster.
- An Azure File Storage account.
- An Azure SQL Server.
- A CosmosDB instance.

Data sources must be registered before they can be managed in **Management Center**.

## View data sources

Select **Management Center** in the left navigation pane.

:::image type="content" source="media/bring-new-data-sources-under-management/select-management-center.png" alt-text="Three Purview options are shown, with Management Center highlighted." border="true":::

The **Data sources** page appears.

:::image type="content" source="media/bring-new-data-sources-under-management/data-sources-page.png" alt-text="The 'Data sources' dialog box has options to add and edit data sources. It also has a list of data sources with check boxes, none of them checked." border="true":::

## Add a new data source

1. Select **+ New** to add a new data source.

    :::image type="content" source="media/bring-new-data-sources-under-management/add-new-data-source.png" alt-text="The left pane is the navigation pane. 'Data sources' is selected, and the right pane is the 'Data sources' dialog box. The + New option is highlighted, and data sources are listed, each with a check box. None is checked." border="true":::

1. **New data source** appears with a list of data source types. Select a type, and then select **Continue**.

    :::image type="content" source="media/bring-new-data-sources-under-management/data-sources-list.png" alt-text="The 'New data source' dialog box is a menu of various data sources, such as Azure blob storage." border="true":::

1. Enter a name for your data source, and specify which one to add, in this case which Azure blob. You can use the drop-down lists to choose an instance to add, or you can specify the URI of your data instance, if it's in a different subscription.

    :::image type="content" source="media/bring-new-data-sources-under-management/new-azure-blob-storage.png" alt-text="The New Azure Blob Storage dialog box has AzureBlobStorage for 'Friendly name'. The 'Enter manually' radio button is selected. Nothing is entered for Endpoint." border="true":::

1. Your selection now appears in the list of data sources.

## Edit a data source

1. Select the check box for the data source you want to edit.

    :::image type="content" source="media/bring-new-data-sources-under-management/select-data-source.png" alt-text="The 'Data Sources' dialog box has the AzureBlobStorage data source checked." border="true":::

1. Select **Edit** to edit the data source.

    :::image type="content" source="media/bring-new-data-sources-under-management/edit-data-source.png" alt-text="In the Data Sources dialog box, the Edit button is highlighted." border="true":::

## Delete data sources

To delete one or more data sources, select their check boxes.

:::image type="content" source="media/bring-new-data-sources-under-management/delete-data-source.png" alt-text="The Data Sources dialog box has the Delete button highlighted. Two data sources are selected." border="true":::

Then select **Delete**.

## Next steps

For information on registering data sources of various types, and on creating scans, see: 

- [Azure Files](register-scan-azure-files-storage-source.md)
- [Azure Blob Storage](register-scan-azure-blob-storage-source.md)
