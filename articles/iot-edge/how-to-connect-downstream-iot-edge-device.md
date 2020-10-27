---
title: Connect downstream IoT Edge devices - Azure IoT Edge | Microsoft Docs
description: How to configure an IoT Edge device to connect to Azure IoT Edge gateway devices. 
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/15/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:  [amqp, mqtt]
monikerRange: '>= iotedge-1.2'
---

# Connect a downstream IoT Edge device to an Azure IoT Edge gateway

This article provides instructions for establishing a trusted connection between an IoT Edge device and an IoT Edge gateway.

In a gateway scenario, an IoT Edge device can be both a gateway and a downstream device. Multiple IoT Edge gateways can link together to create a hierarchy of devices. Only the top IoT Edge device in a hierarchy can connect to IoT Hub. All IoT Edge devices in lower layers of a hierarchy can only communicate with their gateway (or parent) device and any downstream (or child) devices.

There are two different configurations for IoT Edge devices in a gateway hierarchy, and this article address both. The first is the **top layer** IoT Edge device. When multiple IoT Edge devices are connecting through each other, only the device in the top layer has a connection to the cloud. This device is responsible for handling requests from all the devices below it, whether it's requests to pull container images from a registry or to push blobs to cloud storage. The other configuration applies to any IoT Edge device in a **lower layer** of the hierarchy. These devices may be a gateway for other downstream IoT and IoT Edge devices, but also need to know to route any communications through their own parent devices.

All the steps in this article build on those in [Configure an IoT Edge device to act as a transparent gateway](how-to-create-transparent-gateway.md), which sets up an IoT Edge device to be a gateway for downstream IoT devices. The same principals apply to all gateway devices: they need certificates so that they can securely connect to downstream devices, and they need to be configured to route messages from downstream devices. Gateway devices that have downstream IoT Edge devices require additional processing, which is handled by special modules.

