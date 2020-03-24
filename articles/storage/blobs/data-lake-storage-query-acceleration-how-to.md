---
title: Filter data by using Azure Data Lake Storage query acceleration (preview) | Microsoft Docs
description: Use .NET and query acceleration (preview) to retrieve a subset of data from your storage
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: normesta
ms.reviewer: jamsbak
---

# Filter data by using Azure Data Lake Storage query acceleration (preview)

This article shows you how to use .NET and query acceleration (Preview) to retrieve a subset of data from your storage account. 

Query acceleration (Preview) is a new capability for Azure Data Lake Storage that enables applications and analytics frameworks to dramatically optimize data processing by retrieving only the data that they require to perform a given operation. To learn more, see [Azure Data Lake Storage Query Acceleration (Preview)](data-lake-storage-query-acceleration.md).

> [!NOTE]
> The query acceleration feature is in public preview, and is available in the West Central US and West US 2 regions. To review limitations, see the [Known issues](data-lake-storage-known-issues.md) article. To enroll in the preview, see [this form](https://aka.ms/adls/qa-preview-signup). 

## Prerequisites

### [.NET](#tab/dotnet)

- To access Azure Storage, you'll need an Azure subscription. If you don't already have a subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

- A **general-purpose v2** storage account. see [Create a storage account](../common/storage-quickstart-create-account.md).

### [Java](#tab/java)

- To access Azure Storage, you'll need an Azure subscription. If you don't already have a subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.

- A **general-purpose v2** storage account. see [Create a storage account](../common/storage-quickstart-create-account.md).

- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) version 8 or above.

