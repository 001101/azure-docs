---
title: Using the Azure Dev Spaces Plugin for Jenkins with Azure Kubenetes Service
description: Learn how to use the Azure Dev Spaces plugin in a continuous integration pipeline.
author: tomarchermsft
ms.author: tarcher
ms.service: jenkins
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/26/19
---
<!-- GMinchAQ, 06/05/10 -->

# Tutorial: Using the Azure Dev Spaces Plugin for Jenkins with Azure Kubenetes Service 

Azure Dev Spaces allows you to test and iteratively develop your microservice application running in Azure Kubernetes Service (AKS) without the need to replicate or mock dependencies. With the Azure Dev Spaces plugin for Jenkins, you can use dev spaces in your continuous integration and delivery (CI/CD) pipeline. That means you can check your code into a dev branch and deploy the update to a child dev space in AKS to run end to end integration test against your codes before merging into the main branch.

The Azure Dev Spaces plugin helps to create a dev space in AKS <!--and returns the environment variable azdsprefix so that you can append the prefix to your test end point.--> You can then leverage your integration test scripts to run end to end continuous integration against what you have just updated before merging codes to the main branch.

This tutorial also leverages Azure Container Registry (ACR) . <!--todo stores images, acrquicktasks builds docker and helm artifacts--> That way, you do not need to install Docker daemon and software needed for build in your server. <!-- todo edit -->

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Create a dev spaces-enabled AKS cluster
> * Manage Azure Dev Spaces in a Jenkins pipeline
> * Deploy from a dev space to AKS

The scenario selected for the example pipeline is based on a real-world pattern: A pull request triggers a CI pipeline that builds and then deploys the proposed changes to an Azure dev space for testing and review. Depending on the outcome of the review, the changes are either merged or discarded, and then the dev space is removed. This tutorial shows how use Azure Dev Spaces and ACR Tasks (in conjunction with other plugins) to build a functional CI pipeline with a relatively small amount of code.

The following flowchart shows the stages in the Jenkinsfile:

![Jenkins pipeline flow](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-flow.png)

The focus is on the following points:

* Using the Azure Dev Spaces plugin to create and remove dev spaces
<!--todo-->

## Prerequisites

* An Azure account. If you don’t have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

* A Github account. If you don't have a GitHub account, create a [free account](https://github.com/) before you begin.

