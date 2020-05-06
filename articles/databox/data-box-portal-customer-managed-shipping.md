---
title: Microsoft Azure Data Box self managed shipping | Microsoft Docs in data 
description: Describes self managed shipping workflow for Azure Data Box devices
services: databox
author: priestlg

ms.service: databox
ms.subservice: disk
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: v-grpr
---

# Use self managed shipping for Azure Data Box in the Azure portal

This article describes self managed shipping tasks to order, pickup, and drop off of an Azure Data Box device. You can manage the Data Box device using the Azure portal. You have the option of designating a point of contact from your organization, or use any carrier of your choice to pick up the Azure Data Box device from a datacenter and also return the device back to the datacenter. You need to follow the datacenter's security procedures as sign off is required at particular points between pickup and drop-off. At pickup and drop-off, you, or your point of contact will be assigned an authorization code. This authorization code needs to be presented during device pick up and drop-off at the datacenter.

## Prerequisites

Your Azure Data Box order was created for one of the following regions:

1. Western Europe
2. Japan
3. Singapore
4. South Korea
5. India

For detailed information on how to create a Data Box order, see [Tutorial: Order Azure Data Box](data-box-deploy-ordered.md).

## Choose self managed shipping for Data Box order

When you place a Data Box order, you can choose self managed shipping option.

1. When choosing shipping type, if you are located in a supported region, you will have the option of selecting **Self-managed shipping**.

   ![Self-managed shipping](media\data-box-portal-customer-managed-shipping\customer-managed-shipping-ship-option.png)

2. After you fill out the fields, you will need to validate your email address.

3. Ready for Pickup in progress. You need to click on **Schedule Pickup**.

   ![Ordering a Data Box device for pickup](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01b.png)

4. Follow the instructions in the **Schedule pickup for Azure**. Before you can get your authorization code, you must email [adbops@microsoft.com](mailto:adbops@microsoft.com) to schedule the device pickup from your region's datacenter.
   ![Schedule pickup](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-02b.png)

5. After you have made contact with ADBOPs, you will be able to view your **Authorization Code** for your device in the **Schedule pickup for Azure**.
   ![Viewing your authorization code](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   Write down this number or print it.

6. Ready for pickup in progress.
   Your order automatically moves to this state once the device prep has completed. You will receive an email instructing you to contact ADB ops to schedule an appointment to come to the datacenter for pickup.

   ![Picked up](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-01.png)

   You also need to provide details of who will be going to the datacenter for pickup. You or the point of contact has to carry a Government approved photo ID that will be validated at the datacenter. Operations will need to include this person's details so that it can be verified during pickup.

   Additionally, the person who is picking up the device also needs to have the **Authorization code** that is available in the Portal under **Schedule Pick up**. This code is also validated at the datacenter time of pick up.

7. After the device has been picked up, the customer needs to prepare the data copy and then drop off the device back at the datacenter.

   Once you finish copying data, you need to successfully complete **Prepare to ship** step on the device. This step will need to complete without any critical errors, otherwise you will need to run this step again. If it successfully completes, the authorization code for the drop off will be shown to the customer.

   > [!NOTE]
   >
   > The authorization code will no longer be available on the portal once **Prepare to ship** is globally enabled on all orders. It will only be available on the device local UI.

8. You should now be at **Ready to receive success** state of the Azure portal. This means that you have scheduled an appointment for drop off and shared the drop off point of contact's details. They have also confirmed that they have the authorization code available.
   ![Viewing your authorization code](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

9. Customer or point of contact has dropped off the device at the datacenter and their ID and authorization code have been verified.

    ![Received Complete](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

10. From this point on Microsoft will copy your data to their servers. The Azure portal should be at the **Copy in progress**.

    ![Data Copy](media\data-box-portal-customer-managed-shipping\data-box-copy-data-01.png)

11. When your data has finished copying successfully, the Azure portal will indicate that your data copy is complete.

    ![Data Copy Complete](media\data-box-portal-customer-managed-shipping\data-box-copy-data-complete-01.png)

## Next steps

- [Get started with Azure Data Box](data-box-quickstart-portal.md)
