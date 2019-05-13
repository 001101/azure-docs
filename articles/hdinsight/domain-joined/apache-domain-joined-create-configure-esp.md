---
title: Create and configure Enterprise Security Package clusters in Azure HDInsight
description: Learn how to create and configure Enterprise Security Package clusters in Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 05/09/2019
---
# Create and configure Enterprise Security Package clusters in Azure HDInsight

The Enterprise Security Package for Azure HDInsight gives you access to Active Directory-based authentication, multi-user support, and role-based access control for your Apache Hadoop clusters in Azure. HDInsight ESP clusters enable organizations, which adhere to strict corporate security policies, to process sensitive data securely.

The goal of this guide is correctly configure the necessary resources so that on-premises users can sign in to an ESP enabled HDInsight cluster. This article walks through the steps needed to create an Enterprise Security Package enabled Azure HDInsight Cluster. The steps will cover creating a Windows IaaS VM with Active Directory & Domain Name Services (DNS) enabled. This server will act as a replacement for your **actual** on premises environment and will enable you to proceed through the setup and configuration steps so that you can repeat them later in your own environment. This guide will also help you create a hybrid identity environment using password hash sync with Azure Active Directory.

This guide is meant to complement [Use Enterprise Security Package in HDInsight](apache-domain-joined-architecture.md)

Before using this process in your own environment, setup Active Directory and Domain Name Services (DNS). Also, enable Azure Active Directory and sync on premises user accounts to Azure Active Directory.

![alt-text](./media/apache-domain-joined-create-configure-esp/image002.png)

## Deploy Windows Server Domain Controller and Windows DNS server

Overview: In this section, you will use an Azure Quick Deployment template to Create new VMs and configure Domain Name Services (DNS) and a new AD Forest.

1. Go to [Create an Azure VM with a new AD Forest](https://azure.microsoft.com/resources/templates/active-directory-new-domain/), to view the quick deployment template.

1. Click on **Deploying to Azure**.

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image004.png)

1. Sign in to your Azure subscription where you want to configure Windows Domain Controller and DNS Server.
1. Select the subscription where you want the resources deployed.
1. Select **Create new** next to **Resource group** and enter the name **OnPremADVRG**
1. Enter the following details for the rest of the template fields:

    * Location: Central US
    * Admin Username: HDIFabrikamAdmin
    * Admin Password: <YOUR_PASSWORD>
    * Domain: HDIFabrikam.com
    * Dns Prefix: hdifabrikam

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image006.png)

1. Click **Purchase**
1. Monitor the deployment and wait for it to complete.

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image008.png)

1. Confirm the resources are created under the correct resource group `OnPremADVRG`.

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image010.png)

## Configure additional users and groups to access your HDInsight Cluster

Overview: In this section, you will create the users that will access the HDInsight cluster by the end of this guide.

1. Connect to the domain controller using Remote Desktop. If you used the template mentioned at the beginning, the domain controller is a VM called **adVM** in the `OnPremADVRG` resource group.

    1. Go to the Azure portal > **Resource groups** > **OnPremADVRG** > **adVM** > **Connect** > **RDP** > **Download RDP File**. Save the file to your computer and open it.

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image012.png)

1. When prompted for credentials, use `HDIFabrikam\HDIFabrikamAdmin` as the username and then enter the password that you chose for the admin account.

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image014.png)

1. Once your Remote Desktop session opens on the domain controller VM, launch **Active Directory Users and Computers** from the **Server Manager** dashboard. <TODO: How to launch server manager>

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image016.png)

1. Create two new users, **HDIAdmin**, **HDIUser** as the usernames. These two users will be used to sign in to HDInsight clusters.

    1. In the **Active Directory Users and Computers** screen, right-click in the right pane and then select **User** > **New**.

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image018.png)

    1. In the **New Object - User** screen, enter `HDIUser` as the **User logon name** and click **Next**.

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image020.png)

    1. In the popup that appears, enter the desired password for the new account. Click **OK**.
    1. Click **Finish** to create the new account.

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image022.png)

    1. Repeat the account creation steps 1-4 for the second new account `HDIAdmin`.

        ![Second Admin User](./media/apache-domain-joined-create-configure-esp/image024.png)

