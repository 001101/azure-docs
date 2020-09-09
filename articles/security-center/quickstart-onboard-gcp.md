---
title: Connect your GCP account to Azure Security Center
description: Monitoring your GCP resources from Azure Security Center
author: memildin
ms.author: memildin
ms.date: 9/12/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin

---

#  Connect your GCP accounts to Azure Security Center

With cloud workloads commonly spanning multiple cloud platforms, cloud security services must do the same.

Azure Security Center protects workloads in Azure, Amazon Web Services (AWS), and Google Cloud Platform (GCP).

When you onboard your GCP account into Security Center you'll benefit from having a single security solution that provides visibility and protection across all major cloud environments, as well as the following:

- Detection of security misconfigurations
- A single view showing Security Center recommendations and GCP Security Command Center findings
- Incorporation of your GCP resources into Security Center's secure score calculations
- Integration of GCP Security Command Center recommendations based on the CIS standard into the Security Center's regulatory compliance dashboard


## Availability

|Aspect|Details|
|----|:----|
|Release state:|Preview|
|Pricing:|Requires [Azure Defender for servers](defender-for-servers-intro.md)|
|Required roles and permissions:|**Owner** or **Contributor** on the relevant Azure Subscription|
|Clouds:|![Yes](./media/icons/yes-icon.png) Commercial clouds<br>![No](./media/icons/no-icon.png) National/Sovereign (US Gov, China Gov, Other Gov)|
|||


## What's involved in connecting AWS to Security Center?

Connecting your GCP accounts requires [Azure Defender for servers](defender-for-servers-intro.md). 



## Step 1 - Set up GCP Security Command Center with Security Health Analytics

For all the GCP projects in your organization, you must also:

1. Set up **GCP Security Command Center** using [these instructions from the GCP documentation](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup).
1. Enable **Security Health Analytics** using [these instructions from the GCP documentation](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics).
1. Verify that there is data flowing to the Security Command Center.

The instructions for connecting your GCP environment for security configuration follow Google's recommendations for consuming security configuration recommendations. The integration leverages Google Security Command Center and will consume additional resources that might impact your billing.

When you first enable Security Health Analytics, it might take several hours for data to be available.


## Step 2 - Enable GCP Security Command Center API

1. From Google's **Cloud Console API Library**, select the project you want to connect to Azure Security Center.
1. In the API Library, find and select **Security Command Center API**.
1. On the API's page, select **ENABLE**.

Learn more about the [Security Command Center API](https://cloud.google.com/security-command-center/docs/reference/rest/).


## Step 3 - Create a dedicated service account for the security configuration integration

1. In the **GCP Security Command Center**, select the project you want to connect to Security Center.
1. From the **IAM & admin** options, select **Service accounts**.
1. Select **CREATE SERVICE ACCOUNT**.
1. Enter an account name, and select **Create**.
1. Specify the **Role** as **Security Center Admin Viewer**, and select **Save**.
1. Copy the **Email value**, and save it for later use.
1. Under IAM & admin, select IAM.
    1. Switch to organization level.
    1. Select **ADD**.
    1. In the **New members** field, paste the **Email value** you copied earlier.
    1. Specify the Role as Security Center Admin Viewer and then select Save.
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="Setting the relevant GCP permissions":::


## Step 4 - Create a private key for the dedicated service account
1. Switch to project level.
1. Under IAM & admin, select Service accounts.
1. Open the dedicated service account and select Edit.
1. Select CREATE KEY.
1. In the Create private key screen, select JSON, and then select CREATE.
1. Safe this JSON file for later use.


## Step 5 - Connect GCP to Security Center 
1. In Security Center, select Pricing & Settings in the menu
1. Select the Azure subscription you would like to onboard your GCP organization to.
1. Select Multi-cloud connectors and then on add GCP account
1. In the onboarding blade, do the following and then select Next
    1. Validate the chosen subscription
    1. In the Display name box, Enter a display name for the connector
    1. In the Organization ID box, enter the organization you made a note of earlier.
    1. In the Private key file box, browse to the JSON file you downloaded in [Step 4 - Create a private key for the dedicated service account](#step-4---create-a-private-key-for-the-dedicated-service-account).


### Step 7 - Confirmation

When the connector is successfully created and GCP Security Command Center Hub has been configured properly:

- Security Center scans the environment for GCP machines, onboarding them to Azure Arc, enabling to install the Log Analytics agent and providing threat protection and security recommendations. 
- The ASC service scans for new GCP machines every **6 hours ???** and onboards them according to the configuration.
- Security recommendations will appear in the Security Center portal and the regulatory compliance dashboard 5-10 minutes after onboard completes.
- The GCP CIS standard will be shown in the Security Center's regulatory compliance dashboard.


## Monitoring your GCP resources

(Description of using recs and a screenshot of some GCP flagged ones) 


## Next steps

More stuff