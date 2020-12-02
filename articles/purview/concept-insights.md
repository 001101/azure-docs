---
title: Understand Insights reports in Azure Purview
description: This article explains what Insights are in Azure Purview.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/22/2020
---

# Understand Insights in Azure Purview

This article provides an overview of the Insights feature in Azure Purview. 

## Insights

Insights are one of the key pillars of Purview. The feature provides customers, a single pane of glass view into their catalog and further aims to provide specific insights to the data source administrators, business users, data stewards, data officer and, security administrators. Currently, Purview has the following Insights reports that will be available to customers at public preview.

### Asset Insights
This report gives a bird's eye view of your data estate, and its distribution by source type, by classification and by file size as some of the dimensions. This report caters to different types of users who may be managing the Purview account and running scans or business users who may be interested to know how many assets exist with a certain classification within their organization's data estate. 

The report provides broad insights through graphs and KPIs and later deep dive into specific anomalies such as misplaced files. The report also supports an end-to-end customer experience, where customer can view count of assets with a specific classification, can breakdown the information by source types and top folders, and can also view the list of assets for further investigation.

### Scan Insights
The report enables administrators to understand overall health of the scans - how many succeeded, how many failed, how many canceled. This report gives a status update on scans that have been executed in the Purview account within a time period of last seven days or last 30 days.

The report also allows administrators to deep dive and explore which scans failed and on what specific source types. To further enable users to investigate, the report helps them navigate into the scan history page within the "Sources" experience.

### Glossary Insights
This report is gives the business users and data stewards a status report on glossary. Users can view this report to understand distribution of glossary terms by status, learn how many glossary terms are attached to assets and how many are not yet attached to any asset. Business users can also learn about completeness of their glossary terms. 

This report summarizes top items that a business user or data steward needs to focus on, to create a complete and usable glossary for his/her organization. Users can also navigate into the "Glossary" experience from "Glossary Insights" experience, to make changes on a specific glossary term.

### Classification Insights
This report provides details about where classified data is located, the classifications found during a scan, and a drilldown to the classified files themselves. It enables security administrators to understand the types of information found in their organization's data estate. 

In Azure Purview, classifications are similar to subject tags, and are used to mark and identify content of a specific type in your data estate.

Use the Classification Insights report to identify content with specific classifications and understand required actions, such as adding additional security to the repositories, or moving content to a more secure location.

For more information, see [Classification insights about your data from Azure Purview](classification-insights.md).

### Sensivitiy Labeling Insights
This report provides details about the sensitivity labels found during a scan, as well as a drilldown to the labeled files themselves. It enables security administrators to ensure the security of information found in their organization's data estate. 

In Azure Purview, sensitivity labels are used to identify classification type categories, as well as the group security policies that you want to apply to each category.

Use the Labeling Insights report to identify the sensitivity labels found in your content and understand required actions, such as managing access to specific repositories or files.

For more information, see [Sensitivity label insights about your data in Azure Purview](sensitivity-insights.md).

### File Extension Insights
This report provides details about the file extensions, or file types, found during a scan, as well as a drilldown to the files themselves. It enables administrators to understand how many files of each time you have, where those files are, and whether they are scannable for sensitive data.

1. On the left, select **File extensions** to view insights about the file types (extensions) found in your content, over a maximum of the last 30 days.

    |Option  |Description  |
    |---------|---------|
    |**Time selector**     | Adjust the time selector as needed to show updated results found over different time periods.        |
    |**Unique file extensions found**     | Use the tile at the top to view the number of unique file extensions found across your content.       |
    |**Top file extensions**     |  Use the **Top file extensions** graph to view the most commonly found file extensions in your content.       |
    | | |
    
1. Click **View more** to drill down further.

    On the file extension analysis report, use any of the following steps to modify the data displayed:
    
    |Option  |Description  |
    |---------|---------|
    |**Edit columns**     | Select **Edit Columns** to change the column data shown in the table below your graphs.        |
    |**Filter**     |   Use the **Filter by keyword**, **Time period**, **File extension**, and **Sources**, and **Content scanning** filters to filter the graphs to show data for specific content only.      |
    | | |

For more information, see [File extension insights about your data from Azure Purview](file-extension-insights.md).
## Next steps

[Tutorial on how to view Insights in Purview](starter-kit-tutorial-6.md)