1. Create `HDIUserGroup` as a new Group.

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image026.png)

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image028.png)

1. Add the **HDIUser** created in the previous step to the **HDIUserGroup** as a member.

    1. Go to properties of the **HDIUserGroup**.

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image030.png)

    1. Go to **Members** and click **Add**.

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image032.png)

    1. Enter `HDIUser` in the box labeled **Enter the object names to select** and click **OK**.

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image034.png)


Now that we have our Active Directory environment setup, with the Users and User group will be create on the AD environment that will be synchronized with Azure AD.

## Configure your Azure AD tenant

Overview: Now you will configure your Azure AD tenant so that you can synchronize users and groups from on Prem AD to the cloud.

1. Create an AD tenant administrator.
    1. Sign in to the Azure portal and select your Azure AD tenant **HDIFabrikam**
    1. Select **Users** under **Manage** and then **New user**.
    1. Enter the following details for the new user:

        * Name: fabrikamazureadmin
        * User name: fabrikamazureadmin@hdifabrikam.com
        * Password: a secure password of your choice

    1. Click on the **Groups** section, search for **AAD DC Administrators**, and click **Select**.

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image038.png)

    1. Click on the **Directory role** section and select **Global administrator** in the right side.

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image040.png)

    1. Click **Create**.

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image042.png)

1. If you want to change the password for the newly created user <fabrikamazureadmin@hdifabrikam.com>. Sign in to the Azure portal using the identity and then you will prompted to change the password.

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image046.png)

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image048.png)

## Sync on-premises users to Azure AD

### Download and install Microsoft Azure Active Directory connect

1. [Download Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594).

1. Install Microsoft Azure Active Directory connect on the Domain Controller.
    1. Open the executable that you downloaded in the previous step and agree to the license terms.

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image052.png)

    1. Click **Use express settings** and complete the installation.

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image054.png)

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image056.png)

### Configure Sync between On-Premises Domain Controller and Azure AD

1. On the Connect to Azure AD screen, enter the username and password the global administrator for Azure AD. Click **Next**.
    ![alt-text](./media/apache-domain-joined-create-configure-esp/image058.png)
1. On the Connect to Active Directory Domain Services screen, enter the username and password for an enterprise admin account. Click **Next**.
    ![alt-text](./media/apache-domain-joined-create-configure-esp/image060.png)
1. On the **Azure AD sign-in configuration** page, click **Next**.
    ![alt-text](./media/apache-domain-joined-create-configure-esp/image062.png)
1. On the Ready to configure screen, click **Install**.
    ![alt-text](./media/apache-domain-joined-create-configure-esp/image064.png)
1. When the **Configuration complete** screen is displayed, click **Exit**.
    ![alt-text](./media/apache-domain-joined-create-configure-esp/image078.png)

1. After the sync is complete confirm if the users that you created on the IAAS Active Directory are Synced to Azure Active Directory.
    1. Sign in to the Azure portal.
    1. Select **Azure Active Directory** > **HDIFabrikam** > **Users**.

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image080.jpg)

### Create an user-assigned managed identity

Create an user-assigned managed identity that will be used to configure Azure Azure Active Directory Domain Services (Azure AD-DS). For more information on creating a user-assigned managed identity, see [Create, list, delete or assign a role to a user-assigned managed identity using the Azure portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).

![alt-text](./media/apache-domain-joined-create-configure-esp/image082.png)

### Enable Azure Active Directory Domain Services using the Azure portal

For more information, see [Enable Azure Active Directory Domain Services using the Azure portal](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started).

