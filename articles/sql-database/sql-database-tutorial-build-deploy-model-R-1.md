---
title: "Tutorial: Prepare data to train a predictive model in R"
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: In part one of this three-part tutorial series, you'll prepare the data from an Azure SQL database to train a predictive model in R with Azure SQL Database Machine Learning Services (preview).
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom:
ms.devlang: r
ms.topic: tutorial
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 05/02/2019
---

# Tutorial: Prepare data to train a predictive model in R with Azure SQL Database Machine Learning Services (preview)

In this tutorial, you'll learn how to import a sample database into an Azure SQL database and prepare the data to be used for training a predictive model in R.

For this tutorial series, imagine you own a ski rental business and you want to predict the number of rentals that you'll have on a future date. This information will help you get your stock, staff, and facilities ready.

Predictive modeling is a powerful way to add intelligence to your application. It enables applications to predict outcomes against new data. Predictive analytics involves three basic phases: **data preparation**, **model training**, and **model deployment**.

This tutorial is **part one of a three-part tutorial series**.

In part one, you'll learn how to:

> [!div class="checklist"]
> * Import a database backup file into an Azure SQL database
> * Load the data from the Azure SQL database into a data frame using R
> * Prepare the data by identifying some columns as categorical

In [part two](sql-database-tutorial-build-deploy-model-R-2.md), you'll learn how to create and train multiple models, and then choose the most accurate one.

In [part three](sql-database-tutorial-build-deploy-model-R-2.md), you'll learn how to store the model in a database, and then create a stored procedure that can make predictions based on new data.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## Prerequisites

* If you don't have an Azure subscription, [create an account](https://azure.microsoft.com/free/) before you begin.

