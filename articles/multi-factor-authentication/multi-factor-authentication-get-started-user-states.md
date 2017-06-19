---
title: Microsoft Azure Multi-Factor Authentication User States
description: Learn about user states in Azure MFA.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila

ms.assetid: 0b9fde23-2d36-45b3-950d-f88624a68fbd
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
---

# How to require two-step verification for a user or group

There are two approaches for requiring two-step verification. The first option is to enable each individual user for Azure Multi-Factor Authentication. When users are enabled this way, they are always required to perform two-step verification (with some exceptions, like when they sign in from trusted IP addresses or if the remembered devices feature is turned on). The second option is to set up a conditional access policy that requires two-step verification under certain conditions.

## Enable Azure MFA by changing user states

User accounts in Azure Multi-Factor Authentication have the following three distinct states:

| State | Description | Non-browser apps affected |
|:---:|:---:|:---:|
| Disabled |The default state for a new user not enrolled Azure Multi-Factor Authentication (MFA). |No |
| Enabled |The user has been enrolled in Azure MFA, but has not registered. They will be prompted to register the next time they sign in. |No.  They continue to work until the registration process is completed. |
| Enforced |The user has been enrolled and has completed the registration process for Azure MFA. |Yes.  Apps require app passwords. |

### Changing a user state
A user's state reflects whether an admin has enrolled them in Azure MFA, and whether they completed the registration process.

All users start out *disabled*. When you enroll users in Azure MFA, their state changes *enabled*. When enabled users sign in and complete the registration process, their state changes to *enforced*.  

#### View user states

Use the following steps to access the page where you can view and manage user states:

1. Sign in to the [Azure portal](https://portal.azure.com) as an administrator.
2. Go to **Azure Active Directory** > **Users and groups** > **All users**.
3. Select **Multi-Factor Authentication**.
   ![Select Multi-Factor Authentication](./media/multi-factor-authentication-get-started-user-states/selectmfa.png)
4. A new page, which displays the user states, opens.
   ![multi-factor authentication user status - screenshot](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

#### Change the state from disabled to enabled

1. Use the preceding steps to get to the multi-factor authentication users page.
2. Find the user that you want to enable for Azure MFA. You may need to change the view at the top. Ensure that the status is **disabled**.
   ![Find user - screenshot](./media/multi-factor-authentication-get-started-cloud/enable1.png)
3. Check the box next to their name.
4. On the right, under quick steps, click **Enable**.
   ![Enable selected user - screenshot](./media/multi-factor-authentication-get-started-cloud/user1.png)
5. Select **enable multi-factor auth**.
   ![Enable multi-factor auth - screenshot](./media/multi-factor-authentication-get-started-cloud/enable2.png)
6. Notice the user's state has changed from **disabled** to **enabled**.
   ![See that user is now enabled - screenshot](./media/multi-factor-authentication-get-started-cloud/user.png)

After you enable users, you should notify them via email. Include the fact that they'll be asked to register the next time they sign in, and that some non-browser apps may not work with two-step verification. You can also include a link to our [Azure MFA end-user guide](./end-user/multi-factor-authentication-end-user.md) to help them get started.

#### Change the state from enabled/enforced to disabled

1. Use the steps in [View user states](#view-user-states) to get to the multi-factor authentication users page.
6. Find the user that you want to disable. You may need to change the view at the top. Ensure that the status is either **enabled** or **enforced**.
7. Check the box next to their name.
8. On the right, under quick steps, click **Disable**.
   ![Disable user - screenshot](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
9. You are prompted to confirm the action. Click **Yes**.
10. If the user was successfully disabled, you receive a success message. Click **Close**.

## Create a conditional access policy

Conditional access is a paid feature of Azure Active Directory, with many possible configuration options. These steps are one way to create a policy. For more information, read about [Conditional Access in Azure Active Directory](../active-directory/active-directory-conditional-access-azure-portal.md).

1. Sign in to the [Azure portal](https://portal.azure.com) as an administrator.
2. Go to **Azure Active Directory** > **Conditional access**.
3. Select **New policy**.
