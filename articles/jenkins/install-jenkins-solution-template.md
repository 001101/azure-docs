---
title: Create a Jenkins server on Azure
description: Install Jenkins on an Azure Linux virtual machine from the Jenkins solution template.
author: mlearned
manager: douge
ms.service: multiple
ms.workload: web
ms.devlang: java
ms.topic: hero-article
ms.date: 08/21/2017
ms.author: mlearned
ms.custom: Jenkins
---

# Create a Jenkins server on an Azure Linux VM from the Azure Portal

This quickstart shows how to install Jenkins on an Ubuntu Linux VM with the tools and plugins configured to work with Azure. When you're finished, you'll have a Jenkins server running in Azure building code from a [GitHub](https://github.com).

## Prerequisites

* An Azure subscription
* SSH command line access on your computer (for example, through the Bash shell or [PuTTY](http://www.putty.org/) )

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## Create the Jenkins VM from the solution template

Open the [marketplace image for Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/azure-oss.jenkins?tab=Overview) in your web browser and select  **GET IT NOW** from the left hand side of the page. Review the pricing details and select **Continue** to open the configuration page in the Azure portal. Select **Create** from the Portal to configure the Jenkins VM. 
   
![Azure portal dialog](./media/install-jenkins-solution-template/ap-create.png)

In the **Configure basic settings** tab:

![Configure basic settings](./media/install-jenkins-solution-template/ap-basic.png)

* Use **Jenkins** for the name of your server.
* Select **SSD** for VM disk type.
* **User name**: must meet length requirements, and must not include reserved words or unsupported characters. Names like "admin" are not allowed.  For more information, see [here](/azure/virtual-machines/windows/faq) for user name and password requirements.
* Select **Password** as the **Authentication type** and enter a password. The password must have an upper case character, a number, and one special character.
* Use **myJenkinsResourceGroup** for the **Resource Group**.
* Choose the **East US** [Azure region](https://azure.microsoft.com/regions/) from the **Location** drop-down.

Select **OK** to proceed to the **Configure additional options** tab.

![Set up additional options](./media/install-jenkins-solution-template/ap-addtional.png)

* Enter a unique domain name to identify the Jenkins server in DNS.

Select **OK**. Once validation passes, select **OK** again from the **Summary** tab. Finally, select **Purchase** to create the Jenkins VM. When your server is ready, you'll get a notification in the Portal:   

![Jenkins is ready notification](./media/install-jenkins-solution-template/jenkins-deploy-notification-ready.png)

## Connect to Jenkins

Navigate to your virtual machine (for example, http://<jenkins_domain>.eastus.cloudapp.azure.com/ ) in  your web browser. The Jenkins console is accessible only through unsecured HTTP and instructions are provided on the page to access the Jenkins console securely using an SSH tunnel.

![Unlock jenkins](./media/install-jenkins-solution-template/jenkins-ssh-instructions.png)

Open your command line and forward the ports using the `ssh` command on the page:

```bash
ssh -L 127.0.0.1:8080:localhost:8080 username@jenkins2517454.eastus.cloudapp.azure.com
```

After you have started your tunnel, navigate to http://localhost:8080/ on your local machine.

Unlock the Jenkins dashboard for the first time with the initial admin password.

![Unlock jenkins](./media/install-jenkins-solution-template/jenkins-unlock.png)

To get a token, SSH into the VM and run `sudo cat /var/lib/jenkins/secrets/initialAdminPassword`.

![Unlock jenkins](./media/install-jenkins-solution-template/jenkins-ssh.png) 

You are asked to install the suggested plugins.

Next, create an admin user for your Jenkins master.

Your Jenkins instance is now ready to use! You can access a read-only view by going to http://\<Public DNS name of instance you just created\>.

![Jenkins is ready!](./media/install-jenkins-solution-template/jenkins-welcome.png)

## Create your first job

Select **Create new jobs** from the Jenkins console, then name it **mySampleApp** and select **Freestyle project**, then select **OK**.

![Create a new job](./media/install-jenkins-solution-template/jenkins-new-job.png) 

Select the **Source Code Management** tab, enable **Git** and enter the following URL in **Repository URL** https://github.com/spring-guides/gs-spring-boot-docker.git

![Define the Git repo](./media/install-jenkins-solution-template/jenkins-job-git-configuration.png) 

Select the **Build** tab, then select **Add build step**, **Invoke Gradle script**. Select **Use Gradle Wrapper** , then enter `complete` in **Wrapper location** and `build` for **Tasks**.

![Use the Gradle wrapper to build](./media/install-jenkins-solution-template/jenkins-job-gradle-config.png) 

Select **Advanced..**, then enter `complete` in the **Root Build script** field. Select **Save**.

![Set advanced settings in the Gradle wrapper build step](./media/install-jenkins-solution-template/jenkins-job-gradle-advances.png) 

## Build the sample to test the job

Select **Build Now** to compile the code and package the sample app. When your build completes, select the **Workspace** link for the project.

![Browse to the workspace to get the JAR file from the build](./media/install-jenkins-solution-template/jenkins-access-workspace.png) 

Navigate to `complete/build/libs` and ensure the `gs-spring-boot-docker-0.1.0.jar` is there to verify that your build was successful.

## Next Steps

> [!div class="nextstepaction"]
> [Add Azure VMs as Jenkins agents](jenkins-azure-vm-agents.md)
