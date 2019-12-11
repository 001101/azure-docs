---
title: Using external tables in SQL Analytics
description:
services: synapse analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice:
ms.date: 10/21/2019
ms.author: jrasnick
ms.reviewer: jrasnick
---

# Using external tables in SQL Analytics 
An external table points to data located in Hadoop, Azure Storage blob or Azure Data Lake Store. External tables are used to read data from files or write data to files in Azure Storage.



## External tables in SQL Analytics pool

Use an external table to:

- Query Hadoop or Azure blob storage data with Transact-SQL statements.
- Import and store data from Hadoop, Azure Blob Storage, and Azure Data Lake Store into Azure SQL Data Warehouse.

When used in conjunction with the CREATE TABLE AS SELECT statement, selecting from an external table imports data into a SQL Analytics pool. External tables are useful for loading data. For a loading tutorial, see [Use PolyBase to load data from Azure blob storage](../../sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md).



## External tables in SQL Analytics on-demand
Use an external table to:

- Query data in Azure blob storage or Azure Data Lake Store with Transact-SQL statements
- Store results of a SQL Analytics on-demand query to files in Azure Blob Storage or Azure Data Lake Store using [CETAS](development-tables-cetas.md).

You can create external table with following steps:

1. CREATE EXTERNAL DATA SOURCE
2. CREATE EXTERNAL FILE FORMAT
3. CREATE EXTERNAL TABLE



### CREATE EXTERNAL DATA SOURCE

External data sources are used to establish connectivity to storage accounts.

#### Syntax

```
CREATE EXTERNAL DATA SOURCE <data_source_name>
WITH
(	LOCATION         = '<prefix>://<path>' )
[;]
```

#### Arguments

data_source_name
Specifies the user-defined name for the data source. The name must be unique within the database in SQL Server.

LOCATION = `'<prefix>://<path>'` - 
Provides the connectivity protocol and path to the external data source. The path can include a container in the form of  `'<prefix>://<path>/container'`, as well as a folder in the form of `'<prefix>://<path>/container/folder'`.

| External Data Source       | Location prefix | Location path                                       |
| -------------------------- | --------------- | --------------------------------------------------- |
| Azure Blob Storage         | https           | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Store Gen1 | https           | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | https           | <storage_account>.dfs.core.windows.net              |

#### Example

The following example creates an external data source pointing to US population census data:

```sql
CREATE EXTERNAL DATA SOURCE population_ds
WITH
(
	LOCATION =  'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county'
)
```



### CREATE EXTERNAL FILE FORMAT

Creates an External File Format object which defines external data stored in Azure Blob Storage or Azure Data Lake Store. Creating an external file format is a prerequisite for creating an External Table. By creating an External File Format, you specify the actual layout of the data referenced by an external table.

SQL Analytics on-demand supports the following file formats:

- Delimited Text
- Parquet

#### Syntax

```
-- Create an external file format for PARQUET files.  
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = PARQUET  
    [ , DATA_COMPRESSION = {  
        'org.apache.hadoop.io.compress.SnappyCodec'  
      | 'org.apache.hadoop.io.compress.GzipCodec'      }  
    ]);  

--Create an external file format for DELIMITED TEXT files
CREATE EXTERNAL FILE FORMAT file_format_name  
WITH (  
    FORMAT_TYPE = DELIMITEDTEXT  
    [ , FORMAT_OPTIONS ( <format_options> [ ,...n  ] ) ]  
    );  

<format_options> ::=  
{  
    FIELD_TERMINATOR = field_terminator  
    | STRING_DELIMITER = string_delimiter 
    | First_Row = integer    
    | USE_TYPE_DEFAULT = { TRUE | FALSE } 
    | Encoding = {'UTF8' | 'UTF16'} 
}
```

#### Arguments

file_format_name - 
Specifies a name for the external file format.

FORMAT_TYPE = [ PARQUET | DELIMITEDTEXT] - Specifies the format of the external data.

- PARQUET - Specifies a Parquet format.
- DELIMITEDTEXT - Specifies a text format with column delimiters, also called field terminators.

FIELD_TERMINATOR = *field_terminator* -
Applies only to delimited text files. The field terminator specifies one or more characters that mark the end of each field (column) in the text-delimited file. The default is the pipe character (ꞌ|ꞌ). 

Examples:

- FIELD_TERMINATOR = '|'
- FIELD_TERMINATOR = ' '
- FIELD_TERMINATOR = ꞌ\tꞌ

STRING_DELIMITER = *string_delimiter* -
Specifies the field terminator for data of type string in the text-delimited file. The string delimiter is one or more characters in length and is enclosed with single quotes. The default is the empty string (""). 

Examples:

- STRING_DELIMITER = '"'
- STRING_DELIMITER = '*'
- STRING_DELIMITER = ꞌ,ꞌ

FIRST_ROW = *First_row_int* - 
Specifies the row number that is read first and applies to all files. If the value is set to two, the first row in every file (header row) is skipped when the data is loaded. Rows are skipped based on the existence of row terminators (/r/n, /r, /n). 

