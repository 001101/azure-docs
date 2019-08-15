---
title: Configure a failover group for Azure SQL Database
description: Learn how to configure an auto-failover group for an Azure SQL Database single database, elastic pool, and managed instance using the Azure portal, the Az CLI, and PowerShell. 
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom:
ms.devlang: 
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: sstein, carlrab
ms.date: 08/14/2019
---
# Configure a failover group for Azure SQL Database

This topic teaches you how to configure an [auto-failover group](sql-database-auto-failover-group.md) for an Azure SQL Database single database, elastic pool, and managed instance using the Azure portal, the Az CLI, and PowerShell. 

## Single database
Create the failover group and and add a single database to it using the Azure Portal, PowerShell, or the Az CLI.

### Prerequisites

Consider the following prerequisites:

- The server login and firewall settings for the secondary server must match that of your primary server. 

### Create failover group

# [Portal](#tab/azure-portal)
Create your failover group and add your single database to it using the Azure portal.

1. Select **All Services** on the upper-left hand corner of the [Azure portal](https://portal.azure.com). 
1. Type `sql servers` in the search box.
1. (Optional) Select the star icon next to SQL Servers to favorite **SQL servers** and add it to your left-hand navigation pane. 
    
    ![Locate SQL Servers](media/sql-database-single-database-create-failover-group-tutorial/all-services-sql-servers.png)

1. Select **SQL servers** and choose the SQL Server where your single database is hosted.
1. Select **Failover groups** under the **Settings** pane, and then select **Add group** to create a new failover group. 

    ![Add new failover group](media/sql-database-single-database-create-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. On the **Failover Group** page, enter or select the required values, and then select **Create**.

   - **Databases within the group**: Choose the database you want to add to  your failover group. Adding the database to the failover group will automatically start the geo-replication process. 
        
    ![Add SQL DB to failover group](media/sql-database-single-database-create-failover-group-tutorial/add-sqldb-to-failover-group.png)

# [PowerShell](#tab/azure-powershell)
Create your failover group and add your single database to it using PowerShell. 

   ```powershell-interactive
   $subscriptionId = "<SubscriptionID>"
   $resourceGroupName = "<Resource-Group-Name>"
   $location = "<Region>"
   $adminLogin = "<Admin-Login>"
   $password = "<Complex-Password>"
   $serverName = "<Primary-Server-Name>"
   $databaseName = "<Database-Name>"
   $drLocation = "<DR-Region>"
   $drServerName = "<Secondary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"

   # Create a secondary server in the failover region
   Write-host "Creating a secondary logical server in the failover region..."
   $drServer = New-AzSqlServer -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -Location $drLocation `
      -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential `
         -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   $drServer
   
   # Create a failover group between the servers
   $failovergroup = Write-host "Creating a failover group between the primary and secondary server..."
   New-AzSqlDatabaseFailoverGroup `
      –ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -PartnerServerName $drServerName  `
      –FailoverGroupName $failoverGroupName `
      –FailoverPolicy Automatic `
      -GracePeriodWithDataLossHours 2
   $failovergroup
   
   # Add the database to the failover group
   Write-host "Adding the database to the failover group..." 
   Get-AzSqlDatabase `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -DatabaseName $databaseName | `
   Add-AzSqlDatabaseToFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Successfully added the database to the failover group..." 
   ```

# [Azure CLI](#tab/azure-cli)
Create your failover group and add your single database to it using the Azure CLI.

   ```azurecli-interactive
   #!/bin/bash
   Set variables
   subscriptionID=<SubscriptionID>
   resourceGroupName=<Resource-Group-Name>
   location=<Region>
   adminLogin=<Admin-Login>
   password=<Complex-Password>
   serverName=<Primary-Server-Name>
   databaseName=<Database-Name>
   drLocation=<DR-Region>
   drServerName=<Secondary-Server-Name>
   failoverGroupName=<Failover-Group-Name>

   # Create a secondary server in the failover region
   echo "Creating a secondary logical server in the DR region..."
   az sql server create \
      --name $drServerName \
      --resource-group $resourceGroupName \
      --location $drLocation  \
      --admin-user $adminLogin\
      --admin-password $password
   
   # Create a failover group between the servers and add the database
   echo "Creating a failover group between the two servers..."
   az sql failover-group create \
      --name $failoverGroupName  \
      --partner-server $drServerName \
      --resource-group $resourceGroupName \
      --server $serverName \
      --add-db $databaseName
      --failover-policy Automatic
   ```
---

### Test failover 

Test failover of your failover group using the Azure Portal, PowerShell, or the Az CLI. 

# [Portal](#tab/azure-portal)

Test failover of your failover group using the Azure Portal. 

1. Navigate to your **SQL servers** server within the [Azure portal](https://portal.azure.com). 
1. Select **Failover groups** under the **Settings** pane and then choose the failover group you just created. 
  
   ![Select the failover group from the portal](media/sql-database-single-database-create-failover-group-tutorial/select-failover-group.png)

1. Review which server is primary and which server is secondary. 
1. Select **Failover** from the task pane to fail over your failover group containing your single database. 
1. Select **Yes** on the warning that notifies you that TDS sessions will be disconnected. 

   ![Fail over your failover group containing your SQL database](media/sql-database-single-database-create-failover-group-tutorial/failover-sql-db.png)

1. Review which server is now primary and which server is secondary. If fail over succeeded, the two servers should have swapped roles. 
1. Select **Failover** again to fail the servers back to their originally roles. 

# [PowerShell](#tab/azure-powershell)

Test failover of your failover group using PowerShell.  

Check the role of the secondary replica: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Check role of secondary replica
   Write-host "Confirming the secondary replica is secondary...." 
   (Get-AzSqlDatabaseFailoverGroup `
      -FailoverGroupName $failoverGroupName `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName).ReplicationRole
   ```
Fail over to the secondary server: 

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Failover to secondary server
   Write-host "Failing over failover group to the secondary..." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $drServerName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to" $drServerName 
   ```

Revert failover group back to the primary server:

   ```powershell-interactive
   # Set variables
   $resourceGroupName = "<Resource-Group-Name>"
   $serverName = "<Primary-Server-Name>"
   $failoverGroupName = "<Failover-Group-Name>"
   
   # Revert failover to primary server
   Write-host "Failing over failover group to the primary...." 
   Switch-AzSqlDatabaseFailoverGroup `
      -ResourceGroupName $resourceGroupName `
      -ServerName $serverName `
      -FailoverGroupName $failoverGroupName
   Write-host "Failed failover group to successfully to back to" $serverName
   ```

# [Azure CLI](#tab/azure-cli)

Test failover of your failover group using the Azure CLI. 

Verify which server is the secondary:

   
   ```azurecli-interactive
   # Set variables
   resourceGroupName=<Resource-Group-Name>
   serverName=<Primary-Server-Name>
   
   # Verify which server is secondary
   echo "Verifying which server is in the secondary role..."
   az sql failover-group list \
      --server $serverName \
      --resource-group $resourceGroupName
   ```

Fail over to the secondary server: 

   ```azurecli-interactive
   # Set variables
   resourceGroupName=<Resource-Group-Name>
   drServerName=<Secondary-Server-Name>
   failoverGroupName=<Failover-Group-Name>

   
   echo "Failing over group to the secondary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $drServerName
   echo "Successfully failed failover group over to" $drServerName
   ```

Revert failover group back to the primary server:

   ```azurecli-interactive
   # Set variables
   resourceGroupName=<Resource-Group-Name>
   serverName=<Primary-Server-Name>
   failoverGroupName=<Failover-Group-Name>
   
   echo "Failing over group back to the primary server..."
   az sql failover-group set-primary \
      --name $failoverGroupName \
      --resource-group $resourceGroupName \
      --server $serverName
   echo "Successfully failed failover group back to" $serverName
   ```

---

## Elastic pool
Create the failover group and and add an elastic pool to it using the Azure portal, PowerShell and the Az CLI.  

### Prerequisites

Consider the following prerequisites:

- The server login and firewall settings for the secondary server must match that of your primary server. 

### Create the failover group 

Create the failover group for your elastic pool using the Azure Portal, PowerShell, or the Az CLI. 

# [Portal](#tab/azure-portal)
Create your failover group and add your elastic pool to it using the Azure portal.

1. Select **All Services** on the upper-left hand corner of the [Azure portal](https://portal.azure.com). 
1. Type `sql servers` in the search box. 
1. (Optional) Select the star icon next to SQL Servers to favorite **SQL servers** and add it to your left-hand navigation pane. 
    
    ![Locate SQL Servers](media/sql-database-single-database-create-failover-group-tutorial/all-services-sql-servers.png)

1. Select **SQL servers** and choose the server that hosts the elastic pool you want to add to your failover group. 
1. Select **Failover groups** under the **Settings** pane, and then select **Add group** to create a new failover group. 

    ![Add new failover group](media/sql-database-single-database-create-failover-group-tutorial/sqldb-add-new-failover-group.png)

1. On the **Failover Group** page, enter or select the required values, and then select **Create**. Either create a new secondary server, or select an existing secondary server. 

1. Select **Databases within the group** then select the elastic pool you want to add to the failover group. If an elastic group does not already exist on the secondary server, a warning should appear prompting you to create an elastic pool on the secondary server. Select the warning, and then select **OK** to create the elastic pool on the secondary server. 
        
    ![Add elastic pool to failover group](media/sql-database-elastic-pool-create-failover-group-tutorial/add-elastic-pool-to-failover-group.png)
        
1. Select **Select** to apply your elastic pool settings to the failover group, and then select **Create** to create your failover group. Adding the elastic pool to the failover group will automatically start the geo-replication process. 

# [PowerShell](#tab/azure-powershell)

Create your failover group and add your elastic pool to it using PowerShell. 

!!!!!! Need PowerShell commands to create a failover group for an elastic pool !!!!!!!!

# [Azure CLI](#tab/azure-cli)
Create your failover group and add your elastic pool to it using AZ CLI. 

!!!!!! Need Az CLI commands to create a failover group for an elastic pool !!!!!!!!

---

### Test failover

Test failover of your elastic pool using the Azure Portal, PowerShell, or the Az CLI. 

# [Portal](#tab/azure-portal)

Fail your failover group over to the secondary server, and then fail back using the Azure portal. 

1. Navigate to your **SQL servers** server within the [Azure portal](https://portal.azure.com). 
1. Select **Failover groups** under the **Settings** pane and then choose the failover group you created in section 2. 
  
   ![Select the failover group from the portal](media/sql-database-single-database-create-failover-group-tutorial/select-failover-group.png)

1. Review which server is primary, and which server is secondary. 
1. Select **Failover** from the task pane to fail over your failover group containing your elastic pool. 
1. Select **Yes** on the warning that notifies you that TDS sessions will be disconnected. 

   ![Fail over your failover group containing your SQL database](media/sql-database-single-database-create-failover-group-tutorial/failover-sql-db.png)

1. Review which server is primary, which server is secondary. If failover succeeded, the two servers should have swapped roles. 
1. Select **Failover** again to fail the failover group back to the original settings. 

# [PowerShell](#tab/azure-powershell)

Fail your failover group over to the secondary server, and then fail back using the PowerShell. 

!!!!!! Need PowerShell commands to test failover for an elastic pool !!!!!!!!

# [Azure CLI](#tab/azure-cli)

Fail your failover group over to the secondary server, and then fail back using the Az CLI. 

!!!!!! Need Az CLI commands to test  failover for an elastic pool !!!!!!!!

---

## Managed instance

Create a failover group between two managed instances using the Azure Portal, PowerShell, or the Az CLI. 

You will need to create a gateway for the virtual network of each managed instances, connect the two gateways, and then create the failover group.

### Prerequisites
Consider the following prerequisites:

- The secondary managed instance must be empty.
- The secondary managed instance must be in a [paired region](/azure/best-practices-availability-paired-regions) to the primary instance. 
- The subnet range for the secondary virtual network must not overlap the subnet of the primary virtual network. 
- The collation and timezone of the secondary instance must match that of the primary instance. 
- When connecting the two gateways, the **Shared Key** should be the same for both connections. 

### Create primary virtual network gateway 

Create the primary virtual network gateway with the Azure portal, PowerShell and the Az CLI. 

# [Portal](#tab/azure-portal)

Create the primary virtual network gateway using the Azure portal. 

1. In the [Azure portal](https://portal.azure.com), go to your resource group and select the **Virtual network** resource for your primary managed instance. 
1. Select **Subnets** under **Settings** and then select to add a new **Gateway subnet**. Leave the default values. 

   ![Add gateway for primary managed instance](media/sql-database-managed-instance-failover-group-tutorial/add-subnet-gateway-primary-vnet.png)

1. Once the subnet gateway is created, select **Create a resource** from the left navigation pane and then type `Virtual network gateway` in the search box. Select the **Virtual network gateway** resource published by **Microsoft**. 

   ![Create a new virtual network gateway](media/sql-database-managed-instance-failover-group-tutorial/create-virtual-network-gateway.png)

1. Fill out the required fields to configure the gateway your primary managed instance. 

   The following table shows the values necessary for the gateway for the primary managed instance:
 
    | **Field** | Value |
    | --- | --- |
    | **Subscription** |  The subscription where your primary managed instance is. |
    | **Name** | The name for your virtual network gateway. | 
    | **Region** | The region where your secondary managed instance is. |
    | **Gateway type** | Select **VPN**. |
    | **VPN Type** | Select **Route-based** |
    | **SKU**| Leave default of `VpnGw1`. |
    | **Location**| The location where your secondary managed instance and secondary virtual network is.   |
    | **Virtual network**| Select the virtual network for your secondary managed instance. |
    | **Public IP address**| Select **Create new**. |
    | **Public IP address name**| Enter a name for your IP address. |
    | &nbsp; | &nbsp; |

1. Leave the other values as default, and then select **Review + create** to review the settings for your virtual network gateway.

   ![Primary gateway settings](media/sql-database-managed-instance-failover-group-tutorial/settings-for-primary-gateway.png)

1. Select **Create** to create your new virtual network gateway. 

# [PowerShell](#tab/azure-powershell)

Create the primary virtual network gateway using PowerShell. 

    ```powershell-interactive
    $primaryResourceGroupName = "<Primary-Resource-Group>"
    $primaryVnetName = "<Primary-Virtual-Network-Name>"
    $primaryGWName = "<Primary-Gateway-Name>"
    $primaryGWPublicIPAddress = $primaryGWName + "-ip"
    $primaryGWIPConfig = $primaryGWName + "-ipc"
    $primaryGWAsn = 61000
    
    # Get the primary virtual network
    $vnet1 = Get-AzVirtualNetwork -Name $primaryVnetName -ResourceGroupName $primaryResourceGroupName
    $primaryLocation = $vnet1.Location
    
    # Create primary gateway
    Write-host "Creating primary gateway..."
    $subnet1 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet1
    $gwpip1= New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $primaryResourceGroupName `
             -Location $primaryLocation -AllocationMethod Dynamic
    $gwipconfig1 = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
             -SubnetId $subnet1.Id -PublicIpAddressId $gwpip1.Id
     
    $gw1 = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $primaryResourceGroupName `
        -Location $primaryLocation -IpConfigurations $gwipconfig1 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
    $gw1
    ```

# [Azure CLI](#tab/azure-cli)

Create the primary virtual network gateway using the Az CLI.

!!!!! NEED AZ CLI CODE FOR ADDING MI TO FAILOVER GROUP !!!!!!!!!!!!

---

### Create secondary virtual network gateway

Create the secondary virtual network gateway using the Azure Portal, PowerShell, or the Az CLI. 

# [Portal](#tab/azure-portal)
Repeat the steps in the previous section to create the virtual network subnet and gateway for the secondary managed instance. Fill out the required fields to configure the gateway for your secondary managed instance. 

   The following table shows the values necessary for the gateway for the secondary managed instance:

   | **Field** | Value |
   | --- | --- |
   | **Subscription** |  The subscription where your secondary managed instance is. |
   | **Name** | The name for your virtual network gateway, such as `secondary-mi-gateway`. | 
   | **Region** | The region where your secondary managed instance is. |
   | **Gateway type** | Select **VPN**. |
   | **VPN Type** | Select **Route-based** |
   | **SKU**| Leave default of `VpnGw1`. |
   | **Location**| The location where your secondary managed instance and secondary virtual network is.   |
   | **Virtual network**| Select the virtual network that was created in section 2, such as `vnet-sql-mi-secondary`. |
   | **Public IP address**| Select **Create new**. |
   | **Public IP address name**| Enter a name for your IP address, such as `secondary-gateway-IP`. |
   | &nbsp; | &nbsp; |

   ![Secondary gateway settings](media/sql-database-managed-instance-failover-group-tutorial/settings-for-secondary-gateway.png)

# [PowerShell](#tab/azure-powershell)

Create the secondary virtual network gateway using PowerShell. 

    ```powershell-interactive
    $secondaryResourceGroupName = "<Secondary-Resource-Group>"
    $secondaryVnetName = "<Secondary-Virtual-Network-Name>"
    $secondaryGWName = "<Secondary-Gateway-Name>"
    $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
    $secondaryGWIPConfig = $secondaryGWName + "-ipc"
    $secondaryGWAsn = 62000
    
    # Get the secondary virtual network
    $vnet2 = Get-AzVirtualNetwork -Name $secondaryVnetName -ResourceGroupName $secondaryResourceGroupName
    $secondaryLocation = $vnet2.Location
     
    # Create the secondary gateway
    Write-host "Creating secondary gateway..."
    $subnet2 = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet2
    $gwpip2= New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $secondaryResourceGroupName `
             -Location $secondaryLocation -AllocationMethod Dynamic
    $gwipconfig2 = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
             -SubnetId $subnet2.Id -PublicIpAddressId $gwpip2.Id
     
    $gw2 = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $secondaryResourceGroupName `
        -Location $secondaryLocation -IpConfigurations $gwipconfig2 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
    
    $gw2
    ```

# [Azure CLI](#tab/azure-cli)

Create the secondary virtual network gateway using the Az CLI. 

!!!!! NEED AZ CLI CODE FOR ADDING MI TO FAILOVER GROUP !!!!!!!!!!!!

---


### Connect the gateways 
Create connections between the two gateways using the Azure portal, PowerShell, and Az CLI. 

Two connections need to be created - the connection from the primary gateway to the secondary gateway, and then the connection from the secondary gateway to the primary gateway. 

The shared key used for both connections should be the same for each connection. 

# [Portal](#tab/azure-portal)
Create connections between the two gateways using the Azure portal. 

1. Navigate to your resource group in the [Azure portal](https://portal.azure.com) and select the primary gateway you created in Step 4. 
1. Select **Connections** under **Settings** and then select **Add** to create a new connection. 

   ![Add connection to primary gateway](media/sql-database-managed-instance-failover-group-tutorial/add-primary-gateway-connection.png)

1. Enter a name for your connection, and type in a value for the **Shared Key**. 
1. Select the **Second virtual network gateway** and then select the gateway for the secondary managed instance, such as `secondary-mi-gateway`. 

   ![Create primary to secondary connection](media/sql-database-managed-instance-failover-group-tutorial/create-primary-to-secondary-connection.png)

1. Select **OK** to add your new primary-to-secondary gateway connection.
1. Repeat these steps to create a connection from the gateway of the secondary managed instance to the gateway of the primary managed instance. 

   ![Create secondary to primary connection](media/sql-database-managed-instance-failover-group-tutorial/create-secondary-to-primary-connection.png)

# [PowerShell](#tab/azure-powershell)

Create connections between the two gateways using PowerShell. 

    ```powershell-interactive
    $vpnSharedKey = "mi1mi2psk"
     
    $primaryResourceGroupName = "<Primary-Resource-Group>"
    $primaryGWConnection = "<Primary-connection-name>"
    $primaryLocation = "<Primary-Region>"
     
    $secondaryResourceGroupName = "<Secondary-Resource-Group>"
    $secondaryGWConnection = "<Secondary-connection-name>"
    $secondaryLocation = "<Secondary-Region>"    
   
    # Connect the primary to secondary gateway
    Write-host "Connecting the primary gateway"
    New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $primaryResourceGroupName `
        -VirtualNetworkGateway1 $gw1 -VirtualNetworkGateway2 $gw2 -Location $primaryLocation `
        -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
    $primaryGWConnection
    
    # Connect the secondary to primary gateway
    Write-host "Connecting the secondary gateway"
    
    New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $secondaryResourceGroupName `
        -VirtualNetworkGateway1 $gw2 -VirtualNetworkGateway2 $gw1 -Location $secondaryLocation `
        -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
    $secondaryGWConnection 
    ```

# [Azure CLI](#tab/azure-cli)

Create connections between the two gateways using the Az CLI. 

!!!!! NEED AZ CLI CODE FOR ADDING MI TO FAILOVER GROUP !!!!!!!!!!!!

---

### Create the failover group 
Create the failover group for your managed instances using the Azure portal, PowerShell, or the Az CLI. 

# [Portal](#tab/azure-portal)

Create the failover group for your managed instances using Azure portal. 

1. In the [Azure portal](https://portal.azure.com), go to **All services** and type in `managed instance` in the search box. 
1. (Optional) Select the star next to **SQL managed instances** to add managed instances as shortcut to your left-hand navigation bar. 
1. Select **SQL managed instances** and select the  managed instance you want to be the primary in your failover group. 
1. Under **Settings**, navigate to **Instance Failover Groups** and then choose to **Add group** to open the **Instance Failover Group** page. 

   ![Add a failover group](media/sql-database-managed-instance-failover-group-tutorial/add-failover-group.png)

1. On the **Instance Failover Group** page, type the name of your failover group and then choose the secondary managed instance from the drop-down. Select **Create** to create your failover group. 

   ![Create failover group](media/sql-database-managed-instance-failover-group-tutorial/create-failover-group.png)

1. Once failover group deployment is complete, you will be taken back to the **Failover group** page. 

# [PowerShell](#tab/azure-powershell)

Create the failover group for your managed instances using PowerShell. 

    ```powershell-interactive
    $primaryResourceGroupName = "<Primary-Resource-Group>"
    $failoverGroupName = "<Failover-Group-Name>"
    $primaryLocation = "<Primary-Region>"
    $secondaryLocation = "<Secondary-Region>"
    $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
    $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
    
    # Create failover group
    Write-host "Creating the failover group..."
    $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
         -Location $primaryLocation -ResourceGroupName $primaryResourceGroupName -PrimaryManagedInstanceName $primaryManagedInstance `
         -PartnerRegion $secondaryLocation -PartnerManagedInstanceName $secondaryManagedInstance `
         -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
    $failoverGroup
    ```


# [Azure CLI](#tab/azure-cli)

Create the failover group for your managed instances using the Az CLI. 

!!!!! NEED AZ CLI CODE FOR ADDING MI TO FAILOVER GROUP !!!!!!!!!!!!

---

### Test failover

Test failover of your failover group using the Azure portal, PowerShell, or Az CLI. 

# [Portal](#tab/azure-portal)

Test failover of your failover group using the Azure portal. 

1. Navigate to your managed instance within the [Azure portal](https://portal.azure.com) and select **Instance Failover Groups** under settings. 
1. Review which managed instance is the primary, and which managed instance is the secondary. 
1. Select **Failover** and then select **Yes** on the warning about TDS sessions being disconnected. 

   ![Fail over the failover group](media/sql-database-managed-instance-failover-group-tutorial/failover-mi-failover-group.png)

1. Review which manged instance is the primary and which instance is the secondary. If fail over succeeded, the two instances should have switched roles. 

   ![Managed instances have switched roles after failover](media/sql-database-managed-instance-failover-group-tutorial/mi-switched-after-failover.png)

1. Select **Failover** once again to fail the primary instance back to the primary role. 

# [PowerShell](#tab/azure-powershell)

Test failover of your failover group using PowerShell. 

    ```powershell-interactive
    $primaryResourceGroupName = <Primary-Resource-Group>
    $secondaryResourceGroupName = <Secondary-Resource-Group>
    $failoverGroupName = "<Failover-Group-Name>"
    $primaryLocation = "<Primary-Region>"
    $secondaryLocation = "<Secondary-Region>"
    $primaryManagedInstance = "<Primary-Managed-Instance-Name>"
    $secondaryManagedInstance = "<Secondary-Managed-Instance-Name>"
    
    # Verify the current primary role
    Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
        -Location $secondaryLocation -Name $failoverGroupName
    
    # Failover the primary managed instance to the secondary role
    Write-host "Failing primary over to the secondary location"
    Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $secondaryResourceGroupName `
        -Location $secondaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
    
    # Verify the current primary role
    Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
        -Location $secondaryLocation -Name $failoverGroupName
    
    # Fail primary managed instance back to primary role
    Write-host "Failing primary back to primary role"
    Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
        -Location $primaryLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
    
    # Verify the current primary role
    Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $primaryResourceGroupName `
        -Location $secondaryLocation -Name $failoverGroupName
    ```

# [Azure CLI](#tab/azure-cli)

Test failover of your failover group using the Az CLI. 

!!!!! NEED AZ CLI CODE FOR TESTING FAILOVER FOR AN MI !!!!!!!!!!!!

---

## Update connection string

Once your failover group is configured, update the connection string so that your application connects to the failover group listener rather than the primary database, elastic pool, or managed instance. That way, you don"t have to manually update the connection string every time your Azure SQL database entity fails over. 

The connection string to the failover group is in the form of `fog-name.`

## Next steps

For detailed steps configuring a failover group, see the following tutorials:
- [Add single database to a failover group](sql-database-single-database-failover-group-tutorial.md)
- [Add elastiac pool to a failover group](sql-database-elastic-pool-failover-group-tutorial.md)
- [Add managed instance to a failover group](sql-database-managed-instance-failover-group-tutorial.md)
 
For an overview of Azure SQL Database high availability options, see [geo-replication](sql-database-active-geo-replication.md) and [auto-failover groups][auto-failover group](sql-database-auto-failover-group.md). 
