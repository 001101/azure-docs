---
title: Using OPENROWSET in SQL Analytics #Required; update as needed page title displayed in search results. Include the brand.
description: #Required; Add article description that is displayed in search results.
services: sql-data-warehouse #Required for articles that deal with a service, we will use sql-data-warehouse for now and bulk update later once we have the  service slug assigned by ACOM.
author: filippopovic #Required; update with your GitHub user alias, with correct capitalization.
ms.service: sql-data-warehouse #Required; we will use sql-data-warehouse for now and bulk update later once the service is added to the approved list.
ms.topic: overview #Required
ms.subservice: design #Required will update once these are established.
ms.date: 10/21/2019 #Update with current date; mm/dd/yyyy format.
ms.author: fipopovi #Required; update with your microsoft alias of author; optional team alias.
ms.reviewer: jrasnick
---

# Using OPENROWSET in SQL Analytics
The OPENROWSET bulk rowset provider is accessed by calling the OPENROWSET function and specifying the BULK option. The OPENROWSET(BULK...) function allows you to access files in Azure Storage. 

The `OPENROWSET` function can be referenced in the FROM clause of a query as if it were a table name. `OPENROWSET`. It supports bulk operations through a built-in BULK provider that enables data from a file to be read and returned as a rowset.

OPENROWSET is currently not supported in SQL Analytics pool.

## Syntax

```
--OPENROWSET syntax for reading Parquet files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT=’PARQUET’ }  
)  
[WITH ( {'column_name' 'column_type' }) ]
[AS] table_alias(column_alias,...n)

--OPENROWSET syntax for reading delimited text files
OPENROWSET  
( { BULK 'unstructured_data_path' , 
    FORMAT = ‘CSV’
    [ <bulk_options> ] }  
)  
WITH ( {'column_name' 'column_type' [ 'column_ordinal'] })  
[AS] table_alias(column_alias,...n)
 
<bulk_options> ::=  
[ , FIELDTERMINATOR = 'char' ]    
[ , ROWTERMINATOR = 'char’ ] 
[ , ESCAPE_CHAR = 'char' ] 
[ , FIRSTROW = first_row  ]     
[ , FIELDQUOTE = 'quote_characters']
```

### Arguments 

FORMAT
Format of the input files containing data to be queried. Valid values are:

- 'CSV’ - Covers any delimited text file with row/column separators. Any character can be used as a field separator (i.e. TSV: FIELDTERMINATOR = tab)

- ‘PARQUET’ - Binary file in Parquet format 

unstructured_data_path 
Provides path to data in form of `'<prefix>://<storage_account_path>/<storage_path>'` or
Provides the connectivity protocol and path to the external data source.

| External Data Source       | Prefix | Storage account path                                |
| -------------------------- | ------ | --------------------------------------------------- |
| Azure Blob Storage         | https  | <storage_account>.blob.core.windows.net             |
| Azure Data Lake Store Gen1 | https  | <storage_account>.azuredatalakestore.net/webhdfs/v1 |
| Azure Data Lake Store Gen2 | https  | <storage_account>.dfs.core.windows.net              |

 `'<storage_path>'` is path within your storage that points to folder or file you want to read. If path points to container or folder, all files will be read from that particular container or folder. Files in subfolders will not be included. You can use wildcards to target multiple files or folders. Usage of multiple nonconsecutive wildcards is allowed.
Example that reads all *csv* files starting with *population* from all folders starting with */csv/population*:  `'https://sqlondemandstorage.blob.core.windows.net/csv/population*/population*.csv'`

[WITH ( {'column_name' 'column_type' [ 'column_ordinal'] }) ]

WITH clause allows you to specify columns which you want to read from files. 

- In case of CSV data files, to read all the columns, simply provide column names and their data types. In case you want a subset of columns, use ordinal number to pick the columns from originating data files by ordinal (i.e. columns will be binded by ordinal). WITH clause is mandatory for CSV files.
- In case of Parquet data files, provide column names which match the column names in the originating data files (i.e. columns will be binded by name). If WITH clause is ommited, all columns from Parquet files will be returned.

column_name
Name for the output column. If provided, this name overrides the column name in the source file.

column_type
Data type for the output column. The implicit data type conversion will take place here.

column_ordinal
Ordinal number of the column in the source file(s). This argument is ignored for Parquet files since binding is done by name. Following example would return second column only from CSV file:

```sql
WITH (
	--[country_code] VARCHAR (5) COLLATE Latin1_General_BIN2,
	[country_name] VARCHAR (100) COLLATE Latin1_General_BIN2 2
	--[year] smallint,
	--[population] bigint
)
```

ESCAPE_CHAR = 'char'
Specifies the character in the file that is used to escape itself and all delimiter values in the file. If the escape character is followed by a value other than itself or any of the delimiter values, the escape character is dropped when reading the value.
The ESCAPE_CHAR parameter will be applied regarding of whether FIELDQUOTE is enabled or not. It however will not be used to escape the quoting character. The quoting character will get escaped with double-quotes in alignment with the Excel CSV behavior.

FIELDTERMINATOR ='field_terminator'
Specifies the field terminator to be used. The default field terminator is “**,**” (comma character).

ROWTERMINATOR ='row_terminator'
Specifies the row terminator to be used. The default row terminator is **\r\n** (newline character).

## Examples

Following example returns only 2 columns with ordinal numbers 1 and 4 from *population*.csv* files, and start reading from the first line as there is no header row in files:

```sql
SELECT * 
FROM OPENROWSET(
        BULK 'https://sqlondemandstorage.blob.core.windows.net/csv/population/population*.csv',
        FORMAT = 'CSV',
		FIRSTROW = 1
    )
WITH (
    [country_code] VARCHAR (5) COLLATE Latin1_General_BIN2 1,
    [population] bigint 4
) AS [r]
```



Following example returns all columns of first row from census data set in parquet format without specyfing column names and data types: 

```sql
SELECT 
	TOP 1 *
FROM  
	OPENROWSET(
		BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
		FORMAT='PARQUET'
	) AS [r]
```



## Next steps

- [Query data in storage](query-data-in-storage.md)