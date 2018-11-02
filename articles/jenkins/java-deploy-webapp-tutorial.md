---
title: Tutorial - Deploy Java web apps from GitHub to Azure with Jenkins
description: Set up continuous integration for Java web apps from GitHub to Azure App Service by using Jenkins
services: jenkins
ms.service: jenkins
keywords: jenkins, azure, devops, app service, continuous integration, ci, continuous deployment, cd
author: tomarcher
ms.author: tarcher
manager: jeconnoc
ms.topic: tutorial
ms.date: 11/01/2018
---

# Tutorial: Deploy Java web apps to Azure App Service by using Jenkins for continuous integration and deployment

This tutorial shows how you can deploy a sample Java web app to 
[Azure App Service Web Apps on Linux](/azure/app-service/containers/app-service-linux-intro) 
by setting up continuous integration and deployment (CI/CD) using Jenkins. 
The sample web app was developed using the 
[Spring Boot](http://projects.spring.io/spring-boot/) framework. 

In this tutorial, you'll complete these tasks:

> [!div class="checklist"]
> * Install and set up the Azure App Service Jenkins plug-ins so you can deploy to Azure App Service.
> * Define a Jenkins job that builds Docker images from a GitHub repo when a new commit is pushed.
> * Define and set up an Azure Web App for deploying Docker images that are pushed to Azure Container registry.
> * Deploy the sample app to Azure App Service with a manual build.
> * Trigger a Jenkins build and update the web app by pushing changes to GitHub.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## Prerequisites

To complete this tutorial, you need these items:

* A [Jenkins](https://jenkins.io/) server with the Java Development Kit (JDK) 
and Maven tools installed on an Azure Linux VM

  If you don't have a Jenkins server, complete these steps now in the Azure portal: 
  [Create Jenkins server on an Azure Linux VM](/azure/jenkins/install-jenkins-solution-template)

* A [GitHub](https://github.com) account

* The [Azure CLI](/cli/azure), either from your local command line 
or in the [Azure Cloud Shell](/azure/cloud-shell/overview)

## Install Jenkins plug-ins

1. In your web browser, go to your Jenkins web console and sign in. 
From the left menu, select **Manage Jenkins** > **Manage Plugins**.

   ![Manage Jenkins plug-ins](media/jenkins-java-quickstart/manage-jenkins-plugins.png)

1. On the **Available** tab, find and select these plug-ins:

   - [Azure App Service](https://plugins.jenkins.io/azure-app-service)
   - [GitHub Branch Source](https://plugins.jenkins.io/github-branch-source)
   - Jenkins [Environment Injector Plugin](https://plugins.jenkins.io/envinject)    

   If the plugins don't appear, make sure they're not 
   already installed by checking the **Installed** tab.

1. To install the selected plug-ins, choose 
**Download now and install after restart**.

1. When you're done, on the left menu, select **Manage Jenkins** 
so you can return to the Jenkins management page.

## Configure GitHub and Jenkins

Now, set up Jenkins so when new commits are pushed 
to your GitHub repo, Jenkins receives the 
[GitHub webhooks](https://developer.github.com/webhooks/).

1. On your Jenkins web console's **Manage Jenkins** page, 
select **Configure System**. 

   ![Configure system](media/jenkins-java-quickstart/manage-jenkins-configure-system.png)

1. Find the **GitHub** section, and view the details for your GitHub Server. 
Make sure the **Manage hooks** property appears selected. 

1. Choose **Advanced** so addtional settings appear. 
Open the **Manage additional GitHub actions** list, 
and select **Convert login and password to token**.

1. Select **From login and password**, 
and enter your GitHub username and password. 
Select **Create token credentials**, 
which creates a new 
[GitHub Personal Access Token](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   

   ![Create GitHub PAT from login and password](media/jenkins-java-quickstart/create_github_credentials.png)

1. Select the newly created token from the **Credentials** drop down in the GitHub Servers configuration. Select **Test connection** to verify that the authentication is working.   

   ![Verify connection to GitHub once PAT is configured](media/jenkins-java-quickstart/verify_github_connection.png)

> [!NOTE]
> If your GitHub account has two-factor authentication enabled,  create the token on GitHub and configure Jenkins to use it. Review the [Jenkins GitHub plug-in](https://wiki.jenkins.io/display/JENKINS/Github+Plugin) documentation for full details.

## Fork sample repo and create pipeline job 

1. Open the [Spring Boot sample application repo](https://github.com/spring-guides/gs-spring-boot-docker) and fork it to your own GitHub account by selecting **Fork** in the top right-hand corner.   
    ![Fork from GitHub](media/jenkins-java-quickstart/fork_github_repo.png)
1. In the Jenkins web console, select **New Item**, give it a name **MyJavaApp**, select **Freestyle project**, then select **OK**.   
    ![New Jenkins Freestyle Project](media/jenkins-java-quickstart/jenkins_freestyle.png)
2. Under the **General** section, select **GitHub** project and enter your forked repo URL such as https://github.com/raisa/gs-spring-boot-docker
3. Under the **Source code management**  section, select **Git**, enter your forked repo `.git` URL such as https://github.com/raisa/gs-spring-boot-docker.git
4. Under the **Build Triggers** section, select **GitHub hook trigger for GITscm polling**.
5. Under the **Build** section, select **Add build step** and choose **Invoke top-level Maven targets**. Enter `package` in the **Goals** field.
6. Select **Save**. You can test your job by selecting **Build Now** from the project page.

## Configure Azure App Service 

1. Using the Azure CLI or [Cloud Shell](/azure/cloud-shell/overview), create a new [Web App on Linux](/azure/app-service/containers/app-service-linux-intro). The web app name in this tutorial is `myJavaApp`, but you need to use a unique name for your own app.
   
    ```azurecli-interactive
    az group create --name myResourceGroupJenkins --location westus
    az appservice plan create --is-linux --name myLinuxAppServicePlan --resource-group myResourceGroupJenkins 
    az webapp create --name myJavaApp --resource-group myResourceGroupJenkins --plan myLinuxAppServicePlan --runtime "java|1.8|Tomcat|8.5"
    ```

2. Create an [Azure Container Registry](/azure/container-registry/container-registry-intro) to store the Docker images built by Jenkins. The container registry name used in this tutorial is `jenkinsregistry`, but you need to use a unique name for your own container registry. 

    ```azurecli-interactive
    az acr create --name jenkinsregistry --resource-group myResourceGroupJenkins --sku Basic --admin-enabled
    ```
3. Configure the web app to run Docker images pushed to the container registry and specify that the app running in the container listens for requests on port 8080.   

    ```azurecli-interactive
    az webapp config container set -c jenkinsregistry/webapp --resource-group myResourceGroupJenkins --name myJavaApp
    az webapp config appsettings set --resource-group myResourceGroupJenkins --name myJavaApp --settings PORT=8080
    ```

## Configure the Azure App Service Jenkins plug-in

1. In the Jenkins web console, select the **MyJavaApp** job you created and then select **Configure** on the left hand of the page.
2. Scroll down to **Post-build Actions**, select **Add post-build action**, and choose **Publish an Azure Web App**.
3. Under **Azure Profile Configuration**, select **Add** next to **Azure Credentials** and choose **Jenkins**.
4. In the **Add Credentials** dialog, select **Microsoft Azure Service Principal** from the **Kind** drop-down.
5. Create an Active Directory Service principal from the Azure CLI or [Cloud Shell](/azure/cloud-shell/overview).
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
6. Enter the credentials from the service principal into the **Add credentials** dialog. If you don't know your Azure subscription ID, you can query it from the CLI:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    ![Configure Azure Service Principal](media/jenkins-java-quickstart/azure_service_principal.png)
6. Verify the service principal authenticates with Azure by selecting **Verify Service Principal**. 
7. Select **Add** to save the credentials.
8. Select the service principal credential you just added from the **Azure Credentials** drop-down when you are back to the **Publish an Azure Web App** configuration.
9. In **App Configuration**, choose your resource group and web app name from the drop-down.
10. Select the **Publish via Docker** radio button.
11. Enter `complete/Dockerfile` for **Dockerfile path**.
12. Enter `https://jenkinsregistry.azurecr.io` in the **Docker registry URL** field.
13. Select **Add** next to **Registry Credentials**. 
14. Enter the admin username for the Azure Container Registry you created for the **Username**.
15. Enter the password for the Azure Container registry in the **Password** field. You can get your username and password from the Azure portal or through the following CLI command:

    ```azurecli-interactive
    az acr credential show -n jenkinsregistry
    ```
    ![Add your container registry credentials](media/jenkins-java-quickstart/enter_acr_credentials.png)
15. Select **Add** to save the credential.
16. Select the newly created credential from the **Registry credentials** drop-down in the **App Configuration** panel for the **Publish an Azure Web App**. The finished post-build action should look like the following image:   
    ![Post build action configuration for Azure App Service Deploy](media/jenkins-java-quickstart/appservice_plugin_configuration.png)
17. Select **Save** to save the job configuration.

## Deploy the app from GitHub

1. From the Jenkins project, select **Build Now** to deploy the sample app to Azure.
2. Once the build completes, your app is live on Azure at its publishing URL, for example http://myjavaapp.azurewebsites.net.   
   ![View your deployed app on Azure](media/jenkins-java-quickstart/hello_docker_world_unedited.png)

## Push changes and redeploy

1. From your Github fork, browse on the web to  `complete/src/main/java/Hello/Application.java`. Select the **Edit this file** link from the right-hand side of the GitHub interface.
2. Make the following change to the `home()` method and commit the change to the repo's master branch.
   
    ```java
    return "Hello Docker World on Azure";
    ```
3. A new build starts in Jenkins, triggered by the new commit on the `master` branch of the repo. Once it completes, reload your app on Azure.     
      ![View your deployed app on Azure](media/jenkins-java-quickstart/hello_docker_world.png)

## Troubleshooting the Jenkins plugin

If you encounter any bugs with the Jenkins plugins, file an issue in the [Jenkins JIRA](https://issues.jenkins-ci.org/) for the specific component.

## Next steps

> [!div class="nextstepaction"]
> [Use Azure VMs as build agents](/azure/jenkins/jenkins-azure-vm-agents)