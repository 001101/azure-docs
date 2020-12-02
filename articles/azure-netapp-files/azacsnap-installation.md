---
title: Install the Azure Application Consistent Snapshot Tool for Azure NetApp Files | Microsoft Docs
description: This article provides a guide for installation of the Azure Application Consistent Snapshot Tool that you can use with Azure NetApp Files. 
services: azure-netapp-files
documentationcenter: ''
author: phjensen
manager: ''
editor: ''

ms.assetid:
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
---

# Install the Azure Application Consistent Snapshot Tool

This article provides a guide for installation of the Azure Application Consistent Snapshot Tool that you can use with Azure NetApp Files. 

## Introduction

The downloadable self-installer is designed to make the snapshot tools easy to setup and run with non-root user privileges (e.g. azacsnap). The installer will setup the user and put the snapshot tools into the users `$HOME/bin` subdirectory (default = `/home/azacsnap/bin`).
The self-installer tries to determine the correct settings and paths for all the files based on the configuration of the user performing the installation (e.g. root). If the pre-requisite steps (Enable communication with storage and SAP HANA) were run as root, then the installation will copy the private key and the hdbuserstore to the backup user’s location. However, it is possible for the steps which enable communication with the storage back-end and SAP HANA to be manually done by a knowledgeable administrator after the installation.

## Prerequisites for installation

Please follow the guidelines to setup and execute the snapshots and disaster recovery commands. It
is recommended the following steps are completed as root before installing and using the snapshot
tools.

1. **OS is patched** : Please refer for patching and SMT setup <https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/hana-installation#setting-up-smt-server-for-suse-linux>
1. **Time Synchronization is setup**. The customer will need to provide a NTP compatible time
    server, and configure the OS accordingly.
1. **HANA is installed** : Please refer for HANA installation instructions:
    <https://blogs.msdn.microsoft.com/saponsqlserver/2017/11/21/sap-netweaver-installation-on-hana-database/>.
