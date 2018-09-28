---
title: Understanding Azure Digital Twins device connectivity and authentication | Microsoft Docs
description: Using Azure Digital Twins to connect and authenticate devices
author: adgera
manager: alinast
ms.service: azure-digital-twins
services: azure-digital-twins
ms.topic: conceptual
ms.date: 09/28/2018
ms.author: adgera
---

# Create and manage role assignments

Azure Digital Twins uses [role-based access control](./security-role-based-access-control.md) to specify which Digital Twins topology resources are granted or revoked access, permissions, and roles.

Each role assignment includes:

* An **object identifier** (an Azure Active Directory Id, service principal object Id, or domain name).
* An **object identifier type**.
* A **role definition Id**.
* A **space path**.
* (In most cases) an Azure Active Directory **tenant Id**.

## Role definition identifiers

The table below shows what can be obtained by querying the System/Roles API:

|**Role** | **Identifier** |
|---------|---------|
| Space Administrator | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| User Administrator| dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Device Administrator | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Key Administrator | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Token Administrator | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| User | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Support Specialist | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Device Installer | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| GatewayDevice | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |

## Supported ObjectIdTypes

The supported `ObjectIdTypes` are:

* `UserId`
* `DeviceId`
* `DomainName`
* `TenantId`
* `ServicePrincipalId`
* `UserDefinedFunctionId`

## Create a role assignment

```plaintext
HTTP POST /api/v1.0/roleassignments
```

| **Name** | **Required** | **Type** | **Description** |
|---------|---------|---------|---------|
|roleId	| Yes |	string | The role definition identifier. Role definitions and their identifiers can be found by querying the system API. |
|objectId | Yes |	string | The object id for the role assignment that must be formatted according to its associated type.
For the DomainName ObjectIdType, ObjectId must begin with the “@” character. |
| objectIdType | Yes |string | The type of the role assignment. Must be one of the following |
| tenantId | Varies | string |	The tenant identifier.
Disallowed for DeviceId and TenantId ObjectIdTypes.
Required for UserId and ServicePrincipalId ObjectIdTypes.
Optional for the DomainName ObjectIdType. |
| path* | Yes | string |The full access path to the Space object. Ex: /{Guid}/{Guid} If an identifier needs the role assignment for the entire topology specify "/" which designates the root. However, using "/" is discouraged and **you should always follow the Principle of Least Privilege** |

## Sample configuration

A user needs administrative access to a floor of a tenant space:

    ```json
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : " 0fc863bb-eb51-4704-a312-7d635d70e599",
      "ObjectIdType" : "UserId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/ 091e349c-c0ea-43d4-93cf-6b57abd23a44/ d84e82e6-84d5-45a4-bd9d-006a118e3bab"
    }
    ```

An application that runs test scenarios mocking devices and sensors:

    ```json
    {
      "RoleId": "98e44ad7-28d4-4007-853b-b9968ad132d1",
      "ObjectId" : "cabf7acd-af0b-41c5-959a-ce2f4c26565b",
      "ObjectIdType" : "ServicePrincipalId",
      "TenantId": " a0c20ae6-e830-4c60-993d-a91ce6032724",
      "Path": "/"
    }
    ```

All users part of a domain will receive read access for spaces, sensors, and users, including their corresponding related objects:

    ```json
    {
      "RoleId": " b1ffdb77-c635-4e7e-ad25-948237d85b30",
      "ObjectId" : "@microsoft.com",
      "ObjectIdType" : "DomainName",
      "Path": "/091e349c-c0ea-43d4-93cf-6b57abd23a44"
    }
    ```

To GET a Role Assignment:

```plaintext
HTTP GET /api/v1/roleassignments?path={path}
```

| **Name** | **In** | **Required** |	**Type** |	**Description** |
|---------|---------|---------|---------|---------|
| Path | Path | True | String |	The full path to the space |

To DELETE a Role Assignment:

```plaintext
HTTP DELETE /api/v1/roleassignments/{id}
```

| **Name** | **In** | **Required** | **Type** | **Description** |
|---------|---------|---------|---------|---------|
| Id | Path| True | String |	Role Assignment Id |

## Next steps

Read more about Azure Digital Twins security:

> [!div class="nextstepaction"]
> [API authentication] (./security-authenticating-apis.md)