* The **API proxy module** is required on any IoT Edge gateway that has another IoT Edge device below it. That means it must be on *every layer* of a gateway hierarchy except the bottom layer. This module uses an [nginx](https://nginx.org) reverse proxy to route data through network layers. It is highly configurable through its module twin and environment variables, so can be adjusted to fit your gateway scenario requirements.

* The **Docker registry module** is required on the IoT Edge gateway at the *top layer* or a gateway hierarchy. This module is responsible for retrieving and caching container images on behalf of all the IoT Edge devices in lower layers.

* The **Azure Blob Storage on IoT Edge** is optional but recommended on the IoT Edge gateway at the *top layer* of a gateway hierarchy. This module is necessary at the top layer if any IoT Edge devices in lower layers need to upload blobs to Azure Blob Storage. It also enables useful troubleshooting functionality for IoT Edge devices in lower layers, like module log upload and support bundle upload.

## Prerequisites

* A free or standard IoT hub.
* At least two **IoT Edge devices**, one to be the top layer device and one or more lower layer devices. If you don't have IoT Edge devices available, you can quickly [Run Azure IoT Edge on Ubuntu virtual machines](how-to-install-iot-edge-ubuntuvm.md).

<!--This article provides detailed steps and options to help you create the right gateway hierarchy for your scenario. For a guided walkthrough, see [tutorial]-->

## Create a gateway hierarchy

You create an IoT Edge gateway hierarchy by defining parent/child relationships for the IoT Edge devices in the scenario. You can set a parent device when you create a new device identity, or you can manage the parent and children of an existing device identity.

Only IoT Edge devices can be parent devices, but both IoT Edge devices and IoT devices can be children. A parent can have many children, but a child can only have one parent. A gateway hierarcy is created by chaining parent/child sets together so that the child of one device is the parent of another.

<!-- TODO: graphic of gateway hierarcy -->

### Azure portal

In the Azure portal, you can manage the parent/child relationship when you create new device identities, or by editing existing devices.

When you create a new IoT Edge device, you have the option of choosing parent and children devices from the list of existing IoT Edge devices in that hub.

<!-- TODO: Validate UI text/flows -->

1. In the [Azure portal](https://portal.azure.com), navigate to your IoT hub.
1. Select **IoT Edge** from the navigation menu.
1. Select **Add an IoT Edge device**.
1. Along with setting the device ID and authentication settings, you can **Set a parent device** or **Choose child devices**.
1. Choose the device or devices that you want as a parent or child.

You can also create or manage parent/child relationships for existing devices.

1. In the [Azure portal](https://portal.azure.com), navigate to your IoT hub.
1. Select **IoT Edge** from the navigation menu.
1. Select the device you want to manage from the list of **IoT Edge devices**.
1. Select **Set a parent device** or **Manage child devices**.
1. Add or remove any devices.

### Azure CLI

The [azure-iot](/cli/azure/ext/azure-iot) extension for the Azure CLI provides commands to manage your IoT resources. You can manage the parent/child relationship of IoT and IoT Edge devices when you create new device identities or by editing existing devices.

The [az iot hub device-identity](/cli/azure/ext/azure-iot/iot/hub/device-identity) set of commands allow you to manage the parent/child relationships for a given device.

The `create` command includes parameters for adding children devices and setting a parent device at the time of device creation.

Additional device-identity commands, including `add-children`, `get-parent`, `list-children`, `remove-children`, and `set-parent` allow you to manage the parent/child relationships for existing devices.

## Prepare certificates

A consistent chain of certificates must be installed across devices in the same gateway hierarchy to establish a secure communication between themselves. Every device in the hierarchy, whether an IoT Edge device or an IoT leaf device, needs a copy of the same root CA certificate. Each IoT Edge device in the hierarchy then uses that root CA certificate as the root for its device CA certificate.

With this setup, each downstream IoT Edge device or IoT leaf device can verify the identity of their parent by verifying that the edgeHub they connect to has a server certificate that is signed by the shared root CA certificate.

<!-- TODO: certificate graphic -->

Create the following certificates:

* A **root CA certificate**, which is the topmost shared certificate for all the devices in a given gateway hierarchy. This certificate is installed on all devices.
* Any **intermediate certificates** that you want to include in the root certificate chain.
* A **device CA certificate** and its **private key**, generated by the root and intermediate certificates. You need one unique device CA certificate for each IoT Edge device in the gateway hierarchy.

>[!NOTE]
>Currently, a limitation in libiothsm prevents the use of certificates that expire on or after January 1, 2038.

You can use either a self-signed certificate authority or purchase one from a trusted commercial certificate authority like Baltimore, Verisign, Digicert, or GlobalSign.

If you don't have your own certificates to use, you can [create demo certificates to test IoT Edge device features](how-to-create-test-certificates.md). Follow the steps in that article to create the one set of root and intermediate certificates, then to create IoT Edge device CA certificates for each of your devices.

## Configure devices for the top layer

An IoT Edge device is considered the top layer of a gateway hierarchy if it is the parent of other devices, but is not the child of another device. This device maintains the connection to the cloud for all devices that are below it. As such, it has a different configuration pattern than IoT Edge devices in lower levels of a gateway hierarchy.

### Network configuration for top layer devices

For each gateway device in the top layer, network operators need to:

* Provide a static IP address.
* Authorize outbound communications from this IP address to your Azure IoT Hub hostname over ports 443 (HTTPS) and 5671 (AMQP).
* Authorize outbound communications from this IP address to your Azure Container Registry hostname and Microsoft Container Registry (mcr.microsoft.com) over port 443 (HTTPS).

### IoT Edge configuration for top layer devices

You should already have IoT Edge installed on your device. If not, follow the steps to [Install the Azure IoT Edge runtime](how-to-install-iot-edge.md) and then provision your device with either [symmetric key authentication](how-to-manual-provision-symmetric-key.md) or [X.509 certificate authentication](how-to-manual-provision-x509.md).

The steps in this section reference the **root CA certificate** and **device CA certificate and private key** that were discussed earlier in this article. If you created those certificates on a different device, have them available on this device. You can transfer the files physically, like with a USB drive, with a service like [Azure Key Vault](../key-vault/general/overview.md), or with a function like [Secure file copy](https://www.ssh.com/ssh/scp/).

Use the following steps to configure IoT Edge on your device, based on the operating system:

# [Linux](#tab/linux)

On Linux, make sure that the user **iotedge** has read permissions for the directory holding the certificates and keys.

1. Install the **root CA certificate** on this IoT Edge device.

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem
   ```

1. Update the certificate store.

   ```bash
   sudo update-ca-certificates
   ```

   This command should output that one certificate was added to /etc/ssl/certs.

1. Open the IoT Edge security daemon configuration file.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Find the **certificates** section in the config.yaml file. Update the three certificate fields to point to your certificates. Provide the file URI paths, which take the format `file:///<path>/<filename>`.

   * **device_ca_cert**: File URI path to the device CA certificate unique to this device.
   * **device_ca_pk**: File URI path to the device CA private key unique to this device.
   * **trusted_ca_certs**: File URI path to the root CA certificate shared by all devices in the gateway hierarchy.

1. Find the **hostname** parameter in the config.yaml file. Update the hostname to be the fully qualified domain name (FQDN) of the IoT Edge device.

   The value of this parameter is what downstream devices will use to connect to this gateway. The hostname takes the machine name by default, but the FQDN makes it easier to connect downstream devices.

   Use a hostname shorter than 64 characters, which is the character limit for a server certificate common name.

   Do not use IP addresses as hostnames. If you can't use FQDNs, keep the default machine name as the hostname and instead update the child devices to map the parent device's IP address to its hostname. Detailed instructions for this process are in the [IoT Edge configuration for lower layer devices](#iot-edge-configuration-for-lower-layer-devices), later in this article.

1. Save (`Ctrl+O`) and close (`Ctrl+X`) the config.yaml file.

1. If you've used any other certificates for IoT Edge before, delete the files in the following two directories to make sure that your new certificates get applied:

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. Restart the IoT Edge service to apply your changes.

   ```bash
   sudo systemctl restart iotedge
   ```

1. Check for any errors in the configuration.

   ```bash
   sudo iotedge check --verbose
   ```

# [Windows](#tab/windows)

1. Run notepad as an administrator.

1. Open the IoT Edge security daemon config file.

   `C:\ProgramData\iotedge\config.yaml`

1. Find the **certificates** section in the config.yaml file. Update the three certificate fields to point to your certificates. Provide the file URI paths, which take the format `file:///C:/<path>/<filename>`.

   * **device_ca_cert**: File URI path to the device CA certificate unique to this device.
   * **device_ca_pk**: File URI path to the device CA private key unique to this device.
   * **trusted_ca_certs**: File URI path to the root CA certificate shared by all devices in the gateway hierarchy.

1. Find the **hostname** parameter in the config.yaml file. Update the hostname to be the fully qualified domain name (FQDN) of the IoT Edge device.

   The value of this parameter is what downstream devices will use to connect to this gateway. The hostname takes the machine name by default, but the FQDN makes it easier to connect downstream devices.

   Use a hostname shorter than 64 characters, which is the character limit for a server certificate common name.

   Do not use IP addresses as hostnames. If you can't use FQDNs, keep the default machine name as the hostname and instead update the child devices to map the parent device's IP address to its hostname. Detailed instructions for this process are in the [IoT Edge configuration for lower layer devices](#iot-edge-configuration-for-lower-layer-devices), later in this article.

1. Save and close the config.yaml file.

1. If you've used any other certificates for IoT Edge before, delete the files in the following two directories to make sure that your new certificates get applied:

   * `C:\ProgramData\iotedge\hsm\certs`
   * `C:\ProgramData\iotedge\hsm\cert_keys`

1. Open a PowerShell session as an administrator to restart the IoT Edge service to apply your changes.

   ```powershell
   Restart-Service iotedge
   ```

1. Check for any errors in the configuration.

   ```powershell
   iotedge check --verbose
   ```

---

### Deploy modules to top layer devices

## Configure devices for lower layers

An IoT Edge device is considered a lower layer of a gateway hierarchy if it is the child of another device. This device does not have a connection to the cloud, so passes any requests or communications through its parent device. It doesn't matter how many layers of gateways exist between this device and the cloud. The behavior is the same for an IoT Edge device with one parent device above it or an IoT Edge device with five generations of parent devices above it.

An IoT Edge device can have many children, but only one parent.

### Network configuration for lower layer devices

For each gateway device in a lower layer, network operators need to:

* Provide a static IP address.
* Authorize outbound communications from this IP address to the parent gateway's IP address over ports 443 (HTTPS) and 5671 (AMQP), unless the two devices are part of the same subnet.

### IoT Edge configuration for lower layer devices

You should already have IoT Edge installed on your device. If not, follow the steps to [Install the Azure IoT Edge runtime](how-to-install-iot-edge.md) and then provision your device with either [symmetric key authentication](how-to-manual-provision-symmetric-key.md) or [X.509 certificate authentication](how-to-manual-provision-x509.md).

The steps in this section reference the **root CA certificate** and **device CA certificate and private key** that were discussed earlier in this article. If you created those certificates on a different device, have them available on this device. You can transfer the files physically, like with a USB drive, with a service like [Azure Key Vault](../key-vault/general/overview.md), or with a function like [Secure file copy](https://www.ssh.com/ssh/scp/).

The steps in this section are nearly identical to the steps to configure top layer devices, with one important extra step. For IoT Edge devices in lower layers, you need to include the fully qualified domain name (FQDN) or hostname of the parent device.

Use the following steps to configure IoT Edge on your device, based on the operating system:

# [Linux](#tab/linux)

On Linux, make sure that the user **iotedge** has read permissions for the directory holding the certificates and keys.

1. Install the **root CA certificate** on this IoT Edge device.

   ```bash
   sudo cp <path>/<root ca certificate>.pem /usr/local/share/ca-certificates/<root ca certificate>.pem
   ```

1. Update the certificate store.

   ```bash
   sudo update-ca-certificates
   ```

   This command should output that one certificate was added to /etc/ssl/certs.

1. Open the IoT Edge security daemon configuration file.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Find the **certificates** section in the config.yaml file. Update the three certificate fields to point to your certificates. Provide the file URI paths, which take the format `file:///<path>/<filename>`.

   * **device_ca_cert**: File URI path to the device CA certificate unique to this device.
   * **device_ca_pk**: File URI path to the device CA private key unique to this device.
   * **trusted_ca_certs**: File URI path to the root CA certificate shared by all devices in the gateway hierarchy.

1. Find the **hostname** parameter in the config.yaml file. Update the hostname to be the fully qualified domain name (FQDN) of the IoT Edge device.

   The value of this parameter is what downstream devices will use to connect to this gateway. The hostname takes the machine name by default, but the FQDN makes it easier to connect downstream devices.

   Use a hostname shorter than 64 characters, which is the character limit for a server certificate common name.

   Do not use IP addresses as hostnames. If you can't use FQDNs, keep the default machine name as the hostname and instead update the child devices to map the parent device's IP address to its hostname. Detailed instructions for this process are in the next step.

1. Find the **parent_hostname** parameter.

   * **If the parent hostname is a fully qualified domain name**, simply provide the FQDN in the config.yaml file.

      1. Update the **parent_hostname** field to be the FQDN of the parent device.

   * **If the parent hostname is the default machine name, or other hostname**, use the container create options to map the parent's hostname to the parent's IP address in all modules deployed to the device

      1. Update the **parent_hostname** field to be the hostname provided in the parent device's config.yaml file.

      1. Find the **agent** section of the config.yaml file. Add an `ExtraHosts` mapping in the module create options so that the edgeAgent module can start and pull the first deployment.

         ```yml
         agent:
           name: "edgeAgent"
           type: "docker"
           env: {}
           config:
             image: "mcr.microsoft.com/azureiotedge-agent:1.2"
             auth: {}
             createOptions:
               HostConfig:
                 ExtraHosts: ["<parent_hostname>:<parent_IP_address>"]
         ```

      1. In any future deployments to this device, include the `ExtraHosts` create options. For example, add this parameter to the existing edgeHub create options:

         ```json
         "edgeHub": {
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "mcr.microsoft.com/azureiotedge-hub:1.2",
             "createOptions": "{\"HostConfig\": {\"ExtraHosts\": [\"<parent_hostname>:<parent_IP_address>\"],\"PortBindings\": {\"5671\/tcp\": [{\"HostPort\": \"5671\"}],\"8883\/tcp\": [{\"HostPort\": \"8883\"}],\"443\/tcp\": [{\"HostPort\": \"443\"}]}}}"
           }
         }
         ```

1. Save (`Ctrl+O`) and close (`Ctrl+X`) the config.yaml file.

1. If you've used any other certificates for IoT Edge before, delete the files in the following two directories to make sure that your new certificates get applied:

   * `/var/lib/iotedge/hsm/certs`
   * `/var/lib/iotedge/hsm/cert_keys`

1. Restart the IoT Edge service to apply your changes.

   ```bash
   sudo systemctl restart iotedge
   ```

1. Check for any errors in the configuration.

   ```bash
   sudo iotedge check --verbose
   ```

# [Windows](#tab/windows)

1. Run notepad as an administrator.

1. Open the IoT Edge security daemon config file.

   `C:\ProgramData\iotedge\config.yaml`

1. Find the **certificates** section in the config.yaml file. Update the three certificate fields to point to your certificates. Provide the file URI paths, which take the format `file:///C:/<path>/<filename>`.

   * **device_ca_cert**: File URI path to the device CA certificate unique to this device.
   * **device_ca_pk**: File URI path to the device CA private key unique to this device.
   * **trusted_ca_certs**: File URI path to the root CA certificate shared by all devices in the gateway hierarchy.

1. Find the **hostname** parameter in the config.yaml file. Update the hostname to be the fully qualified domain name (FQDN) of the IoT Edge device.

   The value of this parameter is what downstream devices will use to connect to this gateway. The hostname takes the machine name by default, but the FQDN makes it easier to connect downstream devices.

   Use a hostname shorter than 64 characters, which is the character limit for a server certificate common name.

   Do not use IP addresses as hostnames. If you can't use FQDNs, keep the default machine name as the hostname and instead update the child devices to map the parent device's IP address to its hostname. Detailed instructions for this process are in the next section.

1. Find the **parent_hostname** parameter.

   * **If the parent hostname is a fully qualified domain name**, simply provide the FQDN in the config.yaml file.

      1. Update the **parent_hostname** field to be the FQDN of the parent device.

   * **If the parent hostname is the default machine name, or other hostname**, use the container create options to map the parent's hostname to the parent's IP address in all modules deployed to the device

      1. Update the **parent_hostname** field to be the hostname provided in the parent device's config.yaml file.

      1. Find the **agent** section of the config.yaml file. Add an `ExtraHosts` mapping in the module create options so that the edgeAgent module can start and pull the first deployment.

         ```yml
         agent:
           name: "edgeAgent"
           type: "docker"
           env: {}
           config:
             image: "mcr.microsoft.com/azureiotedge-agent:1.2"
             auth: {}
             createOptions:
               HostConfig:
                 ExtraHosts: ["<parent_hostname>:<parent_IP_address>"]
         ```

      1. In any future deployments to this device, include the `ExtraHosts` create options. For example, add this parameter to the existing edgeHub create options:

         ```json
         "edgeHub": {
           "type": "docker",
           "status": "running",
           "restartPolicy": "always",
           "settings": {
             "image": "mcr.microsoft.com/azureiotedge-hub:1.2",
             "createOptions": "{\"HostConfig\": {\"ExtraHosts\": [\"<parent_hostname>:<parent_IP_address>\"],\"PortBindings\": {\"5671\/tcp\": [{\"HostPort\": \"5671\"}],\"8883\/tcp\": [{\"HostPort\": \"8883\"}],\"443\/tcp\": [{\"HostPort\": \"443\"}]}}}"
           }
         }
         ```

1. Save and close the config.yaml file.

1. If you've used any other certificates for IoT Edge before, delete the files in the following two directories to make sure that your new certificates get applied:

   * `C:\ProgramData\iotedge\hsm\certs`
   * `C:\ProgramData\iotedge\hsm\cert_keys`

1. Open a PowerShell session as an administrator to restart the IoT Edge service to apply your changes.

   ```powershell
   Restart-Service iotedge
   ```

1. Check for any errors in the configuration.

   ```powershell
   iotedge check --verbose
   ```

---

### Deploy modules to lower layer devices