---
title: Authentication methods for Azure IoT Security Preview| Microsoft Docs
description: Learn about the different authentication methods available when using the Azure IoT Security service.
services: azureiotsecurity
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''

ms.assetid: 10b38f20-b755-48cc-8a88-69828c17a108
ms.service: azureiotsecurity
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2019
ms.author: mlottner

---

# Security agent authentication methods 

> [!IMPORTANT]
> Azure IoT Security is currently in public preview.
> This preview version is provided without a service level agreement, and is not recommended for production workloads. Certain features might not be supported or might have constrained capabilities. 
> For more information, see [Supplemental Terms of Use for Microsoft Azure Previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

This article explains the different authentication methods you can use with the AzureIoTSecurity  agent to authenticate with the IoT Hub.

For each device onboarded to Azure IoT Security in the IoT Hub, a security module is required. To authenticate the device, Azure IoT Security can use one of two methods. Choose the method that works best for your existing IoT solution. 

> [!div class="checklist"]
> * Security Module option
> * Device option

## Authentication methods

The two methods for the AzureIoTSecurity agent to perform authentication:

 - **Security Module** authentication mode<br>
   The Security Module is authenticated independently of the device twin.
   The information required for this type of authentication is defined in by the Authentication.config file for C# and the LocalConfiguration.json for C.
		
 - **Device** authentication mode<br>
    In this method, the Security agent first authenticates against the device. After the initial authentication, the Azure IoT Security agent performs **Rest** call to the IoT Hub using the Rest API with the authentication data of the device. The Azure IoT Security agent then requests the security module authentication method and data from the IoT Hub. In the final step, the Azure IoT Security agent performs an authentication against the Azure IoT Security module.	

See [How to configure authentication methods](how-to-configure-authentication-methods) to learn how to configure.
								
## Authentication methods known limitations

- **Security Module** authentication mode only supports symmetric key authentication.
- CA-Signed certificate is not supported by **Device** authentication mode.  

## See also
- [Azure IoT Security Preview](preview.md)
- [Installation for Windows](install-windows.md)
- [Azure IoT Security alerts](alerts.md)
- [Data Access](dataaccess.md)