* [Visual Studio Code](https://code.visualstudio.com/download) with the [Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) extension installed.

* [Azure CLI installed](/cli/azure/install-azure-cli?view=azure-cli-latest), version 2.0.43 or higher, with the Azure Dev Spaces CLI (`azds`) installed.

* A Jenkins master server. If you don't already have a Jenkins master, deploy [Jenkins](https://aka.ms/jenkins-on-azure) on Azure by following the steps in this [quickstart](https://docs.microsoft.com/en-us/azure/jenkins/install-jenkins-solution-template). The Jenkins server must have the following plugins installed and enabled:
  * Azure Container Registry Tasks.
  * Azure Dev Spaces (to be installed in this tutorial).
  * GitHub Integration.

* The Jenkins server must have both Helm and kubectl installed and available to the Jenkins account, as explained later in this tutorial.

* This tutorial assumes you are using VS Code, and the VS Code Terminal or WSL, and Bash. Other combinations will also work provided that all the prerequisites are in place. **Note:** Azure Cloud Shell does not currently support the Azure Dev Spaces extension (azds).

* This is an intermediate to advanced tutorial. It requires at least intermediate knowledge of core Azure services, AKS, ACR, Azure Dev Spaces, Jenkins [pipelines](https://jenkins.io/doc/book/pipeline/) and plugins, and GitHub. At least basic familiarity with supporting tools such as kubectl and Helm is helpful.

## Create Azure resources

In this section you create Azure resources:

* A resource group to contain all of the Auzre resources for this tutorial.
* An [Azure Kubernetes Service](https://docs.microsoft.com/en-us/azure/aks/) (AKS) cluster.
* An [Azure container registry](https://docs.microsoft.com/en-us/azure/container-registry/) (ACR) to build (using ACR Tasks) and store Docker images.

1. Create a resource group.

    ```bash
    az group create --name MyResourceGroup --location westus2
    ```

2. Create an AKS cluster. You need to create the AKS cluster in a [region that supports Dev Spaces](https://docs.microsoft.com/azure/dev-spaces/#a-rapid,-iterative-kubernetes-development-experience-for-teams).

    ```bash
    az aks create --resource-group MyResourceGroup --name MyAKS --location westus2 --kubernetes-version 1.11.9 --enable-addons http_application_routing --generate-ssh-keys --node-count 1 --node-vm-size Standard_D1_v2
    ```

3. Configure AKS to use Dev Spaces. This will also install the `azds` CLI extension.

    ```bash
    az aks use-dev-spaces --resource-group MyResourceGroup --name MyAKS
    ```

4. Create a container registry.

    ```bash
    az acr create -n MyACR -g MyResourceGroup --sku Basic --admin-enabled true
    ```

## Deploy sample apps to the AKS cluster

In this section you set up a dev space and deploy a sample application to the AKS cluster you created in the last section. The application consists of two parts, *webfrontend* which calls *mywebapi*. Both components are deployed in a dev space. Later in this tutorial, you submit a pull request against mywebapi to trigger the CI pipeline in Jenkins.

This procedure is condensed from two tutorials, [Get started on Azure Dev Spaces with Java](https://docs.microsoft.com/en-us/azure/dev-spaces/get-started-java), and [Multi-service development with Azure Dev Spaces](https://docs.microsoft.com/en-us/azure/dev-spaces/multi-service-java). Those tutorials provide additional background information not included here.

1. Download the code for *webfrontend* from GitHub by navigating to https://github.com/Azure/dev-spaces, and then select **Clone or Download** to download the GitHub repository to your local environment.

2. Launch VS Code and open the `samples/java/getting-started/webfrontend` folder. (You can ignore any default prompts to add debug assets or restore the project.)

3. In VS Code Explorer, navigate to `/src/main/java/com/ms/sample/webfrontend`, and then edit `Application.java` to look like the following:

    ```java
    package com.ms.sample.webfrontend;

    import java.io.*;
    import java.net.*;
    import org.springframework.boot.SpringApplication;
    import org.springframework.boot.autoconfigure.SpringBootApplication;
    import org.springframework.web.bind.annotation.*;

    @SpringBootApplication
    @RestController
    public class Application {
        public static void main(String[] args) {
            SpringApplication.run(Application.class, args);
        }

        @RequestMapping(value = "/greeting", produces = "text/plain")
        public String greeting(@RequestHeader(value = "azds-route-as", required = false) String azdsRouteAs) throws Exception {
            URLConnection conn = new URL("http://mywebapi/").openConnection();
            conn.setRequestProperty("azds-route-as", azdsRouteAs); // propagate dev space routing header
            try (BufferedReader reader = new BufferedReader(new InputStreamReader(conn.getInputStream())))
            {
                return "Hello from webfrontend and " + reader.lines().reduce("\n", String::concat);
            }
        }
    }
    ```

4. Open the Integrated Terminal in VS Code (using the **View > Terminal** menu).

5. Run this command (make sure you are in the folder `dev-spaces/samples/java/getting-started/webfrontend`):

    ```bash
    azds prep --public
    ```

    The Azure CLI's `azds prep` command generates Docker and Kubernetes assets with default settings. These files persist for the lifetime of the project, and they can be customized:

    * `./Dockerfile` describes the app's container image, and how the source code is built and runs within the container.
    * A [Helm chart](https://helm.sh/docs/developing_charts/) under `./charts/webfrontend` describes how to deploy the container to Kubernetes.
    * A file named `./azds.yaml` is also generated by the `prep` command. It is the configuration file for Azure Dev Spaces.

6. Build and run the application in AKS by running this command from the `webfrontend` folder:

    ```bash
    azds up
    ```

7. Scan the console output for information about the public URL that was created by the `up` command. It will be in the form:

    ```cmd
    (pending registration) Service 'webfrontend' port 'http' will be available at <url>
    Service 'webfrontend' port 80 (TCP) is available at 'http://localhost:<port>'
    ```

    Open this URL in a browser window, and you should see the web app. As the container executes, `stdout` and `stderr` output is streamed to the terminal window.

8. Next, set up and deploy *mywebapi*:

    1. Change directory to `dev-spaces/samples/java/getting-started/mywebapi`

    2. Run

        ```bash
        azds prep --public
        ```

    3. Run

        ```bash
        azds up -d
        ```

## Prepare Jenkins server

In this section you prepare the Jenkins server to run the pipeline.

* Install plugins
* Install Helm and Kubernetes CLI
* Add credentials

### Install plugins

1. Log in to your Jenkins server. Choose **Manage Jenkins > Manage Plugins**.
2. On the **Available** tab, select the following plugins:
    * Azure Container Registry Tasks Plugin
    * EnvInject Plugin
    * GitHub Integration Plugin

    If these plug-ins don't appear, check the **Installed** tab to see if they are already installed.

3. To install the plugins, choose **Download now and install after restart**.

4. Azure Dev Spaces plugin is currently in preview, and it will not appear in the list of available plugins. You need to install it manually:
    1. Download the latest version from https://aka.ms/azjenkinsazds.
    2. In Jenkins Plugin Manager, go to the **Advanced** tab.
    3. Use **Upload Plugin** to to upload the .hpi file.

5. Restart your Jenkins server to complete the installation.

### Install Helm and kubectl

The sample pipeline requires Helm and kubectl to deploy from the dev space to AKS. When Jenkins is installed, it creates an admin account named *jenkins*. Both Helm and kubectl need to be accessible to the jenkins user. If you have forgotten the password for jenkins, consult the Jenkins documentation.

1. Switch to the `jenkins` user:
    ```bash
    sudo su jenkins
    ```

2. Install the Helm CLI. For more information, see [Installing Helm](https://helm.sh/docs/using_helm/#installing-helm).

3. Install kubectl. For more information, see [**az acs kubernetes install-cli**](https://helm.sh/docs/using_helm/#installing-helm).

### Add credentials to Jenkins

1. Jenkins needs an Azure service principal for authenticating and accessing Azure resources. To create the service principal, Refer to the [Create service principal](https://docs.microsoft.com/en-us/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#create-service-principal) section in the Deploy to Azure App Service tutorial. Be sure to save a copy of the output from `create-for-rbac` because you need that information to complete the next step. The output will look something like this:

    ```json
    {
      "appId": "f4150da1-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "displayName": "xxxxxxxjenkinssp",
      "name": "http://xxxxxxxjenkinssp",
      "password": "f6e4d839-xxx-xxxx-xxx-xxxxxxxxxxxx",
      "tenant": "72f988bf--xxx-xxxx-xxx-xxxxxxxxxxxx"
    }
    ```

2. Add a "Microsoft Azure Service Principal" credential type in Jenkins, using the service principal information from the previous step. Refer to the [Add service principal to Jenkins](https://docs.microsoft.com/en-us/azure/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service#add-service-principal-to-jenkins) section in the Deploy to Azure App Service tutorial. The credential name (*yourAzureServicePrincipalName* in the following screen clip) is the value of the AZURE_CRED_ID in the next section.

    ![Add service principal credentials to Jenkins](media/tutorial-jenkins-dev-spaces/add-service-principal-credentials.png)

3. To show your ACR credentials, run this command:

    ```bash
    az acr credential show -n <yourRegistryName>
    ```

    Make a copy of the JSON output, which should look something like this:

    ```json
    {
      "passwords": [
        {
          "name": "password",
          "value": "vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz"
        },
        {
          "name": "password2",
          "value": "zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz"
        }
      ],
      "username": "acr01"
    }
    ```

4. Add a "Username with password" credential type in Jenkins. The **username** is the username from the last step, in this example `acr01` The **password** is the value for the first password, in this example `vGBP=zzzzzzzzzzzzzzzzzzzzzzzzzzz`. The name (ID) of this credential is the value of ACR_CRED_ID.

5. Set up an AKS credential. Add a "Kubernetes configuration (kubeconfig)" credential type in Jenkins (use the option "Enter directly"). To get the access credentials for your AKS cluster, run the following command:

    ```cmd
    az aks get-credentials -g MyResourceGroup -n <yourAKSName> -f -
    ```

   This name (ID) of this credential is the value of KUBE_CONFIG_ID in the next section.

## Create a pipeline

1. Download or clone this [repo](https://github.com/gavinfish/mywebapi). This is a modification of the *mywebapi* installed above, and it contains the Jenkinsfile (`Jekinsfile-helm-cli`) for the pipeline.

>TODO -- this repo needs to be cleaned up. It has several jenkinsfiles, plus artifacts from azds prep. The only part that is really necessary is the jenkinsfile-helm-cli. Can we move the key parts to the main dev-spaces repo?

1. Log into Jenkins. From the menu on the left, select **Add Item**.

2. Select **Pipeline**, and then enter a name in the **Enter an item name** box.

3. Enable **Prepare an environment for the run**, and then check the **Keep Jenkins Environment Variables** and **Keep Jenkins Build Variables** boxes.

4. , In the **Properties Content** box, enter the following environment variables:

    ```
    AZURE_CRED_ID=[your credential ID of service principal]
    RES_GROUP=[your resource group of the function app]
    ACR_RES_GROUP=[your ACR resource group]
    ACR_NAME=[your ACR name]
    ACR_REGISTRY=[your ACR registry url, without http schema]
    ACR_CRED_ID=[your credential id of your ACR account]
    AKS_RES_GROUP=[your AKS resource group]
    AKS_NAME=[your AKS name]
    IMAGE_NAME=[name of Docker image you will push to ACR, without registry prefix]
    KUBE_CONFIG_ID=[your kubeconfig id]
    PARENT_DEV_SPACE=[shared dev space name]
    TEST_ENDPOINT=[your web frontend end point for testing. Should be webfrontend.XXXXXXXXXXXXXXXXXXX.xxxxxx.aksapp.io]
    ```

    Using the sample values given in the preceding sections, the list of environment variables should look something like this, with substitutions for the actual values:

    ![Jenkins pipeline environment variables](media/tutorial-jenkins-dev-spaces/jenkins-pipeline-environment.png)

4. Choose **Pipeline script from SCM** in **Pipeline > Definition**.
5. In **SCM**, choose **Git** and then enter your repo URL.
6. In **Branch Specifier**, enter `refs/remotes/origin/${GITHUB_PR_SOURCE_BRANCH}`.
7. Fill in the SCM repo url and script path "Jenkinsfile". (Script [Example](/JenkinsFile))
8. **Lightweight checkout** should be checked.

## Create a pull request to trigger the pipeline

1. Create a pull request (PR) by editing `Application.java` in `mywebapi/src/main/java/com/ms/sample/mywebapi/`. Change the return string to something like this:

    ```java
    public String index() {
        return "Hello from mywebapi in my private dev space";
    }
    ```

2. If you have a webhook set up, a job will be triggered automatically. Otherwise, you can run the job manually.  For more information about setting up a webhook, see [Connect Jenkins to GitHub](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/jenkins/tutorial-jenkins-deploy-web-app-azure-app-service.md#connect-jenkins-to-github).

3. Open your favorite browser and input `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`

    Open another tab and input the PR dev space URL. Should be 
    `https://<yourdevspace>.s.webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io`. You will find the link in the console output of the Jenkins job you just triggered.

    Notice how with the dev space prefix, your call is routed to the updated mywebapi while `https://webfrontend.XXXXXXXXXXXXXXXXXXX.eastus.aksapp.io` is still pointing to the (team's) shared version. The dev space prefix ($env.azdsprefix) is set by the Azure Dev Spaces plugin (in the 'create dev space' stage.)

## Clean up resources

If you're not going to continue to use this application, delete the resource group you created for this tutorial with the following command:

```bash
az group delete -y --no-wait -n MyResourceGroup
```

## Next steps
>**TODO:** add a list of next steps

Advance to the next article to learn how to create...
> [!div class="nextstepaction"]
> [Next steps button](contribute-get-started-mvc.md)

