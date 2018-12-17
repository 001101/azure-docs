---
title: Access Key Rotation for Azure SignalR Service
description: An overview on why customer needs to routinely rotate the access keys and how to do it with portal GUI and CLI.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
---
# Access Key Rotation for Azure SignalR Service

## Why rotating the Access Keys?

For security reason and compliance requirement, it is highly recommended to routinely rotate the access keys, by regenerating them for Azure SignalR Service instances, and updating your application configurations that use these Access Keys through connection strings.

## How to regenerate Access Keys?

1. Go to the [Azure portal](https://portal.azure.com/) and sign in with your credentials.

1. Find the Keys section from the Azure SignalR Service instance you want to regenerate the keys.

1. Click **Keys** on the navigation menu. A new key and corresponding connection string will be created.

 ![Regenerate Keys](media/signalr-key-rotation/regenerate-keys.png)

You can also regenerate keys using [Azure CLI](/cli/azure/ext/signalr/signalr/key?view=azure-cli-latest#ext-signalr-az-signalr-key-renew)

## Updating configurations with new connection strings

1. Copy the newly generated connection string

1. Update all configurations that use the connection string.

1. Restart the application

## Forced Access Key regeneration

Under certain circumstances, the Azure SignalR Service may enforce a mandatory Access Key regeneration and notify customer via email and portal notification. If you received this communication or encountered service failure due to access key, please login to the Azure Portal and find the new connection string, and update the application configurations accordingly.

## Next steps

Please routinely rotate the access keys as a good security practice. We recommend rotate all keys at least quarterly.