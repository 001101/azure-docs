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

# Tutorial: Deploy Java web apps to Azure App Service with Jenkins for continuous integration and deployment

This tutorial shows how you can deploy a sample Java web app from GitHub to 
[Azure App Service Web Apps on Linux](/azure/app-service/containers/app-service-linux-intro) 
by setting up continuous integration and deployment (CI/CD) using Jenkins. 
The sample web app was developed using the 
[Spring Boot](http://projects.spring.io/spring-boot/) framework. 

In this tutorial, you'll complete these tasks:

> [!div class="checklist"]
> * Install and set up the Azure App Service Jenkins plug-ins so you can deploy to Azure App Service.
> * Define a Jenkins pipeline job that builds your web app from a GitHub repo when a new commit is pushed.
> * Deploy the sample app to Azure App Service with a manual build.
> * Trigger a Jenkins build and update the web app by pushing changes to GitHub.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## Prerequisites

To complete this tutorial, you need these items:

* A [Jenkins](https://jenkins.io/) server with the Java Development Kit (JDK) 
and Maven tools installed on an Azure Linux VM

  If you don't have a Jenkins server, 
  complete these steps now in the Azure portal: 
  [Create Jenkins server on an Azure Linux VM](/azure/jenkins/install-jenkins-solution-template)

* A [GitHub](https://github.com) account

* [Azure CLI](/cli/azure), either from your local command line 
or in the [Azure Cloud Shell](/azure/cloud-shell/overview)

## Install Jenkins plug-ins

1. Sign in to your Jenkins web console at 
`https://<your-Jenkins-server-name>.<Azure-region>.cloudapp.azure.com`. 

1. From the left menu, select **Manage Jenkins** > **Manage Plugins**.

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

## Set up Jenkins for GitHub

When new commits get pushed to your local GitHub repo where 
you'll have your sample Java web app, you want Jenkins 
to receive those [events from GitHub](https://developer.github.com/webhooks/). 
To get these events in Jenkins, enable webhooks for GitHub. 

1. On your Jenkins web console's **Manage Jenkins** page, 
select **Configure System**. 

   ![Configure system](media/jenkins-java-quickstart/manage-jenkins-configure-system.png)

1. On the configuration page, scroll to the **GitHub** section. 
If you haven't already provided details for your GitHub server, 
select **Add GitHub Server** > **GitHub Server**. 

   ![Add GitHub server](media/jenkins-java-quickstart/add-GitHub-server.png)

1. Check that the **Manage hooks** property appears selected. 
Choose **Advanced** so you can choose addtional settings. 

   ![Add GitHub server](media/jenkins-java-quickstart/advanced-GitHub-settings.png)

1. Choose **Manage additional GitHub actions**, 
and select **Convert login and password to token**.

   ![Add GitHub server](media/jenkins-java-quickstart/manage-additional-actions.png)

1. Select **From login and password**, 
enter your GitHub username and password, 
and choose **Create token credentials**, 
which creates a [GitHub Personal Access Token (PAT)](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).   

   ![Create GitHub PAT from login and password](media/jenkins-java-quickstart/create-github-token-credentials.png)

1. Now under **GitHub Server**, open the **Credentials** list, 
and select your new token. Check that authentication is working 
by choosing **Test connection**.

   ![Check connection to GitHub server with new PAT](media/jenkins-java-quickstart/check-github-connection.png)

> [!NOTE]
> 
> If your GitHub account has two-factor authentication enabled, 
> create your token on GitHub and set up Jenkins to use that token. 
> For more information, see the documentation for the 
> [Jenkins GitHub plug-in](https://wiki.jenkins.io/display/JENKINS/Github+Plugin).

## Set up Jenkins for App Service

Now you'll create a service principal for Jenkins.

1. From your Jenkins web console home page, 
select your previously created pipeline job, 
which is **MyJavaWebApp** in this example. 

   ![Check connection to GitHub server with new PAT](media/jenkins-java-quickstart/select-pipeline-job.png)

1. On the left menu, select **Configure**.

1. Scroll to the **Post-build Actions** section, 
and select **Add post-build action** > 
**Publish an Azure Web App**.

   If you can't find the **Post-build Actions** section, 
   check that your job is a pipeline job.

1. After the **Azure Profile Configuration** section appears, next to 
the **Azure Credentials** box, choose **Add**, and select **Jenkins**.

1. In the **Jenkins Credentials Provider: Jenkins** dialog box, 
under **Add Credentials**, from the **Kind** list, select 
**Microsoft Azure Service Principal**.

1. By using either the Azure CLI or [Cloud Shell](/azure/cloud-shell/overview), 
create an Active Directory Service Principal, for example:

   ```azurecli-interactive
   az ad sp create-for-rbac --name yourServicePrincipalName --password yourSecurePassword
   ```

   ```json
   {
      "appId": "<yourWebAppID>",
      "displayName": "<yourServicePrincipalName>",
      "name": "http://<yourServicePrinicipalName",
      "password": "<yourSecurePassword>",
      "tenant": "<yourTenantID>"
   }
   ```

1. In the **Add Credentials** section, enter your 
service principal's credentials, for example:

   ![Set up Azure Service Principal](media/jenkins-java-quickstart/azure_service_principal.png)

   If you don't know your Azure subscription ID, 
   you can retrieve the ID by using the Azure CLI:

   ```azurecli-interactive
   az account list
   ```

   ```json
   {
      "cloudName": "AzureCloud",
      "id": "<yourAzureSubscriptionID>",
      "isDefault": true,
      "name": "<yourAzureSubscriptionName>",
      "state": "Enabled",
      "tenantId": "<yourAzureTenantID>",
      "user": {
         "name": "<yourAzureUsername>",
         "type": "user"
      }
   }
   ```

1. Make sure your service principal authenticates with 
Azure by choosing **Verify Service Principal**. 
When you're done, choose **Add**.

1. After you're returned to the **Publish an Azure Web App** section, 
under **Azure Profile Configuration**, from the **Azure Credentials** list, select your newly created service principal's credentials.

1. Under **App Configuration**, select the **Resource Group Name** 
and **App Name** for your web app, and then choose **Save**.  

   Here's what your finished post-build action might look like:   

   ![Post-build action settings for deployment to Azure App Service](media/jenkins-java-quickstart/appservice_plugin_configuration.png)

1. When you're done setting up the job, choose **Save**.

## Fork sample repo and create pipeline job 

Now you'll create a local copy of the GitHub repo that has the sample Java web app. 
You'll then create a pipeline job for building that app.

1. Go to the [GitHub repo that has the Spring Boot sample app](https://github.com/spring-guides/gs-spring-boot), 
and fork the repo to your GitHub account by choosing **Fork**.

   ![Fork sample repo from GitHub](media/jenkins-java-quickstart/fork-github-repo.png)

1. Return to your Jenkins web console home page, 
and select **New Item**. 

   ![Select "New Item"](media/jenkins-java-quickstart/jenkins-select-new-item.png)

1. Provide a name for your sample app, 
for example, "MyJavaWebApp", and select **Pipeline**. 
At the bottom, choose **OK**.   

   ![Select "Pipeline"](media/jenkins-java-quickstart/jenkins-select-pipeline.png)

1. On the **General** tab, select **Prepare an environment for the run**. 

1. In the **Properties Content** box that appears, 
add these environment variables:

   ```text
   AZURE_CRED_ID=<ID-for-your-service-principal-credentials>
   RES_GROUP=<Azure-resource-group-for-your-web-app>
   WEB_APP=<your-web-app-name>
   ```

1. In your local repo's `src/main/resources/` folder, 
create a file named `web.config` that contains this XML:

   ```xml
   <?xml version="1.0" encoding="UTF-8">
   <configuration>
      <system.webServer>
         <handlers>
            <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
         </handlers>
         <httpPlatform processPath="%JAVA_HOME%\bin\java.exe"
        arguments="-Djava.net.preferIPv4Stack=true -Dserver.port=%HTTP_PLATFORM_PORT% -jar &quot;%HOME%\site\wwwroot\${JAR_FILE_NAME}&quot;"></httpPlatform>
      </system.webServer>
   </configuration>
   ```

1. In this XML file, replace `$(JAR_FILE_NAME)` with `gs-spring-boot-0.1.0.jar`.

1. In the root folder for your local repo, create a script 
file named `Jenkinsfile` that contains this text 
([source in GitHub here](https://github.com/Microsoft/todo-app-java-on-azure/blob/master/doc/resources/jenkins/Jenkinsfile-webapp-se)):

   ```text  
   node {
      stage('init') {
         checkout scm
      }
      stage('build') {
         sh '''
            mvn clean package
            cd target
            cp ../src/main/resources/web.config web.config
            cp todo-app-java-on-azure-1.0-SNAPSHOT.jar app.jar 
            zip todo.zip app.jar web.config
         '''
      }
      stage('deploy') {
         azureWebAppPublish azureCredentialsId: env.AZURE_CRED_ID,
         resourceGroup: env.RES_GROUP, appName: env.WEB_APP, filePath: "**/todo.zip"
      }
   }
   ```

1. Commit these files to your local repo, and push your changes.

1. In the **Pipeline** section, from the **Definition** list, 
select **Pipeline script from SCM**. 

   1. In the **SCM** box that appears, 
   select **Git** as your source control. 

   1. In the **Repositories** section, add your repo's URL, 
   including your fork's location. 
   
   1. In the **Script Path** box, 
   add the path to your script, 
   which is "Jenkinsfile" in this example. 

   1. When you're done, choose **Save**.

1. To test your pipeline job, go to your project page in Jenkins, 
and select **Build Now**.

   ![Test build your project](media/jenkins-java-quickstart/test-build-project.png)

## Set up Azure App Service 

By using either the Azure CLI or [Cloud Shell](/azure/cloud-shell/overview), 
create a new [Web App on Linux](/azure/app-service/containers/app-service-linux-intro). 
This tutorial uses the "MyJavaWebApp" as the web app name, 
but make sure you use a unique name for your own app.
   
```azurecli-interactive
az group create --name yourJenkinsResourceGroup --location yourAzureRegion
az appservice plan create --is-linux --name yourLinuxAppServicePlan --resource-group yourJenkinsResourceGroup 
az webapp create --name myJavaWebApp --resource-group yourJenkinsResourceGroup --plan yourLinuxAppServicePlan --runtime "java|1.8|Tomcat|8.5"
```

## Deploy app from GitHub

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

## Troubleshooting Jenkins plugin

If you encounter any bugs with the Jenkins plugins, 
file an issue in the [Jenkins JIRA](https://issues.jenkins-ci.org/) for the specific component.

## Next steps

> [!div class="nextstepaction"]
> [Use Azure VMs as build agents](/azure/jenkins/jenkins-azure-vm-agents)