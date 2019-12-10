---
title: Connection strings for SQL Analytics using Azure Synapse Analytics | Microsoft Docs
description: Connection strings for SQL Analytics
services: sql-data-warehouse 
author: azaricstefan 
ms.service: sql-data-warehouse 
ms.topic: overview 
ms.subservice: design 
ms.date: 10/21/2019 
ms.author: v-stazar 
ms.reviewer: jrasnick
---

# Connection strings for SQL Analytics using Azure Synapse Analytics
You can connect to SQL Analytics via several different application protocols such as [ADO.NET]( https://msdn.microsoft.com/library/e80y5yhx(v=vs.110).aspx), [ODBC](https://msdn.microsoft.com/library/jj730314.aspx), [PHP](https://msdn.microsoft.com/library/cc296172.aspx?f=255&MSPPError=-2147217396) and [JDBC](https://msdn.microsoft.com/library/mt484311(v=sql.110).aspx). Below you'll find connections strings examples for each protocol. 

You can also use the Azure portal to build your connection string. To build your connection string using the Azure portal:

1. Navigate to your database blade
2. Under *Essentials* select *Show database connection strings*

## Sample ADO.NET connection string
```csharp
Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};User ID={your_user_name};Password={your_password_here};Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
```

## Sample ODBC connection string
```csharp
Driver={SQL Server Native Client 11.0};Server=tcp:{your_server}.database.windows.net,1433;Database={your_database};Uid={your_user_name};Pwd={your_password_here};Encrypt=yes;TrustServerCertificate=no;Connection Timeout=30;
```

## Sample PHP connection string
```PHP
Server: {your_server}.database.windows.net,1433 \r\nSQL Database: {your_database}\r\nUser Name: {your_user_name}\r\n\r\nPHP Data Objects(PDO) Sample Code:\r\n\r\ntry {\r\n   $conn = new PDO ( \"sqlsrv:server = tcp:{your_server}.database.windows.net,1433; Database = {your_database}\", \"{your_user_name}\", \"{your_password_here}\");\r\n    $conn->setAttribute( PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION );\r\n}\r\ncatch ( PDOException $e ) {\r\n   print( \"Error connecting to SQL Server.\" );\r\n   die(print_r($e));\r\n}\r\n\rSQL Server Extension Sample Code:\r\n\r\n$connectionInfo = array(\"UID\" => \"{your_user_name}\", \"pwd\" => \"{your_password_here}\", \"Database\" => \"{your_database}\", \"LoginTimeout\" => 30, \"Encrypt\" => 1, \"TrustServerCertificate\" => 0);\r\n$serverName = \"tcp:{your_server}.database.windows.net,1433\";\r\n$conn = sqlsrv_connect($serverName, $connectionInfo);
```

## Sample JDBC connection string
```Java
jdbc:sqlserver://yourserver.database.windows.net:1433;database=yourdatabase;user={your_user_name};password={your_password_here};encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
```

> [!NOTE]
> Consider setting the connection timeout to 300 seconds to allow the connection to survive short periods of unavailability.
> 
> 

## Recommendations

For executing [SQL Analytics on-demand](on-demand.md) queries, the recommended tools are [Azure Data Studio](get-started-azure-data-studio.md) and Azure Synapse Studio.

## Next steps
To start querying your analytics with Visual Studio and other applications, see [Query with Visual Studio](../../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md).

