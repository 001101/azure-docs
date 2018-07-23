---
title: Migration from Azure Germany compute resources to public Azure
description: Provides help for migrating database resources
author: gitralf
ms.author: ralfwi 
ms.date: 7/20/2018
ms.topic: article
ms.custom: bfmigrate
---

# Databases

## Azure SQL Database

To migrate Azure SQL databases you can use (for smaller workloads) the export function to create a BACPAC file. BaACPAC file is a compressed (zip'ed) file containing metadata and data from the SQL Server database. Once created, you can copy it to the target environment (for example with AzCopy) and use the import function to re-build the database. Be aware of the following considerations (see more in the links provided below):

- For an export to be transactionally consistent, you must ensure either that no write activity is occurring during the export, or that you are exporting from a transactionally consistent copy of your Azure SQL database.
- If you are exporting to blob storage, the maximum size of a BACPAC file is 200 GB. To archive a larger BACPAC file, export to local storage.
- If the export operation from Azure SQL Database exceeds 20 hours, it may be canceled. Please look for hints how to increase performance (links below).

By migrating a database please keep in mind that the connection string will change since the DNS name of the server will change.

### Links

- [Export DB to Bacpac file](../sql-database/sql-database-export.md)
- [Import Bacpac file to a DB](../sql-database/sql-database-import.md)

## SQL Data Warehouse

There are no special migration options for SQL Data Warehouse. Please follow the instructions under [Azure SQL Database](#azure-sql-database).

## Azure Cosmos DB

## Redis Cache

There are multiple options to migrate to public Azure, depending on the requirements you have.

### Option 1: Data Loss ok, create new instance

This approach makes most sense in the case where you are using Redis as a transient data cache and your application will re-populate the cache data automatically in the new region.

- Create a new Redis instance in the new target region
- Update your application to use the new instance in new region.
- Delete old Redis instance in source region

### Option 2: Copy data from Source instance to Target instance

A member of the Azure Redis team wrote an open source tool that can be used to copy data from one Redis instance to another without requiring Import/Export functionality.

- Create a VM in the source region. If your data set in Redis is large, make sure to select a relatively powerful VM size to minimize copying time.
- Create new Redis instance in target region.
- Flush data from the **target** instance (make sure **NOT** to flush from the **source** instance. This is required because the copy tool below **does not** overwrite existing keys in the target location.
- Use a tool like the following to automatically copy data from source Redis instance to target Redis instance: [Source](https://github.com/deepakverma/redis-copy), [Download](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip). Note that this process can take a long time, depending on the size of your data set.

### Option 3: Export from source instance, import into destination instance

This approach takes advantage of features only available in the Premium tier.

- Create a new Premium tier Redis instance in the new target region of the same size as source Redis instance.
- [Export data from source cache](../azure/redis-cache/cache-how-to-import-export-data.md) or with the [Export-AzureRmRedisCache PowerShell cmdlet](https://docs.microsoft.com/en-us/powershell/module/azurerm.rediscache/export-azurermrediscache?view=azurermps-6.4.0). **Note:** you will have to export to a storage account in the same region as the cache instance.
- Copy exported blobs from storage account in source region to storage account in destination region (for example by using AzCopy)
- [Import data into destination cache](../redis-cache/cache-how-to-import-export-data.md) or with the [Import-AzureRmRedisCAche PowerShell cmdlet](https://docs.microsoft.com/en-us/powershell/module/azurerm.rediscache/import-azurermrediscache?view=azurermps-6.4.0).
- Reconfigure your application to use the target Redis instance.

### Option 4: Write data to two Redis instances, read from one instance

This approach requires that the application be modified such that it can write data to multiple cache instances while reading from one of the cache instances. Selecting this approach makes sense if the data stored in Redis is refreshed on a regular basis, with the assumption that all data will be written to the target Redis instance, given enough time for all data to be refreshed.