1. **[Enable communication with storage](#enable-communication-with-storage)** (refer separate section for more details) : Customer must
    setup SSH with a private/public key pair, and provide the public key for each node where the
    snapshot tools are planned to be executed to Microsoft Operations for setup on the storage
    back-end.
   1. **For Azure NetApp Files (refer separate section for details)**: Customer must generate the
      service principal authentication file.
   1. **For Azure Large Instance (refer separate section for details)**: Customer must setup SSH with a
      private/public key pair, and provide the public key for each node where the snapshot tools are
      planned to be executed to Microsoft Operations for setup on the storage back-end.

      Test this by using SSH to connect to one of the nodes (e.g. `ssh -l <Storage UserName> <Storage IP Address>`).
      Type `exit` to logout of the storage prompt.

      Microsoft  operations will  provide  the  storage  user  and  storage  IP at  the  time  of provisioning.
  
1. **[Enable communication with SAP HANA](#enable-communication-with-sap-hana)** (refer separate section for more details) : Customer must
    setup an appropriate SAP HANA user with the required privileges to perform the snapshot.
   1. This can be tested from the command line as follows using the text in `grey`
      1. HANAv1

            `hdbsql -n <HANA IP address> -i <HANA instance> -U <HANA user> "\s"`

      1. HANAv2

            `hdbsql -n <HANA IP address> -i <HANA instance> -d SYSTEMDB -U <HANA user> "\s"`

      - The examples above are for non-SSL communication to SAP HANA.

## Enable communication with storage
This section explains how to enable communication with storage.

### Azure NetApp Files

Create RBAC Service Principal

1. Within an Azure Cloud Shell session, make sure you're logged on at the subscription where you want
  to be associated with the service principal by default:

  ```bash
  Cloud Shell User@Azure> az account show
  ```

1. If this is not the correct subscription, use

    ```bash
    Cloud Shelluser@Azure> az account set -s <subscription name or id>
    ```

1. Create a service principal using Azure CLI per the following example

    ```bash
    Cloud Shell User@Azure> az ad sp create-for-rbac --sdk-auth
    ```

    1. This should generate an output like the following:

        ```bash
        {
          "clientId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "clientSecret": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "subscriptionId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "tenantId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
          "resourceManagerEndpointUrl": "https://management.azure.com/",
          "activeDirectoryGraphResourceId": "https://graph.windows.net/",
          "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
          "galleryEndpointUrl": "https://gallery.azure.com/",
          "managementEndpointUrl": "https://management.core.windows.net/"
        }
        ```

    > This command will automatically assign RBAC contributor role to the service principal at
    subscription level, you can narrow down the scope to the specific resource group where your
    tests will create the resources.

1. Cut and Paste the output content into a file called `azureauth.json` stored on the same system as the `azacsnap`
   command and secure the file with appropriate system permissions.

### Azure Large Instance

Communication with the storage back-end executes over an encrypted SSH channel. The following
example steps are to provide guidance on setup of SSH for this communication.

1. Modify the `/etc/ssh/ssh_config` file

    Refer to the following output where the `MACs hmac-sha1` line has been added:

    ```bash
    # RhostsRSAAuthentication no
    # RSAAuthentication yes
    # PasswordAuthentication yes
    # HostbasedAuthentication no
    # GSSAPIAuthentication no
    # GSSAPIDelegateCredentials no
    # GSSAPIKeyExchange no
    # GSSAPITrustDNS no
    # BatchMode no
    # CheckHostIP yes
    # AddressFamily any
    # ConnectTimeout 0
    # StrictHostKeyChecking ask
    # IdentityFile ~/.ssh/identity
    # IdentityFile ~/.ssh/id_rsa
    # IdentityFile ~/.ssh/id_dsa
    # Port 22
    Protocol 2
    # Cipher 3des
    # Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-
    cbc
    # MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd
    MACs hmac-sha
    # EscapeChar ~
    # Tunnel no
    # TunnelDevice any:any
    # PermitLocalCommand no
    # VisualHostKey no
    # ProxyCommand ssh -q -W %h:%p gateway.example.com
    ```

1. Create a private/public key pair

    Using the following example command to generate the key pair, do not enter a password when generating a key.

    ```bash
    > ssh-keygen -t rsa –b 5120 -C ""
    ```

1. Send the public key to Microsoft Operations

    Send the output of the `cat /root/.ssh/id_rsa.pub` command (example below) to Microsoft Operations
    to enable the snapshot tools to communicate with the storage sub-system.

    ```bash
    > cat /root/.ssh/id_rsa.pub
    ssh-rsa
    AAAAB3NzaC1yc2EAAAADAQABAAABAQDoaRCgwn1Ll31NyDZy0UsOCKcc9nu2qdAPHdCzleiTWISvPW
    FzIFxz8iOaxpeTshH7GRonGs9HNtRkkz6mpK7pCGNJdxS4wJC9MZdXNt+JhuT23NajrTEnt1jXiVFH
    bh3jD7LjJGMb4GNvqeiBExyBDA2pXdlednOaE4dtiZ1N03Bc/J4TNuNhhQbdsIWZsqKt9OPUuTfD
    j0XvwUTLQbR4peGNfN1/cefcLxDlAgI+TmKdfgnLXIsSfbacXoTbqyBRwCi7p+bJnJD07zSc9YCZJa
    wKGAIilSg7s6Bq/2lAPDN1TqwIF8wQhAg2C7yeZHyE/ckaw/eQYuJtN+RNBD
    ```

## Enable communication with SAP HANA

The snapshot tools communicate with SAP HANA and need a user with appropriate permissions to
initiate and release the database save-point. The following provides an example on setup of the SAP
HANA v2 user and the hdbuserstore for communication to the SAP HANA database.

The following example commands setup a user (AZACSNAP) in the SYSTEMDB on SAP HANA 2.
database, change the IP address, usernames and passwords as appropriate:

1. Connect to the SYSTEMDB to create the user

    ```bash
    > hdbsql -n <IP_address_of_host>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>

    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql SYSTEMDB=>
    ```

1. Create the user

    This example creates the AZACSNAP user in the SYSTEMDB.

    ```bash
    hdbsql SYSTEMDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. Grant the user permissions

    This example sets the permission for the AZACSNAP user to allow for performing a database
    consistent storage snapshot.

    ```bash
    hdbsql SYSTEMDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *OPTIONAL* - Prevent user's password from expiring

  > [!CAUTION]
  > Recommend checking with corporate policy before making this change.

   This example disables the password expiration for the AZACSNAP user, without this change the user's password will expire preventing snapshots to be taken correctly.  

   ```bash
   hdbsql SYSTEMDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

1. Setup the SAP HANA Secure User Store (change the password)
    This example uses the hdbuserstore command from the Linux shell to setup the SAP HANA Secure User store.

    ```bash
    > hdbuserstore Set AZACSNAP <IP_address_of_host>:30013 AZACSNAP <AZACSNAP_PASSWORD_CHANGE_ME>
    ```

1. Check the SAP HANA Secure User Store
    To check if the secure user store is setup correctly, use the hdbuserstore command to list the
    output which should be similar to the following. More details on using hdbuserstore are available
    on the SAP website.

    ```bash
    > hdbuserstore List
    DATA FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.DAT
    KEY FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.KEY


    KEY AZACSNAP
    ENV : <IP_address_of_host>:
    USER: AZACSNAP
    ```

### Additional instructions for using the log trimmer (SAP HANA 2.0 and later)

If using the log trimmer, then the following example commands setup a user (AZACSNAP) in the
TENANT database(s) on a SAP HANA 2.0 database system. Remember to change the IP address,
usernames and passwords as appropriate:

1. Connect to the TENANT database to create the user, tenant specific details are `<IP_address_of_host>` and `<SYSTEM_USER_PASSWORD>`.  Also, note the port (`30015`) required to communicate with the TENANT database.

    ```bash
    > hdbsql -n <IP_address_of_host>:30015 - i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
  
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql TENANTDB=>
    ```

1. Create the user

    This example creates the AZACSNAP user in the SYSTEMDB.

    ```bash
    hdbsql TENANTDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME>
    NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. Grant the user permissions

    This example sets the permission for the AZACSNAP user to allow for performing a database
    consistent storage snapshot.

    ```bash
    hdbsql TENANTDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. *OPTIONAL* - Prevent user's password from expiring

  > [!CAUTION]
  > Recommend checking with corporate policy before making this change.

   This example disables the password expiration for the AZACSNAP user, without this change the user's password will expire preventing snapshots to be taken correctly.  

   ```bash
   hdbsql TENANTDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

> **Note**  repeat these steps for all the tenant databases. It's possible to get the connection details for all the
tenants using the following SQL query against the SYSTEMDB.

```bash
SELECT HOST, SQL_PORT, DATABASE_NAME FROM SYS_DATABASES.M_SERVICES WHERE SQL_PORT LIKE '3%'
```

See the following example query and output.

```bash
> hdbsql -jaxC -n 10.90.0.31:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> " SELECT HOST,
SQL_PORT, DATABASE_NAME FROM SYS_DATABASES.M_SERVICES WHERE SQL_PORT LIKE '3%' "
sapprdhdb80,30013,SYSTEMDB
sapprdhdb80,30015,H81
sapprdhdb80,30041,H82
```

### Using SSL for communication with SAP HANA

The `azacsnap` tool utilizes SAP HANA's `hdbsql` command to communicate with SAP HANA. This
includes the use of SSL options when encrypting communication with SAP HANA. `azacsnap` uses
`hdbsql` command's SSL options as follows.

The following are always used when using the `azacsnap --ssl` option:

- `-e` - Enables TLS encryptionTLS/SSL encryption. The server chooses the highest available.
- `-ssltrustcert` - Specifies whether to validate the server's certificate.
- `-sslhostnameincert "*"` - Specifies the host name used to verify server’s identity. By
    specifying `"*"` as the host name, then the server's host name is not validated.

SSL communication also requires Key Store and Trust Store files.  While it is possible for
these files to be stored in default locations on a Linux installation, to ensure the
correct key material is being used for the various SAP HANA systems (i.e. in the cases where
different key-store and trust-store files are used for each SAP HANA system) `azacsnap`
expects the key-store and trust-store files to be stored in the `securityPath` location
as specified in the `azacsnap` configuration file.

#### Key Store files

- If using multiple SIDs with the same key material it's easier to create links into
    the securityPath location as defined in the `azacsnap` config file.  Ensure these exist
    for every SID using SSL.
  - For openssl:
    - `ln $HOME/.ssl/key.pem <securityPath>/<SID>_keystore`
  - For commoncrypto:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_keystore`
- If using multiple SIDs with the different key material per SID, copy (or move and rename)
    the files into the securityPath location as defined in the SIDs `azacsnap` config
    file.
  - For openssl:
    - `mv key.pem <securityPath>/<SID>_keystore`
  - For commoncrypto:
    - `mv sapcli.pse <securityPath>/<SID>_keystore`

When `azacsnap` calls `hdbsql` it will add `-sslkeystore=<securityPath>/<SID>_keystore`
to the command line.

#### Trust Store files

- If using multiple SIDs with the same key material create hard-links into the securityPath
    location as defined in the `azacsnap` config file.  Ensure these exist for every SID
    using SSL.
  - For openssl:
    - `ln $HOME/.ssl/trust.pem <securityPath>/<SID>_truststore`
  - For commoncrypto:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_truststore`
- If using multiple SIDs with the different key material per SID, copy (or move and rename)
   the files into the securityPath location as defined in the SIDs `azacsnap` config file.
  - For openssl:
    - `mv trust.pem <securityPath>/<SID>_truststore`
  - For commoncrypto:
    - `mv sapcli.pse <securityPath>/<SID>_truststore`

> [!NOTE]
> The `<SID>` component of the file names must be the SAP HANA System Identifier
all in upper case (e.g. `H80`, `PR1`, etc.)

When `azacsnap` calls `hdbsql` it will add `-ssltruststore=<securityPath>/<SID>_truststore`
to the command line.

Therefore, running `azacsnap -c test --test hana --ssl openssl` where the `SID` is `H80`
in the config file, it would execute the `hdbsql`connections as follows:

```bash
hdbsql \
    -e \
    -ssltrustcert \
    -sslhostnameincert "*" \
    -sslprovider openssl \
    -sslkeystore ./security/H80_keystore \
    -ssltruststore ./security/H80_truststore
    "sql statement"
```

> [!NOTE]
> The `\` character is a command line line-wrap to improve clarity of the
multiple parameters passed on the command line.

## Installing the snapshot tools

The downloadable self-installer is designed to make the snapshot tools easy to setup and run with
non-root user privileges (e.g. azacsnap). The installer will setup the user and put the snapshot tools
into the users `$HOME/bin` subdirectory (default = `/home/azacsnap/bin`).

The self-installer tries to determine the correct settings and paths for all the files based on the
configuration of the user performing the installation (e.g. root). If the previous setup steps (Enable
communication with storage and SAP HANA) were run as root, then the installation will copy the
private key and the hdbuserstore to the backup user's location. However, it is possible for the steps
which enable communication with the storage back-end and SAP HANA to be manually done by a
knowledgeable administrator after the installation.

> [!NOTE]
> For earlier SAP HANA on Azure Large Instance installations, the directory of pre-installed
snapshot tools was `/hana/shared/<SID>/exe/linuxx86_64/hdb`.

With the [pre-requisite steps](#pre-requisite-steps-for-installation) completed, it’s now possible to install the snapshot tools using the self-installer as follows:

1. Copy the downloaded self-installer to the target system.
1. Execute the self-installer as the `root` user, see the following example. If necessary, make the
    file executable using the `chmod +x *.run` command.

Running the self-installer command without any arguments will display help on using the installer to
install the snapshot tools as follows:

```bash
# chmod +x azacsnap_installer_v5.0.run
# ./azacsnap_installer_v5.0.run
Usage: ./azacsnap_installer_v5.0.run [-v] -I [-u <HLI Snapshot Command user>]
./azacsnap_installer_v5.0.run [-v] -X [-d <directory>]
./azacsnap_installer_v5.0.run [-h]

Switches enclosed in [] are optional for each command line.
- h prints out this usage.
- v turns on verbose output.
- I starts the installation.
- u is the Linux user to install the scripts into, by default this is
'azacsnap'.
- X will only extract the commands.
- d is the target directory to extract into, by default this is
'./snapshot_cmds'.
Examples of a target directory are ./tmp or /usr/local/bin
```

> [!NOTE]
> The self-installer has an option to extract (-X) the snapshot tools from the bundle without
performing any user creation and setup. This allows an experienced administrator to
complete the setup steps manually, or to copy the commands to upgrade an existing
installation.

### Easy installation of snapshot tools (default)

The installer has been designed to quickly install the snapshot tools for SAP HANA on Azure. By default, if the
installer is run with only the -I option, it will do the following:

1. Create Snapshot user 'azacsnap', home directory, and set group membership.
1. Configure the azacsnap user's login `~/.profile`.
1. Search filesystem for directories to add to azacsnap's `$PATH`, these are typically the paths to
    the SAP HANA tools, such as `hdbsql` and `hdbuserstore`.
1. Search filesystem for directories to add to azacsnap's `$LD_LIBRARY_PATH`. Many commands
    require a library path to be set in order to execute correctly, this configures it for the
    installed user.
1. Copy the SSH keys for back-end storage for azacsnap from the "root" user (the user running
    the install). This assumes the "root" user has already configured connectivity to the storage
    - see section "[Enable communication with storage](#enable-communication-with-storage)".
1. Copy the SAP HANA connection secure user store for the target user, azacsnap. This
    assumes the "root" user has already configured the secure user store – see section "Enable
    communication with SAP HANA".
1. The snapshot tools are extracted into `/home/azacsnap/bin/`.
1. The commands in `/home/azacsnap/bin/` have their permissions set (ownership and
    executable bit, etc).

The following example shows the correct output of the installer when run with the default
installation option.

```bash
> ./azacsnap_installer_v5.0.run -I
+-----------------------------------------------------------+
| Azure Application Consistent Snapshot Tool Installer      |
+-----------------------------------------------------------+
|-> Installer version '5.0'
|-> Create Snapshot user 'azacsnap', home directory, and set group membership.
|-> Configure azacsnap .profile
|-> Search filesystem for directories to add to azacsnap's $PATH
|-> Search filesystem for directories to add to azacsnap's $LD_LIBRARY_PATH
|-> Copying SSH keys for back-end storage for azacsnap.
|-> Copying HANA connection keystore for azacsnap.
|-> Extracting commands into /home/azacsnap/bin/.
|-> Making commands in /home/azacsnap/bin/ executable.
|-> Creating symlink for hdbsql command in /home/azacsnap/bin/.
+-----------------------------------------------------------+
| Install complete! Follow the steps below to configure.    |
+-----------------------------------------------------------+
+-----------------------------------------------------------+
|  Install complete!  Follow the steps below to configure.  |
+-----------------------------------------------------------+

1. Change into the snapshot user account.....
     su - azacsnap
2. Setup the HANA Secure User Store..... (command format below)
     hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>
3. Change to location of commands.....
     cd /home/azacsnap/bin/
4. Configure the customer details file.....
     azacsnap -c configure --configuration new
5. Test the connection to storage.....
     azacsnap -c test --test storage
6. Test the connection to HANA.....
   a. without SSL
     azacsnap -c test --test hana
   b. with SSL,  you will need to choose the correct SSL option
     azacsnap -c test --test hana --ssl=<commoncrypto|openssl>
7. Run your first snapshot backup..... (example below)
     azacsnap -c backup --volume=data --prefix=hana_test --frequency=15min --retention=1
```

### Uninstallation of the snapshot tools

If the snapshot tools have been installed using the default settings, uninstallation only requires
removing the user the commands have been installed for (default = azacsnap).

```bash
> userdel -f -r azacsnap
```

### Manual installation of the snapshot tools

In some cases, it is necessary to install the tools manually, but the recommendation is to use the
installer's default option to ease this process.

Each line starting with a `#` character demonstrates the example commands following the character
are run by the root user. The `\` at the end of a line is the standard line-continuation character for a
shell command.

As the root superuser, a manual installation can be achieved as follows:

1. Get the "sapsys" group id, in this case the group id = 1010

    ```bash
    # grep sapsys /etc/group
    sapsys:x:1010:
    ```

1. Create Snapshot user 'azacsnap', home directory, and set group membership using
    the group id from step 1.

    ```bash
    # useradd -m -g 1010 -c "Azure SAP HANA Snapshots User" azacsnap
    ```

1. Make sure the user azacsnap's login `.profile` exists.

    ```bash
    # echo "" >> /home/azacsnap/.profile
    ```

1. Search filesystem for directories to add to azacsnap's $PATH, these are typically the paths to
    the SAP HANA tools, such as hdbsql and hdbuserstore.

    ```bash
    # HDBSQL_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name hdbsql -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Add the updated PATH to the user's profile

    ```bash
    # echo "export PATH=\"\$PATH:$HDBSQL_PATH\"" >> /home/azacsnap/.profile
    ```

1. Search filesystem for directories to add to azacsnap's $LD_LIBRARY_PATH.

    ```bash
    # NEW_LIB_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name "*.so" -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. Add the updated library path to the user's profile

    ```bash
    > echo "export LD_LIBRARY_PATH=\"\$LD_LIBRARY_PATH:$NEW_LIB_PATH\"" >> /home/azacsnap/.profile
    ```

1. On Azure Large Instances
    1. Copy the SSH keys for back-end storage for azacsnap from the "root" user (the user running
    the install). This assumes the "root" user has already configured connectivity to the storage
       > see section "[Enable communication with storage](#enable-communication-with-storage)".

        ```bash
        > cp -pr ~/.ssh /home/azacsnap/.
        ```

    1. Set the user permissions correctly for the SSH files

        ```bash
        > chown -R azacsnap.sapsys /home/azacsnap/.ssh
        ```

1. On Azure NetApp Files
    1. Configure the user’s `DOTNET_BUNDLE_EXTRACT_BASE_DIR` path per the .NET Core single-file extract
       guidance.
        1. SUSE Linux

            ```bash
            # echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.profile
            # echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.profile
            ```

        1. RHEL

            ```bash
            # echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.bash_profile
            # echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.bash_profile
            ```

1. Copy the SAP HANA connection secure user store for the target user, azacsnap. This
    assumes the "root" user has already configured the secure user store.
    > see section "[Enable communication with SAP HANA](#enable-communication-with-sap-hana)".

    ```bash
    # cp -pr ~/.hdb /home/azacsnap/.
    ```

1. Set the user permissions correctly for the hdbuserstore files

    ```bash
    # chown -R azacsnap.sapsys /home/azacsnap/.hdb
    ```

1. Extract the snapshot tools into /home/azacsnap/bin/.

    ```bash
    # ./azacsnap_installer_v5.0.run -X -d /home/azacsnap/bin
    ```

1. Make the commands executable

    ```bash
    # chmod 700 /home/azacsnap/bin/*
    ```

1. Ensure the correct ownership permissions are set on the user's home directory

    ```bash
    # chown -R azacsnap.sapsys /home/azacsnap/*
    ```

### Completing setup of snapshot tools

The installer provides steps to complete after the installation of the snapshot tools has been done.
Follow these steps to configure and test the snapshot tools.  After successful testing, then perform the first database
consistent storage snapshot.

The following output shows the steps to complete after running the installer with the default installation options:

1. Change into the snapshot user account
    1. `su - azacsnap`
1. Setup the HANA Secure User Store
   1. `hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>`
1. Change to location of commands
   1. `cd /home/azacsnap/bin/`
1. Configure the customer details file
   1. `azacsnap -c configure –-configuration new`
1. Test the connection to storage.....
   1. `azacsnap -c test –-test storage`
1. Test the connection to HANA.....
    1. without SSL
       1. `azacsnap -c test –-test hana`
    1. with SSL, you will need to choose the correct SSL option
       1. `azacsnap -c test –-test hana --ssl=<commoncrypto|openssl>`
1. Run your first snapshot backup
    1. `azacsnap -c backup –-volume data--prefix=hana_test --retention=1`

Step 2 will be necessary if "[Enable communication with SAP HANA](#enable-communication-with-sap-hana)" was not done before the
installation.

> [!CAUTION]
> The test commands should execute correctly otherwise the commands may fail.

## Next steps

- [Introduction to Azure Application Consistent Snapshot Tool](azacsnap-introduction.md)
- [Get started with Azure Application Consistent Snapshot Tool](azacsnap-get-started.md)
- [Install Azure Application Consistent Snapshot Tool](azacsnap-installation.md)
- [Configure Azure Application Consistent Snapshot Tool](azacsnap-configuration.md)
- [Test Azure Application Consistent Snapshot Tool](azacsnap-test.md)
- [Back up with Azure Application Consistent Snapshot Tool](azacsnap-backup.md)