USE_TYPE_DEFAULT = { TRUE | **FALSE** } -
Specifies how to handle missing values in delimited text files when retrieving data from the text file.

TRUE - 
When retrieving data from the text file, store each missing value by using the default value for the data type of the corresponding column in the external table definition. For example, replace a missing value with:

- 0 if the column is defined as a numeric column. Decimal columns are not supported and will error.
- Empty string ("") if the column is a string column.
- 1900-01-01 if the column is a date column.

FALSE - 
Store all missing values as NULL. Any NULL values that are stored by using the word NULL in the delimited text file are imported as the string 'NULL'.

Encoding = {'UTF8' | 'UTF16'} - 
SQL Analytics on-demand can read UTF8 and UTF16 encoded delimited text files.

DATA_COMPRESSION = *data_compression_method* - 
This argument is ignored when reading from external tables. It is used only when writing to external tables using [CETAS](development-tables-cetas.md). It specifies the data compression method for the external data.

The PARQUET file format type supports the following compression methods:

- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.GzipCodec'
- DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'

#### Example

The following example creates an external file format for census files:

```sql
CREATE EXTERNAL FILE FORMAT census_file_format
WITH 
(  
    FORMAT_TYPE = PARQUET,
    DATA_COMPRESSION = 'org.apache.hadoop.io.compress.SnappyCodec'
)
```



### CREATE EXTERNAL TABLE

This command creates an external table for SQL Analytics to access data stored in Azure blob storage or Azure Data Lake Store. 

#### Syntax

```
CREATE EXTERNAL TABLE { database_name.schema_name.table_name | schema_name.table_name | table_name }
    ( <column_definition> [ ,...n ] )  
    WITH (
        LOCATION = 'folder_or_filepath',  
        DATA_SOURCE = external_data_source_name,  
        FILE_FORMAT = external_file_format_name   
    )  
[;]  

<column_definition> ::=
column_name <data_type>
    [ COLLATE collation_name ]
```

#### Arguments

*{ database_name.schema_name.table_name | schema_name.table_name | table_name }* 

The one to three-part name of the table to create. For an external table, SQL Analytics on-demand stores only the table metadata. No actual data is moved or stored in SQL Analytics on-demand.

<column_definition>, ...*n* ]

CREATE EXTERNAL TABLE supports the ability to configure column name, data type, nullability, and collation. You can't use the DEFAULT CONSTRAINT on external tables.

The column definitions, including the data types and number of columns, must match the data in the external files. If there's a mismatch, the file rows will be rejected when querying the actual data.

When reading from Parquet files, you can specify only the columns you want to read and skip the rest.

LOCATION = '*folder_or_filepath*'

Specifies the folder or the file path and file name for the actual data in Azure blob storage. The location starts from the root folder. The root folder is the data location specified in the external data source.

If you specify a folder LOCATION, a SQL Analytics on-demand query that selects from the external table will retrieve files from the folder. Unlike Hadoop and PolyBase, SQL Analytics on-demand doesn't return subfolders. It returns files for which the file name begins with an underline (_) or a period (.).

In this example, if LOCATION='/webdata/', a SQL Analytics on-demand query will return rows from mydata.txt and_hidden.txt. It won't return mydata2.txt and mydata3.txt because they are located in a subfolder.

![Recursive data for external tables](media/development-tables-external-tables/folder-traversal.png)



DATA_SOURCE = *external_data_source_name* - Specifies the name of the external data source that contains the location of the external data. To create an external data source, use [CREATE EXTERNAL DATA SOURCE](#create-external-data-source).

FILE_FORMAT = *external_file_format_name* - Specifies the name of the external file format object that stores the file type and compression method for the external data. To create an external file format, use [CREATE EXTERNAL FILE FORMAT](#create-external-file-format).

#### Permissions

You need proper credentials with list and read permissions to be able to select from an external table.

#### Example

The following example creates an external table using the previously created external data source and external file format, and returns the first row:

```sql
CREATE EXTERNAL TABLE census_external_table
( 
    decennialTime varchar(20),
    stateName varchar(100),
    countyName varchar(100),
    population int,
    race varchar(50),
    sex	varchar(10),
    minAge int,
    maxAge int
)  
WITH (
    LOCATION = 'year=*/*.parquet', 
    DATA_SOURCE = population_ds,  
    FILE_FORMAT = census_file_format
)
GO

/* make sure you have credentials for storage account access created
IF EXISTS (SELECT * FROM sys.credentials WHERE name = 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer')
DROP CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]
GO

CREATE CREDENTIAL [https://azureopendatastorage.blob.core.windows.net/censusdatacontainer]  
WITH IDENTITY='SHARED ACCESS SIGNATURE',  
SECRET = ''
GO
*/
SELECT TOP 1 * FROM census_external_table
```



## Next steps


Check [CETAS](development-tables-cetas.md) to save the query results to an external table in Azure Storage, or start querying [Spark tables](development-storage-files-spark-tables.md).