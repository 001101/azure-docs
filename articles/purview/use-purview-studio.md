---
title: "Quickstart: Use Purview Studio"
description: This quickstart describes how to use a purview studio. 
author: nayenama
ms.author: nayenama
ms.service: data-catalog
ms.subservice: data-catalog-gen2
ms.topic: quickstart
ms.date: 11/12/2020

---

# Quickstart: Use Purview Studio

In this quickstart, you can learn how to use Purview Studio.

## Prerequisites

* An Active Purview account is already created in Azure portal and the user has permissions to access Purview Studio.

## Launch purview account

* To Launch Purview account go to Purview accounts in Azure portal, select the account you want to launch and launch the account.

   :::image type="content" source="./media/use-purview-studio/launch-from-portal.png" alt-text="Screenshot of the selection to launch the Azure Purview account catalog.":::

* Another way to launch Purview account is to go to https://web.purview.azure.com and select Azure active directory and account name to launch the account.

## Home page

**Home** is the starting page for the Azure Purview client.

 :::image type="content" source="./media/use-purview-studio/purview-homepage.png" alt-text="Screenshot of the homepage.":::

The following list summarizes the main features of **Home**. Each number in the list corresponds to a highlighted number in the preceding screenshot.

1. Friendly name of the catalog. You can set catalog name in **Management Center** > **Account information*.

2. Catalog analytics shows the number of:
    - Users, groups, and applications
    - Data sources
    - Assets
    - Glossary terms

3. The search box allows you to search for data assets across the data catalog.

4. The quick access buttons give access to frequently used functions of the application. The buttons which are presented, depend on the role assigned to your user account.

    - For *data source administrator*, the quick access buttons are: **Register Data Sources** and **Knowledge Center**.
    - For *data curator*, the buttons are **Knowledge Center**, **Browse Assets**, **Manage Glossary** and **View Insights**.
    - For *data reader*, the featured buttons are **Knowledge Center**, **Browse Assets**, **View Glossary** and **View Insights**.

5. The left navigation bar helps you locate the main pages of the application. The buttons which are presented, depend on the role assigned to your user account.

    - For *data source administrator*, the buttons are  **Home**, **Sources** and **Management Center**
    - For *data curator*, the buttons are **Home**, **Glossary**, **Insights and Management center**
    - For *data reader*, the buttons are **Home**, **Glossary**, **Insights and Management center**
  
6. The **Recently accessed** tab shows a list of recently accessed data assets. For information about accessing assets, see [Search the catalog for assets](#search-the-catalog-for-assets) and [Browse by asset type](#browse-by-asset-type).  **My items** tab is a list of data assets owned by the logged-on user.
7. **Useful Links** contains links to region status, documentation, pricing, overview, and purview status
8. The top navigation bar contains information about release notes/updates, change purview account, notifications, help, and feedback sections

## Knowledge Center

Knowledge center is where you can find all the videos and tutorials related to Purview.

## Guided Tours

Each UX in Azure Purview Studio will have guided tours to give overview of the page. To start the guided tour, select **help** on the top bar and select **guided tours**.


 :::image type="content" source="./media/use-purview-studio/guided-tour.png" alt-text="Screenshot of the guided tour.":::

> [!div class="nextstepaction"]
> [Add a security principal](starter-kit-tutorial-1.md)