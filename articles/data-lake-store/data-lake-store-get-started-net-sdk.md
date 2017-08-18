---
title: '.NET SDK: Account management operations on Azure Data Lake Store | Microsoft Docs'
description: Use Azure Data Lake Store .NET SDK to perform account management operations in the Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun

ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/31/2017
ms.author: nitinme

---
# Account management operations on Azure Data Lake Store using .NET SDK
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

In this article you learn how to perform account management operations on Data Lake Store using .NET SDK. Account management operations include listing the Data Lake Store account for an Azure subscription, etc.

For instructions on how to perform data management operations on Data Lake Store using .NET SDK, see [Filesystem operations on Data Lake Store using .NET SDK](data-lake-store-data-operations-net-sdk.md).

## Prerequisites
* **Visual Studio 2013, 2015, or 2017**. The instructions below use Visual Studio 2015 Update 2.

* **An Azure subscription**. See [Get Azure free trial](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Store account**. For instructions on how to create an account, see [Get started with Azure Data Lake Store](data-lake-store-get-started-portal.md)

## Create a .NET application
1. Open Visual Studio and create a console application.
2. From the **File** menu, click **New**, and then click **Project**.
3. From **New Project**, type or select the following values:

   | Property | Value |
   | --- | --- |
   | Category |Templates/Visual C#/Windows |
   | Template |Console Application |
   | Name |CreateADLApplication |
4. Click **OK** to create the project.
5. Add the Nuget packages to your project.

   1. Right-click the project name in the Solution Explorer and click **Manage NuGet Packages**.
   2. In the **Nuget Package Manager** tab, make sure that **Package source** is set to **nuget.org** and that **Include prerelease** check box is selected.
   3. Search for and install the following NuGet packages:

      * `Microsoft.Azure.Management.DataLake.Store` - This tutorial uses v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - This tutorial uses v2.2.12.

        ![Add a Nuget source](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Create a new Azure Data Lake account")
   4. Close the **Nuget Package Manager**.
6. Open **Program.cs**, delete the existing code, and then include the following statements to add references to namespaces.

        using System;
        using System.IO;
		using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
        using System.Threading;

        using Microsoft.Azure.Management.DataLake.Store;
		using Microsoft.Azure.Management.DataLake.Store.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Rest.Azure.Authentication;

7. Declare the variables as shown below, and provide the values for Data Lake Store name and the resource group name that already exist. Also, make sure the local path and file name you provide here must exist on the computer. Add the following code snippet after the namespace declarations.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                private static DataLakeStoreFileSystemManagementClient _adlsFileSystemClient;

                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>"; // TODO: Replace this value with the name of your existing Data Lake Store account.
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; // TODO: Replace this value with the name of the resource group containing your Data Lake Store account.
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";

                    string localFolderPath = @"C:\local_path\"; // TODO: Make sure this exists and can be overwritten.
                    string localFilePath = Path.Combine(localFolderPath, "file.txt"); // TODO: Make sure this exists and can be overwritten.
                    string remoteFolderPath = "/data_lake_path/";
                    string remoteFilePath = Path.Combine(remoteFolderPath, "file.txt");
                }
            }
        }

In the remaining sections of the article, you can see how to use the available .NET methods to perform operations such as authentication, file upload, etc.

## Authentication

* For end-user authentication for your application, see [End-user authentication with Data Lake Store using .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* For service-to-service authentication for your application, see [Service-to-service authentication with Data Lake Store using .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## Create client objects
The following snippet creates the Data Lake Store account and filesystem client objects, which are used to issue requests to the service.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(creds) { SubscriptionId = _subId };
    _adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(creds);

## List all Data Lake Store accounts within a subscription
The following snippet lists all Data Lake Store accounts within a given Azure subscription.

    // List all ADLS accounts within the subscription
    public static async Task<List<DataLakeStoreAccount>> ListAdlStoreAccounts()
    {
        var response = await _adlsClient.Account.ListAsync();
        var accounts = new List<DataLakeStoreAccount>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## See also
* [Filesystem operations on Data Lake Store using .NET SDK](data-lake-store-data-operations-net-sdk.md)
* [Data Lake Store .NET SDK Reference](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## Next steps
* [Secure data in Data Lake Store](data-lake-store-secure-data.md)
