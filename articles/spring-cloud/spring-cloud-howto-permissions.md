---
title: "How to - Use permissions in Azure Spring Cloud"
description: This article shows you how to create custom roles for permissions in Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/04/2020
ms.custom: devx-track-java
---

# How to use permissions in Azure Spring Cloud
This article shows you how to create custom roles that delegate permissions to Azure Spring Cloud resources. Custom roles extend [built-in Azure roles](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) with various stock permissions.

We will implement the following custom roles:

* **Developer role**: 
    * Deploy
    * Test
    * Restart apps
    * Can apply and make changes to app configurations in the git repository
    * Can get the log stream
* **Ops - Site Reliability Engineering**: 
    * Restart apps
    * Get log streams
    * Cannot make changes to apps or configurations
* **Azure Pipelines/Jenkins/Github Actions role**:
    * Can perform create, read, update, delete operations
    * Can create and configure everything in Azure Spring Cloud and apps within service instance: Azure Pipelines, Jenkins or GitHub Actions, using Terraform or ARM Templates

## Define Developer role

The developer role includes permissions to restart apps and see their log streams, but cannot make changes to apps, configuration.

### Navigate subscription and resource group Access control (IAM)

Follow these steps to start defining a role.

1. In the Azure portal, open the subscription and resource group where you want the custom role to be assignable.
2. Open **Access control (IAM)**.
3. Click **+ Add**.
4. Click **Add custom role**.
5. Click **Next**.

   ![Create custom role](media/spring-cloud-permissions/create-custom-role.png)

6. Click **Add permissions**.

   ![Add permissions start](media/spring-cloud-permissions/add-permissions.png)

### Search for Azure Spring Cloud permissions:
7. In the search box, search for *Microsoft.app*.
Select *Microsoft Azure Spring Cloud*.

   ![Select Azure Spring Cloud](media/spring-cloud-permissions/spring-cloud-permissions.png)

8. Select the permissions.

  [ ![Create Developler permissions](media/spring-cloud-permissions/developer-permissions-box.png) ](media/spring-cloud-permissions/developer-permissions-box.png#lightbox)

9. Click **Add**.

10. Review the permissions.

11. Click **Review and create**.

## Define Ops - Site Reliability Engineering role
This procedure defines a role with permissions to deploy, test, and restart Azure Spring Cloud apps.

1. Repeat the procedure to navigate subscription, resource group,and access Access control (IAM).

2. Select the permissions:

   [ ![App platform permissions](media/spring-cloud-permissions/app-platform-permissions.png) ](media/spring-cloud-permissions/ops-sre-permissions.png#lightbox)

3. Click **Add**.

4. Review the permissions.

5. Click **Review and create**.

## Define Azure Pipelines/Provisioning role
This Jenkins/Github Actions role can create and configure everything in Azure Spring Cloud and apps with a service instance. This role is for releasing or deploying code.

1. Repeat the procedure to navigate subscription, resource group, and access Access control (IAM).

2. Open the **Permissions** options.

3. Select the permissions:
  
   [ ![App platform permissions](media/spring-cloud-permissions/app-platform-permissions.png) ](media/spring-cloud-permissions/pipelines-permissions-box.png#lightbox)  

4. Click **Add**.

5. Review the permissions.

6. Click **Review and create**.


## See also
* [Create or update Azure custom roles using the Azure portal](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-portal)

For more information about three methods that define a custom permissions see:
* [Clone a role](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-portal#clone-a-role)
* [Start from scratch](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-portal#start-from-scratch)
* [Start from JSON](https://docs.microsoft.com/azure/role-based-access-control/custom-roles-portal#start-from-json)