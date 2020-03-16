---
title: Configure Azure Role Based Access Control (RBAC)
description: This article describes how to configure Azure RBAC for the Azure API for FHIR data plane
author: hansenms
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference 
ms.date: 03/15/2020
ms.author: mihansen
---
# Configure Azure RBAC for FHIR 

In this article, you will learn how to use [Azure Role Based Access Control (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/) to assign access to the Azure API for FHIR data plane. Azure RBAC is the preferred methods for assigning data plane access when data plane users are managed in the Azure Active Directory tenant associated with your Azure subscription. If you are using an external Azure Active Directory tenant, refer to the [local RBAC assignment reference](configure-local-rbac.md).

## Confirm Azure RBAC mode

To use Azure RBAC, your Azure API for FHIR must be configured to use your Azure subscription tenant for data plane and there should be no assigned identity object IDs. You can verify your settings by inspecting the **Authentication** blade of your Azure API for FHIR:

![Confirm Azure RBAC mode](media/rbac/confirm-azure-rbac-mode.png)

The **Authority** should be set to the Azure Active directory tenant associated with your subscription and there should be no GUIDs in the box labeled **Allowed object IDs**. You will also notice that the box is disabled and a label indicates that Azure RBAC should be used to assign data plane roles.

## Assign roles

To grant users, service principals or groups access to the FHIR data plane, click **Access control (IAM)**, then click **Role assignments** and click **+ Add**:

![Add Azure RBAC role assignment](media/rbac/add-azure-rbac-role-assignment.png)

In the **Role** selection, search for one of the built-in roles for the FHIR data plane:

![Built-in FHIR data roles](media/rbac/built-in-fhir-data-roles.png)

You can choose between:

1. FHIR Data Reader: Can read (and search) FHIR data.
1. FHIR Data Writer: Can read, write, and soft delete FHIR data.
1. FHIR Data Exporter: Can read and export (`$export` operator) data.
1. FHIR Data Contributor: Can perform all data plane operations.

If these roles are not sufficient for your need, you can also [create custom roles](https://docs.microsoft.com/azure/role-based-access-control/tutorial-custom-role-powershell).

In the **Select** box, search for a user, service principal, or group that you wish to assign the role to.

## Next steps

In this article, you learned how to assign Azure RBAC roles for the FHIR data plane. Next learn about additional settings for the Azure API for FHIR:
 
>[!div class="nextstepaction"]
>[Additional settings Azure API for FHIR](azure-api-for-fhir-additional-settings.md)