- [Apache Maven](https://maven.apache.org/download.cgi). 

  > [!NOTE] 
  > This article assumes that you've created a Java project by using Apache Maven. For an example of how to create a project by using Apache Maven, see [Setting up](storage-quickstart-blobs-java.md#setting-up).
  
---

## Install packages 

### [.NET](#tab/dotnet)

1. Download the query acceleration packages. You can obtain a compressed .zip file that contains these packages by using this link: [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net). 

2. Extract the files in this .zip file to any directory on your local drive. 

3. In a console window (such as cmd, PowerShell, or Bash), switch to the directory that contains the extracted files.

4. Use the [nuget.exe CLI](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-nuget-cli) to install each package to your project. 

   These example commands install each package into a subdirectory of a project named `packages`.
  
   ```console
   nuget add Azure.Storage.Blobs.12.4.0-preview.1.nupkg -source C:\Users\contoso\myProject\packages
   nuget add Azure.Storage.Common.12.3.0-preview.1.nupkg -source C:\Users\contoso\myProject\packages
   nuget add Azure.Storage.QuickQuery.12.0.0-preview.1.nupkg -source C:\Users\contoso\myProject\packages
   ```
5. Add the following xml to your project file.

   ```xml
    <ItemGroup>
        <PackageReference Include="Azure.Storage.Blobs" Version="12.4.0-preview.1" />
        <PackageReference Include="Azure.Storage.Common" Version="12.3.0-preview.1" />
        <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
  </ItemGroup>
   ```
   
> ![NOTE]
> To learn about other ways to install a NuGet package, see [Ways to install a NuGet Package](https://docs.microsoft.com/nuget/consume-packages/overview-and-workflow#ways-to-install-a-nuget-package).


### [Java](#tab/java)

1. Create directory in the root of your project. The root directory is the directory that contains the **pom.xml** file.

   > [!NOTE]
   > The examples in this article assume that the name of the directory is **lib**.

2. Download the query acceleration packages. You can obtain a compressed .zip file that contains these packages by using this link: [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java). 

3. Extract the files in this .zip file to the directory that you created. In our example, that directory is named **lib**. 

4. Open the *pom.xml* file in your text editor. Add the following dependency elements to the group of dependencies. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-core-http-netty</artifactId>
      <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>org.apache.avro</groupId>
      <artifactId>avro</artifactId>
      <version>1.9.2</version>
   </dependency>
   <dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-csv</artifactId>
    <version>1.8</version>
</dependency>
   <!-- Local dependencies -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-blob</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-blob-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-common</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-common-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-quickquery</artifactId>
       <version>12.0.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-quickquery-12.0.0-beta.1.jar</systemPath>
   </dependency>
   ```

---

## Add statements


### [.NET](#tab/dotnet)

Add these `using` statements to the top of your code file.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

Query acceleration retrieves CSV and Json formatted data. Therefore, make sure to add using statements for any CSV or Json parsing libraries that you choose to use. The examples that appear in this article parse a CSV file by using the [CsvHelper](https://www.nuget.org/packages/CsvHelper/) library that is available on NuGet. Therefore, we'd add these using statements to the top of the code file.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

### [Java](#tab/java)

Add these `import` statements to the top of your code file.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import com.azure.storage.quickquery.*;
import com.azure.storage.quickquery.models.*;
import java.io.*;
import org.apache.commons.csv.*;
```

---

## Retrieve data by using a filter

You can use SQL to specify the row filter predicates and column projections in a query acceleration request. The following code queries a CSV file in storage and returns all rows of data where the third column matches the value `Hemingway, Ernest`. 

- In the SQL query, the keyword `BlobStorage` is used to denote the file that is being queried.

- Column references are specified as `_N` where the first column is `_1`. If the source file contains a header row, then you can refer to columns by the name that is specified in the header row. 

### [.NET](#tab/dotnet)

The async method `BlobQuickQueryClient.QueryAsync` sends the query to the query acceleration API, and then streams the results back to the application as a [Stream](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8) object.

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        using (var reader = new StreamReader((await blob.GetQuickQueryClient().QueryAsync(query,
                new CvsTextConfiguration() { HasHeaders = headers }, 
                new CvsTextConfiguration() { HasHeaders = false }, 
                new ErrorHandler(),
                new BlobRequestConditions(), 
                new ProgressHandler(),
                CancellationToken.None)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) 
            { HasHeaderRecord = false }))
            {
                while (await parser.ReadAsync())
                {
                    parser.Context.Record.All(cell =>
                    {
                        Console.Out.Write(cell + "  ");
                        return true;
                    });
                    Console.Out.WriteLine();
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

class ErrorHandler : IBlobQueryErrorReceiver
{
    public void ReportError(BlobQueryError err)
    {
        Console.Error.WriteLine(String.Format("Error: {1}:{2}", err.Name, err.Description));
    }
}

class ProgressHandler : IProgress<long>
{
    public void Report(long value)
    {
        Console.Error.WriteLine("Bytes scanned: " + value.ToString());
    }
}

```

### [Java](#tab/java)

The method `BlobQuickQueryClient.openInputStream()` sends the query to the query acceleration API, and then streams the results back to the application as a `InputStream` object which can be read like any other InputStream object.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    DumpQueryCsv(blobClient, expression, false);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
    
        BlobQuickQueryDelimitedSerialization input = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');

        BlobQuickQueryDelimitedSerialization output = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(false)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
                
        BlobRequestConditions requestConditions = null;
        /* ErrorReceiver determines what to do on errors. */
        ErrorReceiver<BlobQuickQueryError> errorReceiver = System.out::println;

        /* ProgressReceiver details how to log progress*/
        com.azure.storage.common.ProgressReceiver progressReceiver = System.out::println;
    
        /* Create a query acceleration client to the blob. */
        BlobQuickQueryClient qqClient = new BlobQuickQueryClientBuilder(blobClient)
            .buildClient();
        /* Open the query input stream. */
        InputStream stream = qqClient.openInputStream(query, input, output, requestConditions, errorReceiver, progressReceiver);
            
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL)) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
    }
}
```

---

## Retrieve specific columns

You can scope your results to a subset of columns. That way you retrieve only the columns needed to perform a given calculation. This improves application performance and reduces cost because less data is transferred over the network. 

This code retrieves only the `PublicationYear` column for all books in the data set. It also uses the information from the header row in the source file to reference columns in the query.


### [.NET](#tab/dotnet)

```cs
static async Task QueryPublishDates(BlockBlobClient blob)
{
    string query = @"SELECT PublicationYear FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### [Java](#tab/java)

```java
static void QueryPublishDates(BlobClient blobClient)
{
    String expression = "SELECT PublicationYear FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

The following code combines row filtering and column projections into the same query. 

### [.NET](#tab/dotnet)

```cs
static async Task QueryMysteryBooks(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    await DumpQueryCsv(blob, query, true);
}
```

### [Java](#tab/java)

```java
static void QueryMysteryBooks(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

## Next steps

- [Query acceleration enrollment form](https://aka.ms/adls/queryaccelerationpreview)    
- [Azure Data Lake Storage query acceleration (Preview)](data-lake-storage-query-acceleration.md)
- Query acceleration SQL language reference
- Query acceleration REST API reference