1. Create the Virtual Network to host AADDS. Run the following powershell code.

    ```powershell
    Connect-AzAccount
    Get-AzSubscription
    Set-AzContext -Subscription d3f35892-70bb-44c8-a19d-6aa86f765816
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName HDIFabrikam-CentralUS -Location 'Central US' -Name HDIFabrikam-AADDSVNET -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Sign in to the Azure portal.
1. Click **Create a resource** and enter **Azure AD Domain** in the search box. Click **Azure AD Domain Services** and then click **Create**.
1. Select the Azure Active Directory created for this tutorial, **HDIFabrikam**.

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image084.png)

1. Select the network and the subnet that you created in the previous step.

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image086.png)

1. Click **OK** on the **Adminstrator group** screen. You can optionally modify membership of this group, but it is not required for the steps of this tutorial.

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image088.png)

1. Enable complete synchronization by selecting **All** on the synchronization screen and clicking **OK**.

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image090.png)

1. Verify the details for the Azure DS-DS and click **Ok**.

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image092.png)

1. After you enable Azure AD-DS, a local Domain Name Service (DNS) server runs on the AD Virtual Machines (VMs).

### Configure your Azure AD-DS virtual network

The steps in this section will help you configure your Azure AD-DS virtual network (**HDIFabrikam-AADDSVNET**) to use your custom DNS servers.

1. Locate the IP addresses of your custom DNS servers. Click on the **HDIFabrikam.com** AD-DS resource, click **Properties** under **Manage**  and look at the IP Addresses listed under **IP Address on Virtual Network**.

    ![Locate custom DNS IP addresses for Azure AD-DS](./media/apache-domain-joined-create-configure-esp/image096.png)

1. Configure **HDIFabrikam-AADDSVNET** to custom IPs `10.0.0.4` and `10.0.0.5`.

    1. Select **DNS Servers** under the **Settings** category. then click the radio button next to **Custom**, enter the first IP Address (10.0.0.4) in the text box below, and click **Save**.
    1. Add additional IP Addresses (10.0.0.5) using the same steps.

1. In our scenario Azure AD-DS was configured to use IP Addresses 10.0.0.4 and 10.0.0.5, setting the same IP address on AADDS VNet as show in the image below.

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image098.png)

## Securing LDAP traffic

Lightweight Directory Access Protocol (LDAP) is used to read from and write to Active Directory. You can make LDAP traffic confidential and secure by using Secure Sockets Layer (SSL) / Transport Layer Security (TLS) technology. You can enable LDAP over SSL (LDAPS) by installing a properly formatted certificate.

For more information on secure LDAP, see [Configure secure LDAP (LDAPS) for an Azure AD Domain Services managed domain](https://docs.microsoft.com/en-us/azure/active-directory-domain-services/active-directory-ds-admin-guide-configure-secure-ldap).

In this section, you create a self-signed certificate, download the certificate and configure secure LDAP (LDAPS) for the **hdifabrikam** Azure AD Domain Services managed domain.

The following script creates a certificate for hdifabrikam. The certificate is saved under the path "LocalMachine".

> [!Note] 
> Any utility or application that creates a valid PKCS \#10 request can be used to form the SSL certificate request.

```powershell
$lifetime = Get-Date
New-SelfSignedCertificate -Subject hdifabrikam.com `
-NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
-Type SSLServerAuthentication -DnsName *.hdifabrikam.com, hdifabrikam.com
```

![alt-text](./media/apache-domain-joined-create-configure-esp/image100.jpg)

Verify that the certificate is installed in the computer\'s Personal store. Complete the following steps:

1. Start Microsoft Management Console (MMC).
1. Add the Certificates snap-in that manages certificates on the local computer.
1. Expand **Certificates (Local Computer)**, expand **Personal**, and then expand **Certificates**. A new certificate should exist in the Personal store. This certificate is issued to the fully qualified host name.

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image102.png)

1. In the right pane, right-click the certificate that you created in the previous step, point to **All Tasks**, and then click **Export**.

1. On the **Export Private Key** page, click **Yes, export the private, key]**. The private key is required for the encrypted messages to be read from the computer where the key will be imported.

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image103.png)

1. On the **Export File Format** page, leave the default settings, and then click **Next**. On the **Password** page, type password for the private key.

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image105.png)

1. On the **File to Export** page, type the path and the name for the exported certificate file, and then
click **Next**.

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image107.png)

1. The file name has to be .pfx extension, this file is configured on Azure Portal to establish secure connection.

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image109.png)
    
    ![alt-text](./media/apache-domain-joined-create-configure-esp/image111.png)

1. Enable secure LDAP (LDAPS) for an Azure AD Domain Services managed domain

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image113.png)

