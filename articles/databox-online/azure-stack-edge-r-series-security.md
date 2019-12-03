---
title: Azure Stack Edge security | Microsoft Docs
description: Describes the security and privacy features that protect your Azure Stack Edge device, service, and data on-premises and in the cloud.
services: Azure Stack Edge
author: alkohli

ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 11/28/2019
ms.author: alkohli
---
# Security and data protection for Azure Stack Edge Rugged series 

Security is a major concern when you're adopting a new technology, especially if the technology is used with confidential or proprietary data. Azure Stack Edge Rugged series helps you ensure that only authorized entities can view, modify, or delete your data.

This article describes the Azure Stack Edge Rugged series security features that help protect each of the solution components and the data stored in them.

Azure Stack Edge consists of four main components that interact with each other:

- **Azure Stack Edge service, hosted in Azure public or Azure Government cloud**. The management resource that you use to create the device order, configure the device, and then track the order to completion.
- **Azure Stack Edge device**. The rugged, physical device that's shipped to you so you can import your on-premises data into Azure public or Azure Government cloud.
- **Clients/hosts connected to the device**. The clients in your infrastructure that connect to the Azure Stack Edge device and contain data that needs to be protected.
- **Cloud storage**. The location in the Azure cloud platform where data is stored. This location is typically the storage account linked to the Azure Stack Edge resource that you create.

## Azure Stack Edge service protection

The Azure Stack Edge service is a management service that's hosted in Azure. The service is used to configure and manage the device.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-service-protection.md)]

## Azure Stack Edge device protection

The Azure Stack Edge device is an on-premises device that helps transform your data by processing it locally and then sending it to Azure. Your device:

- Needs an activation key to access the Azure Stack Edge service.
- Is protected at all times by a device password.
- Is a locked-down device. The device BMC and BIOS are password-protected. The BIOS is protected by limited user-access.
- Has secure boot enabled that ensures the device boots up only using the trusted software provided by Microsoft.
- Runs Windows Defender Device Guard. Device Guard lets you run only trusted applications that you define in your code-integrity policies.
- Has a Trusted Platform Module (TPM) that performs hardware-based, security-related functions. Specifically, the TPM manages and protects secrets and data that needs to be persisted on the device.
- Only the required ports are opened on the device and all the other ports are blocked. For more information, see the list of [Port requirements for Azure Stack Edge appliance](azure-stack-edge-r-series-placeholder.md) .
- All the access to the device hardware as well as software is logged. 
    - For the device software, default firewall logs are collected for inbound and outbound traffic from the device. These logs are bundled in the support package.
    - For the device hardware, all the device chassis events such as opening and closing of the device chassis, are logged in the device.

    For more information on the specific logs that contain the hardware and software intrusion events and how to get the logs, go to [Gather advanced security logs](azure-stack-edge-r-series-troubleshoot.md). 


### Protect the device via activation key

Only an authorized Azure Stack Edge device is allowed to join the Azure Stack Edge service that you create in your Azure subscription. To authorize a device, you need to use an activation key to activate the device with the Azure Stack Edge service.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-activation-key.md)]

For more information, see [Get an activation key](azure-stack-edge-r-series-placeholder.md).

### Protect the device via password

Passwords ensure that only authorized users can access your data. Azure Stack Edge devices boot up in a locked state.

You can:

- Connect to the local web UI of the device via a browser and then provide a password to sign in to the device.
- Remotely connect to the device PowerShell interface over HTTP. Remote management is turned on by default. Remote management is also configured to use Just Enough Administration (JEA) to limit what the users can do. You can then provide the device password to sign in to the device. For more information, see [Connect remotely to your Azure Stack Edge device](azure-stack-edge-r-series-placeholder.md).
- The local Edge user on the device has limited access to the device for initial configuration, and troubleshooting. The compute workloads running on the device, data transfer, and the storage can all be accessed from the Azure public or government portal for the resource in the cloud.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-password-best-practices.md)]
- Use the local web UI to [change the password](azure-stack-edge-r-series-placeholder.md). If you change the password, be sure to notify all remote access users so they don't have problems signing in.

### Establish trust with the device via certificates

Azure Stack Edge appliance lets you Bring Your Own Certificates (BYOC) and install those to be used for all public endpoints. For more information, go to [Upload your certificate](azure-stack-edge-r-series-manage-certificates.md#upload-certificates). For a list of all the certificates that can be installed on your device, go to [Manage certificates on your Azure Stack Edge appliance](azure-stack-edge-r-series-manage-certificates.md).

- When you configure compute on your Azure Stack Edge device, an IoT device and an IoT Edge device are created. These devices are automatically assigned symmetric access keys. As a security best practice, these keys are rotated regularly via the IoT Hub service.

## Protect your data

This section describes the Azure Stack Edge security features that protect in-transit and stored data.

### Protect data at rest

All the data at rest on the device is double-encrypted, the access to data is controlled and once the device is deactivated, the data is securely erased off the data disks.

#### Double-encryption of data

Data on your disks is protected by two layers of encryption:
- First layer of encryption is the BitLocker XTS-AES 256-bit encryption on the data volumes. This encryption is used to protect local data. The OS volume has BitLocker as the single layer of encryption.
- Second layer is the hard disks that have a built-in encryption.

When the device is activated, you are prompted to save a file that contains recovery keys that help recover the data on the device if the device doesn’t boot up. There are three keys in the file:
- One key recovers the device configuration on the OS volumes.
- Second key is to unlock the BitLocker on the data disks.
- Third key unlocks the hardware encryption in the data disks.
    
    **Important**: Save the recovery key in a secure location outside the device istelf. If the device doesn’t boot up, and you don’t have the key, it could potentially result in data loss.
- If a node isn’t booting up, then you need to provide this recovery key.
- Certain recovery scenarios will prompt you for the key file that you have saved.
- If the device fails, Microsoft Support will be able to help boot the device to a USB device. With the above recovery keys, you can recover and transfer data from the failed device.

#### Restricted access to data

Access to data stored in shares and storage accounts is restricted.
- SMB clients that access share data need user credentials associated with the share. These credentials are defined when the share is created.
- NFS clients that access a share need to have their IP address added explicitly when the share is created.
- The Edge storage accounts that are created on the device are local and are protected by the encryption on the data disks. The Azure storage accounts that these Edge storage accounts are mapped to are protected by subscription and two 512-bit storage access keys associated with the Edge storage account (these keys are different than those associated with your Azure Storage accounts). For more information, see [Protect data in storage accounts](#protect-data-in-storage-accounts).

#### Secure data erasure

When the device undergoes a hard reset, a secure wipe is performed on the device. The secure wipe performs data erasure on the disks using the NIST SP 800-88r1 purge.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-data-rest.md)]
- BitLocker XTS-AES 256-bit encryption is used to protect local data.

### Protect data in flight

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-data-flight.md)]

### Protect data in storage accounts

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-protect-data-storage-accounts.md)]
- Rotate and then [sync your storage account keys](azure-stack-edge-r-series-placeholder.md) regularly to help protect your storage account from unauthorized users.

## Manage personal information

The Azure Stack Edge service collects personal information in the following scenarios:

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-manage-personal-data.md)]

To view the list of users who can access or delete a share, follow the steps in [Manage shares on the Azure Stack Edge](azure-stack-edge-r-series-placeholder.md).

For more information, review the Microsoft privacy policy on the [Trust Center](https://www.microsoft.com/trustcenter).

## Next steps

[Deploy your Azure Stack Edge device](azure-stack-edge-r-series-placeholder.md)
