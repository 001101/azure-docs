---
title: Connect your AWS account to Azure Security Center
description: Monitoring your AWS resources from Azure Security Center
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin

---

#  Connect your AWS accounts to Azure Security Center

With cloud workloads commonly spanning multiple cloud platforms, cloud security services must do the same.

Azure Security Center protects workloads in Azure, Amazon Web Services (AWS), and Google Cloud Platform (GCP).

When you onboard your AWS account into Security Center you'll benefit from having a single security solution that provides visibility and protection across all major cloud environments, as well as the following:

- Detection of security misconfigurations
- A single view showing Security Center recommendations and AWS Security Hub findings
- Incorporation of your AWS resources into Security Center's secure score calculations
- Regulatory compliance assessments of your AWS resources


## Availability

|Aspect|Details|
|----|:----|
|Release state:|Preview|
|Pricing:|Requires [Azure Defender for servers](defender-for-servers-intro.md)|
|Required roles and permissions:|**Owner** or **Contributor** on the relevant Azure Subscription|
|Clouds:|![Yes](./media/icons/yes-icon.png) Commercial clouds<br>![No](./media/icons/no-icon.png) National/Sovereign (US Gov, China Gov, Other Gov)|
|||


## What's involved in connecting AWS to Security Center?

Connecting your AWS accounts requires [Azure Defender for servers](defender-for-servers-intro.md). 

**Azure Defender for servers** deploys the Log Analytics agent to your AWS instances. With the help of [Azure Arc](../azure-arc/servers/overview.md), this provides Security Center features such as:

- Automatic agent provisioning
- Policy management
- Vulnerability management
- Embedded Endpoint Detection and Response (EDR)


## Connect your AWS account

### Step 1 - Set up AWS Security Hub:

1. To view security recommendations for multiple regions, repeat the following steps for each relevant region.

    > [!IMPORTANT]
    > If you're using a master account, repeat the following three steps to configure the master account and all connected member accounts across all relevant regions

    1. Enable [AWS Config](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html).
    1. Enable [AWS Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html).
    1. Verify that there is data flowing to the Security Hub.

        When you first enable Security Hub, it may take several hours for data to be available.

### Step 2 - Create an IAM role for Security Center:

1. From your Amazon Web Services console, under **Security, Identity & Compliance**, select **IAM**.

    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="AWS services":::

1. Select **Roles** and **Create role**.

1. Select **Another AWS account**.

1. Enter the following details:

    - **Account ID** - enter the Microsoft Account ID (**158177204117**) as shown in the AWS connector page in Security Center.
    - **Require External ID** - selected
    - **External ID** - enter the subscription ID as shown in the AWS connector page in Security Center 

1. Select **Next**.

1. In the **Attach permission policies** section, select the following policies:

    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess

1. Optionally add tags. Adding Tags to the user doesn't affect the connection.

1. Select **Next**.

1. Save the Amazon Resource Name (ARN) for later. 

### Step 3 - Create AWS user for Security Center 
1. Open the **Users** tab and select **Add user**.
1. In the **Details** step, enter a username for Security Center and ensure that you select **Programmatic access** for the AWS Access Type. 
1. Select **Next Permissions**.
1. Select **Attach existing policies directly** and apply the following policies:
    - AmazonEC2RoleforSSM
    - SecurityAudit
1. Select **Next: Tags**. Optionally add tags. Adding Tags to the user doesn't affect the connection.
1. Select **Review**.
1. Save the automatically generated **Access key ID** and **Secret access key** CSV file for later.
1. Review the summary and click **Create user**.


### Step 4 - Configure SSM

AWS Systems Manager is required for automating tasks across your AWS resources. If your EC2 instances don't have the SSM Agent, follow the relevant instructions from Amazon:

- [Installing and Configuring SSM Agent on Windows Instances](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [Installing and Configuring SSM Agent on Amazon EC2 Linux Instances](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### Step 5 - Create a Service Principal for onboarding at scale

As an **Owner** on the subscription you want to use for the onboarding, create a a service principal for Azure Arc onboarding as described in [Create a Service Principal for onboarding at scale](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)


### Step 6 - Connect AWS to Security Center

1. From Security Center's menu, select **Multi cloud connectors**.

1. Select **Add AWS account**.

    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Add AWS account button on Security Center's multi cloud connectors page":::

1. Configure the options in the **AWS authentication** tab:

    1. Enter a **Display name** for the connector.
    1. Confirm that the subscription is correct. It is the subscription that will include the connector and AWS Security Hub recommendations.
    1. Select the authentication method **Assume Role** and paste the ARN from [Step 2 - Create an IAM role for Security Center:](#step-2---create-an-iam-role-for-security-center).

        :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="Pasting the ARN file in the relevant field of the AWS connection wizard in the Azure Portal":::
    1. Select **Credentials** and paste the **access key** and **secret key** from the .csv file you saved in [Step 3 - Create AWS user for Security Center](#step-3---create-aws-user-for-security-center).
    1. Select **Next**.

1. Configure the options in the **Azure Arc Configuration** tab:

    Security Center discovers the EC2 instances in the connected AWS account and uses SSM to onboard them to Azure Arc. 

    > [!TIP]
    > The list of supported operating systems is in the FAQ below.

    1. Select the **Resource Group** and **Azure Region** that the discovered AWS EC2s will be onboarded to in the selected subscription.
    1. Enter the **Service Principal ID** and **Service Principal Client Secret** for Azure Arc as described here [Create a Service Principal for onboarding at scale](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)
    1. If the machine is connecting to the internet via a proxy server, specify the proxy server IP address or the name and port number that the machine uses to communicate with the proxy server. Enter the value in the format ```http://<proxyURL>:<proxyport>```
    1. Select **Review + create**.

        Review the summary information

        The Tags sections will list all Azure Tags that will be automatically created for each onboarded EC2 with its own relevant details to easily recognize it in Azure. 

        Learn more about Azure Tags in [Use tags to organize your Azure resources and management hierarchy](../azure-resource-manager/management/tag-resources.md).

### Step 7 - Confirmation

When the connector is successfully created and AWS Security Hub has been configured properly:

- Security Center scans the environment for AWS EC2 instances, onboarding them to Azure Arc, enabling to install the Log Analytics agent and providing threat protection and security recommendations. 
- The ASC service scans for new AWS EC2 instances every 6 hours and onboards them according to the configuration.
- If Security Hub policy is enabled, recommendations will appear in the Security Center portal and the  regulatory compliance dashboard 5-10 minutes after onboard completes.
- The AWS CIS standard will be shown in the Security Center's regulatory compliance dashboard.



## Monitoring your AWS resources

(Description of using recs and a screenshot of some GCP flagged ones) 


## AWS in Security Center FAQ

### What operating systems for my EC2 instances are supported?

Supported OS for automatic onboarding to Azure Arc for AWS Machines

- Ubuntu 16.04 - SSM Agent is preinstalled, by default
- Ubuntu 18.04 -SSM Agent is preinstalled, by default
- Windows server - SSM Agent is preinstalled, by default
- CentOS Linux 7 – SSM should be installed manually or onboard separately
- SUSE Linux Enterprise Server (SLES) 15 (x64) -SSM should be installed manually or onboarded separately
- Red Hat Enterprise Linux (RHEL) 7 (x64) - SSM should be installed manually or onboarded separately



## Next steps

This page provided the step-by-step process for connecting your AWS account to Azure Security Center. To connect a GCP account, see [Connect your GCP accounts to Azure Security Center](quickstart-onboard-gcp.md)