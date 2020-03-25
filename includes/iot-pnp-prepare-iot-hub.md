---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 03/17/2020
---

## Prepare an IoT hub

You need an Azure IoT hub in your Azure subscription to complete the steps in this article. If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin. If you don't have an IoT hub, follow [these instructions to create one](../articles/iot-hub/iot-hub-create-using-cli.md).

If you're using the Azure CLI locally, first sign in to your Azure subscription using `az login`. If you're running these commands in the Azure Cloud Shell, you're signed in automatically.

If you're using the Azure CLI locally, the `az` version should be **2.0.73** or later; the Azure Cloud Shell uses the latest version. Use the `az --version` command to check the version installed on your machine.

Run the following command to add the Microsoft Azure IoT Extension for Azure CLI to your instance:

```azurecli-interactive
az extension add --name azure-iot
```

If you don't already have an IoT hub to use, run the following commands to create a resource group and a free-tier IoT hub in your subscription. Replace `<YourIoTHubName>` with a hub name of your choice:

```azurecli-interactive
az group create --name my-pnp-resourcegroup \
    --location centralus
az iot hub create --name <YourIoTHubName> \
    --resource-group my-pnp-resourcegroup --sku F1
```

Run the following command to create the device identity in your IoT hub. Replace the `<YourIoTHubName>` and `<YourDeviceID>` placeholders with your own _IoT Hub name_ and a _device ID_ of your choice.

```azurecli-interactive
az iot hub device-identity create --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

Run the following command to get the _device connection string_ for the device you just registered (note for use later):

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name <YourIoTHubName> --device-id <YourDeviceID> --output table
```
