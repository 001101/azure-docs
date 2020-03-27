---
title: Query acceleration SQL language reference (preview)
titleSuffix: Azure Storage
description: Learn how to use query acceleration sql syntax.
services: storage
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: normesta
ms.subservice: data-lake-storage-gen2
ms.reviewer: ereilebr
---

# Query acceleration SQL language reference (preview)

Query acceleration supports an ANSI SQL-like language for expressing queries over blob contents.  The query acceleration SQL dialect is a subset of ANSI SQL, with a limited set of supported data types, operators, etc., but it also expands on ANSI SQL to support queries over hierarchical semi-structured data formats such as JSON. 

> [!NOTE]
> The query acceleration feature is in public preview, and is available in the West Central US and West US 2 regions. To review limitations, see the [Known issues](data-lake-storage-known-issues.md) article. To enroll in the preview, see [this form](https://aka.ms/adls/qa-preview-signup).

## SELECT Syntax

The only SQL statement supported by query acceleration is the SELECT statement. This example returns every row for which expression returns true.

```sql
SELECT * FROM table [WHERE expression] [LIMIT limit]
```

For CSV-formatted data, *table* must be `BlobStorage`.  This means that the query will run against whichever blob was specified in the REST call.
For JSON-formatted data, *table* is a "table descriptor."   See the [Table Descriptors](#table-descriptors) section of this article.

In the following example, for each row for which the WHERE *expression* returns true, this statement will return a new row that is made from evaluating each of the projection expressions.


```sql
SELECT expression [, expression …] FROM table [WHERE expression] [LIMIT limit]
```

The following example returns an aggregate computation (For example: the average value of a particular column) over each of the rows for which *expression* returns true. 

```sql
SELECT aggregate_expression FROM table [WHERE expression] [LIMIT limit]
```

The following example returns suitable offsets for splitting a CSV-formatted blob.  See the [Sys.Split](#sys-split) section of this article.

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

<a id="data-types" />

## Data Types

|Data Type|Description|
|---------|-------------------------------------------|
|INT      |64-bit signed integer.                     |
|FLOAT    |64-bit ("double-precision") floating point.|
|STRING   |Variable-length Unicode string.            |
|TIMESTAMP|A point in time.                           |
|BOOLEAN  |True or false.                             |

When reading values from CSV-formatted data, all values are read as strings.  String values may be converted to other types using CAST expressions.  Values may be implicitly cast to other types depending on context. for more info, see [Data type precedence (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

## Expressions

### Referencing fields

For JSON-formatted data, or CSV-formatted data with a header row, fields may be referenced by name.  Field names can be quoted or unquoted. Quoted field names are enclosed in double-quote characters ("), may contain spaces, and are case-sensitive.  Unquoted field names are case-insensitive, and may not contain any special characters.

In CSV-formatted data, fields may also be referenced by ordinal, prefixed with an underscore (_) character.  For example, the first field may be referenced as _1, or the eleventh field may be referenced as _11.  Referencing fields by ordinal is useful for CSV-formatted data that does not contain a header row, in which case the only way to reference a particular field is by ordinal.

### Operators

The following standard SQL operators are supported:

``=``, ``!=``, ``<>``, ``<``, ``<=``, ``>``, ``>=``, ``+``, ``-``, ``/``, ``*``, ``%``, ``AND``, ``OR``, ``NOT``, ``CAST``, ``BETWEEN``, ``IN``, ``NULLIF``, ``COALESCE``

If data types on the left and right of an operator are different, then automatic conversion will be performed according to the rules specified here: [Data type precedence (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-precedence-transact-sql?view=sql-server-2017).

The query acceleration SQL language supports only a very small subset of the data types discussed in that article.  See the [Data Types](#data-types) section of this article.

### Casts

The query acceleration SQL language supports the CAST operator, according to the rules here: [Data type conversion (Database Engine)](https://docs.microsoft.com/sql/t-sql/data-types/data-type-conversion-database-engine?view=sql-server-2017).  

The query acceleration SQL language supports only a tiny subset of the data types discussed in that article.  See the [Data Types](#data-types) section of this article.

### String functions

The query acceleration SQL language supports the following standard SQL string functions:

``LIKE``, ``CHAR_LENGTH``, ``LOWER``, ``UPPER``, ``SUBSTRING``, ``TRIM``, ``LEADING``, ``TRAILING``.

### Date functions

The following standard SQL date functions are supported:

``DATE_ADD``, ``DATE_DIFF``, ``EXTRACT``, ``TO_STRING``, ``TO_TIMESTAMP``.

Currently we convert all the [date formats of standard IS08601](https://www.w3.org/TR/NOTE-datetime). 

#### DATE_ADD function

The query acceleration SQL language supports year, month, day, hour, minute, second for the ``DATE_ADD`` function.

Examples:

```sql
DATE_ADD(datepart, quantity, timestamp)
DATE_ADD('minute', 1, CAST('2017-01-02T03:04:05.006Z' AS TIMESTAMP)
```

#### DATE_DIFF function

The query acceleration SQL language supports year, month, day, hour, minute, second for the ``DATE_DIFF`` function.

```sql
DATE_DIFF(datepart, timestamp, timestamp)
DATE_DIFF('hour','2018-11-09T00:00+05:30','2018-11-09T01:00:23-08:00') 
```

#### EXTRACT function

For EXTRACT other than date part supported for the ``DATE_ADD`` function, the query acceleration SQL language supports timezone_hour and timezone_minute as date part.

Examples:

```sql
EXTRACT(datepart FROM timestampstring)
EXTRACT(YEAR FROM '2010-01-01T')
```

#### TO_STRING function

Examples:

```sql
TO_STRING(TimeStamp , format)
TO_STRING(CAST('1969-07-20T20:18Z' AS TIMESTAMP),  'MMMM d, y')
```

This table describes strings that you can use to specify the output format of the ``TO_STRING`` function.

|Format string    |Output                               |
|-----------------|-------------------------------------|
|yy               |Year in 2 digit format – 1999 as '99'|
|y                |Year in 4 digit format               |
|yyyy             |Year in 4 digit format               |
|M                |Month of year – 1                    |
|MM               |Zero padded Month – 01               |
|MMM              |Abbr. month of Year  -JAN            |
|MMMM             |Full month – May                      |
|d                |Day of month (1-31)                  |
|dd               |Zero padded day of Month (01-31)     |
|a                |AM or PM                             |
|h                |Hour of day (1-12)                   |
|hh               |Zero padded Hours od day (01-12)     |
|H                |Hour of day (0-23)                   |
|HH               |Zero Padded hour of Day (00-23)      |
|m                |Minute of hour (0-59)                |
|mm               |Zero padded minute (00-59)           |
|s                |Second of Minutes (0-59)             |
|ss               |Zero padded Seconds (00-59)          |
|S                |Fraction of Seconds (0.1-0.9)        |
|SS               |Fraction of Seconds (0.01-0.99)      |
|SSS              |Fraction of Seconds (0.001-0.999)    |
|X                |Offset in Hours                      |
|XX or XXXX       |Offset in hours and minutes (+0430)  |
|XXX or XXXXX     |Offset in hours and minutes (-07:00) |
|x                |Offset in hours (7)                  |
|xx or xxxx       |Offset in hour and minute (+0530)    |
|Xxx or xxxxx     |Offset in hour and minute (+05:30)   |

#### TO_TIMESTAMP function

Only IS08601 formats are supported.

Examples:

```sql
TO_TIMESTAMP(string)
TO_TIMESTAMP('2007T')
```

> [!NOTE]
> You can also use the ``UTCNOW`` function to get the system time.


## Aggregate Expressions

A SELECT statement may contain either one or more projection expressions or a single aggregate expression.  The following aggregate expressions are supported:

|Expression|Description|
|--|--|
|[COUNT(\*)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Returns the number of records which matched the predicate expression.|
|[COUNT(expression)](https://docs.microsoft.com/sql/t-sql/functions/count-transact-sql?view=sql-server-ver15)    |Returns the number of records for which expression is non-null.|
|[AVERAGE(expression)](https://docs.microsoft.com/sql/t-sql/functions/avg-transact-sql?view=sql-server-ver15)    |Returns the average of the non-null values of expression.|
|[MIN(expression)](https://docs.microsoft.com/sql/t-sql/functions/min-transact-sql?view=sql-server-ver15)    |Returns the minimum non-null value of expression.|
|[MAX(expression](https://docs.microsoft.com/sql/t-sql/functions/max-transact-sql?view=sql-server-ver15))    |Returns the maximum non-null value of expression.|
|[SUM(expression)](https://docs.microsoft.com/sql/t-sql/functions/sum-transact-sql?view=sql-server-ver15)    |Returns the sum of all non-null values of expression.|

### MISSING

The ``IS MISSING`` operator is the only non-standard that the query acceleration SQL language supports.  For JSON data, if a field is missing from a particular input record, the expression field ``IS MISSING`` will evaluate to the Boolean value true.

<a id="table-descriptors" />

## Table Descriptors

For CSV data, the table name is always `BlobStorage`.  For example:

```sql
SELECT * FROM BlobStorage
```

For JSON data, additional options are available:

```sql
SELECT * FROM BlobStorage[*].path
```

This allows queries over subsets of the JSON data.

For JSON queries, you can mention the path in part of the FROM clause. These paths will help to parse the subset of JSON data. These paths can reference to JSON Array and Object values.

Let's take an example to understand this in more detail.

This is our sample data:

```json
{
  "id": 1,
  "name": "mouse",
  "price": 12.5,
  "tags": [
    "wireless",
    "accessory"
  ],
  "dimensions": {
    "length": 3,
    "width": 2,
    "height": 2
  },
  "weight": 0.2,
  "warehouses": [
    {
      "latitude": 41.8,
      "longitude": -87.6
    }
  ]
}
```

You might be interested only in the `warehouses` JSON object from the above data. The `warehouses` object is a JSON array type, so you can mention this in the FROM clause. Your sample query can look something like this.

```sql
SELECT latitude FROM BlobStorage[*].warehouses[*]
```

The query gets all fields but selects only the latitude.

If you wanted to access only the `dimensions` JSON object value, you could use refer to that object in your query. For example:

```sql
SELECT length FROM BlobStorage[*].dimensions
```

This also limits your access to members of the `dimensions` object. If you want to access other members of JSON fields and inner values of JSON objects, then you might use a queries such as shown in the following example:

```sql
SELECT weight,warehouses[0].longitude,id,tags[1] FROM BlobStorage[*]
```

> [!NOTE]
> BlobStorage and BlobStorage[\*] both refer to the whole object. However, if you have a path in the FROM clause, then you'll need to use BlobStorage[\*].path

<a id="sys-split" />

## Sys.Split

This is a special form of the SELECT statement, which is only available for CSV-formatted data:

```sql
SELECT sys.split(split_size)FROM BlobStorage
```

In this form, the query returns a list of non-negative integers of sizes of record aligned regions.

### Example

ABCDEABCD<strong>\n</strong>ABCDEABCDEABCDE<strong>\n</strong>ABCDEAB<strong>\n</strong>ABCDE<strong>\n</strong>ABCD<strong>\n</strong>

The following are examples for the above csv blob:

|Command|Result|
|---|---|
|SELECT  SYS.SPLIT(1) FROM BlobStorage<br>SELECT  SYS.SPLIT(6) FROM BlobStorage|"10\n16\n8\n6\n5\n"|
|SELECT  SYS.SPLIT(7) FROM BlobStorage<br>SELECT  SYS.SPLIT(8) FROM BlobStorage|"10\n16\n8\n11\n"|
|SELECT  SYS.SPLIT(9) FROM BlobStorage|"10\n16\n14\n5\n"|
|SELECT  SYS.SPLIT(10) FROM BlobStorage|"10\n16\n14\n5\n"|
|SELECT  SYS.SPLIT(11) FROM BlobStorage<br>SELECT  SYS.SPLIT(14) FROM BlobStorage|"26\n14\n5\n"|
|SELECT  SYS.SPLIT(15) FROM BlobStorage<br>SELECT  SYS.SPLIT(26) FROM BlobStorage|"26\n19\n"|
|SELECT  SYS.SPLIT(27) FROM BlobStorage<br>SELECT  SYS.SPLIT(34) FROM BlobStorage|"34\n11\n"|
|SELECT  SYS.SPLIT(28) FROM BlobStorage<br>SELECT  SYS.SPLIT(40) FROM BlobStorage|"40\n5\n"|
|SELECT  SYS.SPLIT(41) FROM BlobStorage<br>SELECT  SYS.SPLIT(…) FROM BlobStorage|"45\n"|

>[!NOTE]
> The split_size must be at least 10 MB (10485760).

## See also

- [Azure Data Lake Storage query acceleration (preview)](data-lake-storage-query-acceleration.md)
- [Filter data by using Azure Data Lake Storage query acceleration (preview)](data-lake-storage-query-acceleration-how-to.md)