1. Now that you enabled Secure LDAP, make sure it is reachable by enabling port 636.

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image115.jpg)

1. `HDIFabrikamManagedIdentity` is the user-assigned managed identity, the HDInsight Domain Services Contributor role is enabled to the managed identity that will enable this Identity to read, create, modify, and delete domain services operations.

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image117.png)

## Creating Enterprise Security Package Enabled HDInsight cluster

This Step requires 3 Pre-requisites.

1. First step is to create Virtual Network that will host ESP enabled HDInsight cluster

    ```powershell
    $virtualNetwork = New-AzVirtualNetwork -ResourceGroupName HDIFabrikam-WestUS -Location 'west US' -Name HDIFabrikam-HDIVNet -AddressPrefix 10.1.0.0/16
    $subnetConfig = Add-AzVirtualNetworkSubnetConfig -Name SparkSubnet -AddressPrefix 10.1.0.0/24 -VirtualNetwork $virtualNetwork
    $virtualNetwork | Set-AzVirtualNetwork
    ```

1. Create a peer relationship between the Virtual Network that is hosting AADDS (`HDIFabrikam-AADDSVNET`) and the Virtual Network that will host the ESP enabled HDInsight cluster (`HDIFabrikam-HDIVNet `). Use the following powershell code to peer these two virtual networks.

    ```powershell
    Add-AzVirtualNetworkPeering -Name HDIVNet-AADDSVNet -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName HDIFabrikam-CentralUS).Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName HDIFabrikam-WestUS)

    Add-AzVirtualNetworkPeering -Name AADDSVNet-HDIVNet -RemoteVirtualNetworkId (Get-AzVirtualNetwork -ResourceGroupName HDIFabrikam-WestUS).Id -VirtualNetwork (Get-AzVirtualNetwork -ResourceGroupName HDIFabrikam-CentralUS)
    ```

1. Create a new Azure Data Lake Storage Gen2 account, **Hdigen2store**, that is configured with the user managed identity **HDIFabrikamManagedIdentity**. For more information on creating Data Lake Storage Gen2 accounts enabled with user managed identities, see [Use Azure Data Lake Storage Gen2 with Azure HDInsight clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md).

    ![alt-text](./media/apache-domain-joined-create-configure-esp/image119.png)

1. Setup custom DNS on the **HDIFabrikam-AADDSVNET** virtual network.
    1. Go to the Azure portal > **Resource groups** > **OnPremADVRG** > **HDIFabrikam-AADDSVNET** > **DNS servers**.
    1. Select **Custom** and enter `10.0.0.4` and `10.0.0.5`.
    1. Click **Save**.

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image123.png)

1. Create a new ESP-enabled HDInsight Spark cluster.
    1. Enter the desired details for section 1 **Basics**.

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image125.jpg)

    1. Under section 2 **Security + Networking**, click **Enabled** under **Enterprise Security Package**.
    1. Click **Cluster admin user** and select the **HDIAdmin** account that you created earlier as the on-premises admin user. Click **Select**.

        ![select cluster admin user](./media/apache-domain-joined-create-configure-esp/image127.jpg)

    1. Click **Cluster access group** and then select **HDIUserGroup**. Any user that you add to this group in the future will be able to access HDInsight clusters.

        ![select cluster access group](./media/apache-domain-joined-create-configure-esp/image129.jpg)

    1. Complete the other steps of the cluster configuration and verify the details on the **Cluster summary**.

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image133.jpg)

    1. Login to the Ambari UI for the newly created cluster using your admin user name and password.

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image135.jpg)

    1. Click **Roles** from the cluster dashboard.
    1. On the **Roles** page, enter the group **hdiusergroup** to assign it to the **Cluster Administrator** role under **Assign roles to these**.

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image137.jpg)

    1. Open your SSH client and login to the cluster using the **hdiuser** that you created previously in the on-premises Active Directory.

        ![alt-text](./media/apache-domain-joined-create-configure-esp/image139.jpg)

If you are able to login with this account, then you have configured your ESP cluster correctly to sync with your on-premises active directory.

## Next steps

* [An introduction to Apache Hadoop security with Enterprise Security Package](apache-domain-joined-introduction.md)