* To run the example code in these exercises, you must first have an Azure SQL Database Server with Machine Learning Services enabled. During the public preview, Microsoft will onboard you and enable machine learning for your existing or new database. Follow the steps in [Sign up for the preview](sql-database-machine-learning-services-overview.md#signup).

* Install an R IDE such as [RStudio Desktop](https://www.rstudio.com/products/rstudio/download/) or [R Tools for Visual Studio (RTVS)](https://www.visualstudio.com/vs/rtvs). This tutorial will use RStudio Desktop.

* Install [Azure Data Studio](https://docs.microsoft.com/sql/azure-data-studio/what-is) or [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) (SSMS). You can run SQL commands using other database management or query tools, but this example assumes Azure Data Studio or SSMS.

## Sign in to the Azure portal

Sign in to the [Azure portal](https://portal.azure.com/).

## Import the sample database

The dataset used in this tutorial is hosted in a SQL database table that contains rental data from previous years. The table is in a database named TutorialDB that can be imported from a `.bacpac` backup file.
 
The first thing to do is import the TutorialDB database into your own Azure SQL database.

> NOTE TO ME: Add a screenshot of the "Import database" blade with info filled in, and/or an animated gif of the parts/all of the sequence.

1. Download the file [TutorialDB.bacpac](https://sqlchoice.blob.core.windows.net/sqlchoice/static/TutorialDB.bacpac) to your local computer.
1. In the Azure portal, select **All services**, search for and select **SQL servers**, and then select your SQL server or create a new one.
1. In the Overview page for the SQL server, select **Import database**.
1. Select your subscription.
1. For **Storage**, select a storage account and then select an existing container or create a new one.
1. Select **Upload** and browse to and upload the **TutorialDB.bacpac** file you downloaded.
1. Choose **TutorialDB.bacpac** in the list of items in the container and click **Select**.
1. Choose the **Pricing tier**. During the preview, choose the **Gen5/vCore** configuration for the database.
1. Give the database the name "TutorialDB".
1. Enter the remaining information and select **OK**.

Once the database is created, it should contain a table named rental_data.
You can verify this table by connecting to the TutorialDB database in Azure Data Studio or SSMS and running the following query.

```sql
USE tutorialdb;

SELECT *
FROM [dbo].[rental_data];
```

You should see something similar to this.

```results
   Year  Month  Day  RentalCount  WeekDay  Holiday  Snow
1  2014    1     20      445         2        1      0
2  2014    2     13       40         5        0      0
3  2013    3     10      456         1        0      0
4  2014    3     31       38         2        0      0
5  2014    4     24       23         5        0      0
6  2015    2     11       42         4        0      0
7  2013    4     28      310         1        0      0
8  2014    3      8      240         7        0      0
```

## Load the data into a data frame using R

To use the data in R, you'll load the data from the Azure SQL database into a data frame (`rentaldata`).

Create a new RScript file in RStudio and run the following script. Insert your own connection information for **Server**, **UID**, and **PWD**.

```r
#Define the connection string to connect to the TutorialDB database
connStr <- paste("Driver=SQL Server",
               "; Server=", "<Azure SQL Database Server>",
               "; Database=TutorialDB",
               "; UID=", "<user>",
               "; PWD=", "<password>",
                  sep = "");

#Get the data from the table
SQL_rentaldata <- RxSqlServerData(table = "dbo.rental_data", connectionString = connStr, returnDataFrame = TRUE);

#Import the data into a data frame
rentaldata <- rxImport(SQL_rentaldata);

#Take a look at the structure of the data and the top rows
head(rentaldata);
str(rentaldata);
```

You should see results similar to the following.

```results
   Year  Month  Day  RentalCount  WeekDay  Holiday  Snow
1  2014    1     20      445         2        1      0
2  2014    2     13       40         5        0      0
3  2013    3     10      456         1        0      0
4  2014    3     31       38         2        0      0
5  2014    4     24       23         5        0      0
6  2015    2     11       42         4        0      0
'data.frame':       453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : num  2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : int  1 0 0 0 0 0 0 0 0 0 ...
$ Snow       : num  0 0 0 0 0 0 0 0 0 0 ...
```

## Prepare the data

In the *data preparation* phase, data to be used in a predictive model often needs to be cleaned before it can be used. In this sample database, most of the preparation has already been done, but you'll do one more preparation here.

Use the following R script to identify three columns as *categories* by changing the data types to *factor*. Changing these data types helps when building the model because it explicitly specifies that values in these columns are categorical.

```r
#Changing the three factor columns to factor types
rentaldata$Holiday <- factor(rentaldata$Holiday);
rentaldata$Snow    <- factor(rentaldata$Snow);
rentaldata$WeekDay <- factor(rentaldata$WeekDay);

#Visualize the dataset after the change
str(rentaldata);
```

```results
data.frame':      453 obs. of  7 variables:
$ Year       : int  2014 2014 2013 2014 2014 2015 2013 2014 2013 2015 ...
$ Month      : num  1 2 3 3 4 2 4 3 4 3 ...
$ Day        : num  20 13 10 31 24 11 28 8 5 29 ...
$ RentalCount: num  445 40 456 38 23 42 310 240 22 360 ...
$ WeekDay    : Factor w/ 7 levels "1","2","3","4",..: 2 5 1 2 5 4 1 7 6 1 ...
$ Holiday    : Factor w/ 2 levels "0","1": 2 1 1 1 1 1 1 1 1 1 ...
$ Snow       : Factor w/ 2 levels "0","1": 1 1 1 1 1 1 1 1 1 1 ...
```

The data is now prepared for training.

## Next Steps

In part one of this tutorial series, you completed these steps:

* Import a database backup file into an Azure SQL database
* Load the data from the Azure SQL database into a data frame using R
* Prepare the data by identifying some columns as categories

To create a machine learning model that uses data from the TutorialDB database, follow part two of this tutorial series:

> [!div class="nextstepaction"]
> [Tutorial: Create a predictive model in R with Azure SQL Database Machine Learning Services (preview)](sql-database-tutorial-build-deploy-model-R-2.md)
