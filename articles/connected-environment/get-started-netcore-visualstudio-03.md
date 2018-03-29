---
title: "Create a .NET Core development environment with containers using Kubernetes in the cloud with Visual Studio - Step 3 - Create a Kubernetes development environment | Microsoft Docs"
author: "ghogen"
ms.author: "ghogen"
ms.date: "03/23/2018"
ms.topic: "tutorial"

description: "Rapid Kubernetes development with containers and microservices on Azure"
keywords: "Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers"
manager: "douge"
---
# Get Started on Connected Environment with .NET Core and Visual Studio

Previous step: [Create an ASP.NET web apps](get-started-netcore-visualstudio-02.md)

## Create a dev environment in Azure
With Connected Environment, you can create Kubernetes-based development environments that are fully managed by Azure and optimized for development. With the project you just created open, select **Connected Environment for AKS** from the launch settings dropdown, as shown below.

![](media/get-started-netcore-visualstudio/LaunchSettings.png)

In the dialog that is displayed next, make sure you are signed in with the appropriate account, and then either select an existing development environment, or select **<Create New Connected Environment for AKS…>** to create a new one.

![](media/get-started-netcore-visualstudio/ConnectedEnvDialog.png)

You can use the default values provided or adjust them as you like. Click **OK** when the values are set appropriately.

![](media/get-started-netcore-visualstudio/NewEnvDialog.png)

Back on the previous dialog, leave the **Space** dropdown defaulted to `mainline` for now. Later, you'll learn more about this option. Check the **Publicly Accessible** checkbox so the web app will be accessible via a public endpoint. This setting isn't required, but it will be helpful to demonstrate some concepts later in this walkthrough. But don’t worry, in either case you will be able to debug your website using Visual Studio.

![](media/get-started-netcore-visualstudio/ConnectedEnvDialog2.png)

Click **OK** to select or create the development environment. A background task will be started to accomplish this, it will take a number of minutes to complete. To see if it's still being created, hover your pointer over the **Background tasks** icon in the bottom left corner of the status bar, as shown in the following image.

![](media/get-started-netcore-visualstudio/BackgroundTasks.png)

> [!Note]
Until the development environment is successfully created you cannot debug your application.

## Look at the files added to project
While you wait for the development environment to be created, look at the files that have been added to your project when you chose to use a development environment.

First, you can see a folder named `charts` has been added and within this folder a [Helm chart](https://docs.helm.sh) for your application has been scaffolded. These files are used to deploy your application into the development environment.

You will see a file named `Dockerfile` has been added. This file has information needed to package your application in the standard Docker format. A `HeaderPropagation.cs` file is also created, we will discuss this file later in the walkthrough. 

Lastly, you will see a file named `vsce.yaml`, which contains configuration information that is needed by the development environment, such as whether the application should be accessible via a public endpoint.

![](media/get-started-netcore-visualstudio/ProjectFiles.png)

> [!div class="nextstepaction"]
> [Debug a container in Kubernetes](get-started-netcore-visualstudio-04.md)