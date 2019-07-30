---
title: View bill in Azure IoT Central application | Microsoft Docs
description: As an administrator, how to view bill in your Azure IoT Central application
author: v-krghan
ms.author: v-krghan
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
---

# View your bill in IoT Central application

After you create an IoT Central application, you can go to the **Administration** section to view your bill.

To access and use the **Administration** section, you must be in the **Administrator** role for an Azure IoT Central application. If you create an Azure IoT Central application, you're automatically assigned to the **Administrator** role for that application. The [View your bill](#view-your-bill) section in this article explains more about how to view your bill in the Azure IoT Central application.


## View your bill

To view your bill, go to the **Billing** page in the **Administration** section. The Azure billing page opens in a new tab, where you can see the bill for each of your Azure IoT Central applications.

### Convert your Trial to Pay-As-You-Go

You can convert your Trial application to a Pay-As-You-Go application. Here are the differences between these types of applications.

- **Trial** applications are free for seven days before they expire. They can be converted to Pay-As-You-Go at any time before they expire.
- **Pay-As-You-Go** applications are charged per device, with the first five devices free.

Learn more about pricing on the [Azure IoT Central pricing page](https://azure.microsoft.com/pricing/details/iot-central/).

To complete this self-service process, follow these steps:

1. Go to the **Billing** page in the **Administration** section.

    ![Trial state](media/howto-administer/freetrialbilling.png)

1. Select **Convert to Pay-As-You-Go**.

    ![Convert trial](media/howto-administer/convert.png)

1. Select the appropriate Azure Active Directory, and then the Azure subscription to use for your Pay-As-You-Go application.

1. After you select **Convert**, your application is now a Pay-As-You-Go application and you start getting billed.

## Next steps

Now that you've learned how to view your bill in Azure IoT Central application, here is the suggested next step:

> [!div class="nextstepaction"]
> [Set up the device template](howto-set-up-template.md)
