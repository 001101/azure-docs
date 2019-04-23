---
title: Continuously deliver function code updates using Azure DevOps
description: Learn how to set up an Azure DevOps pipeline targeting Azure Functions.
author: ahmedelnably
manager: jeconnoc

ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.custom: 
---

# Continuous delivery using Azure DevOps

You can automatically deploy your function to an Azure Function app using [Azure Pipelines](/devops/pipelines/).
To define your pipeline, you can use:

- YAML File: This file describes the pipeline, it may have a build steps section, and a release section. Usually that YAML file resides in the same repo as the app.

- Templates: Templates are ready made pipelines that builds or deploys your app.

## YAML Based Pipeline

### Build your app

Building your app in Azure Pipelines depends on the programming language of your app. Each language has specific build steps to create a deployment artifact, that can be used to update your function app in Azure.

### .NET

You can use the following samples to create your YAML file to build your .NET app, note that the vmImage should be changed depending on which Azure Functions hosting OS your app is on:

```yaml
jobs:
  - job: Build
    pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' for Windows Azure Functions
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: true
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output/s"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/$(Build.BuildId).zip'
    name: 'drop'
```

### JavaScript

You can use the following samples to create your YAML file to build your JavaScript app, note that the vmImage should be changed depending on which Azure Functions hosting OS your app is on:

```yaml
jobs:
  - job: Build
    pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' for Windows Azure Functions
steps:
- script: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output/s"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/$(Build.BuildId).zip'
    name: 'drop'
```

## Python

You can use the following samples to create your YAML file to build your Python app, note that Python is only supported for Linux Azure Functions:

```yaml
jobs:
  - job: Build
    pool:
      vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- script: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    python3.6 -m venv worker_venv
    source worker_venv/bin/activate
    pip3.6 install setuptools
    pip3.6 install -r requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output/s"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/$(Build.BuildId).zip'
    name: 'drop'
```

### Deploy your app

Depending on the hosting OS, you will need to include the following YAML sample in your YAML file.

### Windows Function App

The following snippet can be used to deploy to a Windows function app

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
```

### Linux Function App

The following snippet can be used to deploy to a Linux function app

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
```

## Template Based Pipeline

### Build your app

Building your app in Azure Pipelines depends on the programming language of your app. Each language has specific build steps to create a deployment artifact, that can be used to update your function app in Azure. To use the built in build templates, when creating a new build pipeline, choose "Use the classic editor" to create a pipeline using the designer templates

![](media/functions-how-to-azure-devops/classic-editor.png)

After configuring the source of your code, search for Azure Functions build templates, and choose the template that matches your app language.

![](media/functions-how-to-azure-devops/build-templates.png)



### Deploy your app

When creating a new release pipeline, search for Azure Functions release template.

![](media/functions-how-to-azure-devops/release-template.png)



## Creating an Azure Pipeline using the Azure CLI

Using [Azure CLI](/cli/azure/get-started-with-azure-cli.md) you can create a build and release pipeline using YAML files.

### Creating an Azure Pipeline

Using the `devops-build create` [command](/cli/azure/functionapp/devops-build#az-functionapp-devops-build-create) a pipeline will be created to build and release any code changes in your repo. The command will create a new YAML file that defines the build and release pipeline and commit it to your repo.
The pre-requisites for this command depend on the location of your code:

- If your code is in GitHub:

    - You need to have **write** permission (for example, owner) of your subscription.

    - You are the project administrator in Azure DevOps.

    - You have permission to create a GitHub Personal Access Token with sufficient permissions. [GitHub PAT Permission Requirements.](https://aka.ms/azure-devops-source-repos)

    - You have permission to commit to the master branch in your GitHub repository to upload the auto generated YAML file.

- If your code is in Azure Repos:

    - You need to have **write** permission (for example, owner) of your subscription.
 
    - You are the project administrator in Azure DevOps.

## Next steps

+ [Need to add something here](https://www.microsoft.com)
