---
title: Create C# modules for Azure IoT Edge | Microsoft Docs
description: Use Visual Studio Code to develop, build, and debug a C# module for
 Azure IoT Edge
services: iot-edge
keywords: 
author: shizn
manager: timlt

ms.author: xshi
ms.date: 05/28/2018
ms.topic: article
ms.service: iot-edge

---

# Develop, build, and deploy a C# module with Azure IoT Edge for Visual Studio Code

You can send your business logic to operate at the edge by turning it into modules for Azure IoT Edge. This article provides detailed instructions for using [Visual Studio (VS) Code](https://code.visualstudio.com/) as the main development tool to develop C# modules.

## Prerequisites
This article assumes that you are using a computer or virtual machine running Windows or Linux as your development machine. Your IoT Edge device can be another physical device, or you can simulate your IoT Edge device on your development machine.

Since this article uses Visual Studio Code as the main development tool, install VS Code and then add the necessary extensions:
* [Visual Studio Code](https://code.visualstudio.com/) 
* [Azure IoT Edge extension](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge) 
* [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) 
* [Docker extension](https://marketplace.visualstudio.com/items?itemName=PeterJausovec.vscode-docker)

To create a module, you need .NET which builds the project folder, Docker to build the module image, and a container registry to hold the module image:
* [.NET Core 2.0 SDK](https://www.microsoft.com/net/core#windowscmd) 
* [Docker](https://docs.docker.com/engine/installation/)
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) or [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags)

To test your module on a device, you need an active IoT hub with at least one IoT Edge device. If you want to use your computer as an IoT Edge device, you can do so by following the steps in the tutorials for [Windows](tutorial-simulate-device-windows.md) or [Linux and Mac](tutorial-simulate-device-linux.md). 

<!--- - A local Docker registry running on your development machine. It is suggested to use a local Docker registry for prototype and testing purpose. You can update the container registry in the `module.json` file in each module folder.
- An Edge runtime running on your development machine.
--->

## Create a new solution template

The following steps show you how to create an IoT Edge module based on .NET Core 2.0 using Visual Studio Code and the Azure IoT Edge extension. You start by creating a solution, and then generating the first module in that solution. Each solution can contain multiple modules. 

1. In Visual Studio Code, select **View** > **Integrated Terminal**.
2. In the integrated terminal, enter the following command to update the latest version of the Azure IoT Edge module template in .NET:

   ```cmd/sh
   dotnet new -i Microsoft.Azure.IoT.Edge.Module
   ```
3. In Visual Studio Code, select **View** > **Command Palette**. 
4. In the command palette, type and run the command **Azure IoT Edge: New IoT Edge Solution**.

   ![Run New IoT Edge Solution](./media/how-to-csharp-module-vscode/new-solution.png)

5. Browse to the folder where you want to create the new solution, and click **Select folder**. 
6. Provide a name for your solution. 
7. Choose **C# Module** as the template for the first module in the solution.
8. Provide a name for your module. Choose a name that's unique within your container registry. 
9. Provide the image repository for the module. VS Code autopopulates the module name, so you just have to replace **localhost:5000** with your own registry information. If you use a local Docker registry for testing, then localhost is fine. If you use Azure Container Registry, then use the login server from your registry's settings. The login server looks like **\<registry name\>.azurecr.io**.

VS Code takes the information you provided, creates an IoT Edge solution, then loads it in a new window.

![View IoT Edge solution](./media/how-to-csharp-module-vscode/view-solution.png)

Within the solution you have three items: 
* A **.vscode** folder contains debug configurations.
* A **modules** folder contains subfolders for each module. Right now you only have one, but you could add more in the command palette with the command **Azure IoT Edge: Add IoT Edge Module**. 
* A **deployment.template.json** file lists your new module along with a sample **tempSensor** module that simulates data that you can use for testing. For more information about how deployment manifests work, see [Understand how IoT Edge modules can be used, configured, and reused](module-composition.md).

## Build your module for deployment



## Build your module for debugging

> [!NOTE]
> You can only debug C# module in linux-amd64 containers.

1. To start debugging, you need to use the **Dockerfile.amd64.debug** to rebuild your docker image and deploy your Edge solution again. In VS Code explorer, navigate to `deployment.template.json` file. Update your function image URL by adding a `.debug` in the end.

2. Rebuild your solution. In VS Code command palette, type and run the command **Edge: Build IoT Edge solution**.

3. In Azure IoT Hub Devices explorer, right-click an IoT Edge device ID, then select **Create deployment for Edge device**. Select the `deployment.json` file in the `config` folder. Then you can see the deployment is successfully created with a deployment ID in VS Code integrated terminal.

You can check your container status in the VS Code Docker explorer or by run the `docker images` command in the terminal.

## Start debugging C# module in VS Code
1. VS Code keeps debugging configuration information in a `launch.json` file located in a `.vscode` folder in your workspace. This `launch.json` file was generated when you created a new IoT Edge solution. It updates each time you add a new module that supports debugging. Navigate to the debug view and select the corresponding debug configuration file.
    ![Select debug configuration](./media/how-to-debug-csharp-function/select-debug-configuration.jpg)

2. Navigate to `program.cs`. Add a breakpoint in this file.

3. Click the **Start Debugging** button or press **F5**, and select the process to attach to.

4. In VS Code Debug view, you can see the variables in left panel. 

> [!NOTE]
> The preceding example shows how to debug .NET Core IoT Edge modules on containers. It's based on the debug version of the `Dockerfile.debug`, which includes VSDBG (the .NET Core command-line debugger) in your container image while building it. After you finish debugging your C# modules, we recommend you directly use or customize `Dockerfile` without VSDBG for production-ready IoT Edge modules.

## Next steps

[Use Visual Studio Code to debug Azure Functions with Azure IoT Edge](how-to-vscode-debug-azure-function.md)

