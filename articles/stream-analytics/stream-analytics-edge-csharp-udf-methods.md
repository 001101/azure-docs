---
title: Develop C# user defined functions for Azure Stream Analytics Edge jobs (Preview)
description: Learn how to write c# user defined functions for Stream Analytics Edge jobs.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 09/24/2018
---

# Develop C# user-defined functions for Azure Stream Analytics Edge jobs (Preview)
With C# user-defined functions (UDF), you can use your own functions to extend the Azure Stream Analytics query language. UDF allows you to reuse existing code, implement custom logic to manipulate complex strings, and run mathematical or complex logic. 

## Overview
Visual Studio tools for Azure Stream Analytics make it easy for you to write C# UDF, test your jobs locally (even offline), and publish your job to Azure. Once published to Azure, you can deploy your job to IoT devices using IoT Hub.

There are three ways to implement UDF:

* CodeBehind files in an ASA project
* UDF from a local C# project
* An existing package from a storage account

## CodeBehind
You can write user-defined functions in the **Script.asql** CodeBehind. Visual Studio tools will automatically compile the CodeBehind file into an assembly file. The assemblies are packaged as a zip file and uploaded to your storage account when you submit your job to Azure. You can learn how to write a C# UDF using CodeBehind by following [this tutorial](stream-analytics-edge-csharp-udf.md). 

## Local C# project
User-defined functions can be written in a C# assembly and then referenced in an Azure Stream Analytics query. This is the recommended option for complex functions that require the full power of C# beyond its expression language, such as procedural logic or recursion. You might also use a project reference if you need to share the function logic across several Azure Stream Analytics queries.

To reference a local C# project:

1.	Create a new C# class library in your solution.
2.	Write the code in your class. Remember that the classes must be defined as *public* and objects must be defined as *static public*. 
3.	Build your project.
4.	Reference the new class in your Azure Stream Analytics project.
5.	Add a new function in your Azure Stream Analytics project.

### Example

In this example, **UDFTest** is a C# class library project and **ASAEdgeUDFDemo** is the Azure Stream Analytics Edge project, which will reference **UDFTest**. 

   ![ASA Edge project](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-demo.png)

First, you must build your C# project, which will enable you to add a reference to your C# UDF from the Azure Stream Analytics query.

   ![Build ASA Edge project](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-build-project.png)

Then add the reference to the C# project in the ASA edge project. Right-click the References node and choose Add Reference.

   ![Add reference to C# project](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-reference.png)

Choose the C# project name from the list. 

   ![Choose C# project name from list](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-choose-project-name.png)

You will then see the **UDFTest** listed under **References** in **Solution Explorer**.

   ![Edge solution explorer](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-added-reference.png)

Right click on the **Functions** folder and choose **New Item**.

   ![Add new item](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function.png)

Add a C# function **SquareFunction.json** to your Azure Stream Analytics project.

   ![Add C sharp function](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-add-csharp-function-2.png)

Double-click the function in **Solution Explorer** to open the configuration dialog.

   ![C sharp function configuration](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-csharp-function-config.png)

In the C# function configuration, choose **Load from ASA Project Reference** and the related assembly, class, and method names from the dropdown list. To refer to the methods, types, and functions in the Edge query, the classes must be defined as *public* and the objects must be defined as *static public*.

   ![Stream Analytics C sharp function configuration](./media/stream-analytics-edge-csharp-udf-methods/stream-analytics-edge-udf-asa-csharp-function-config.png)

## Existing packages
Once assembly zip packages have been uploaded to your storage account, you can use the functions in Azure Stream Analytics queries. All you need to do is include the storage information in the Edge job configuration. You will not be able to test the function locally with this option because Visual Studio tools will not download your package. The package path is parsed directly to the service. 

### Package format

The format of the package has the path `/UserCustomCode/CLR/*`. User DLLs and resources are copied under the  , which helps isolate user DLLs from system and Azure Stream Analytics DLLs. 

## Supported types and mapping

 |**UDF type (C#)**  |**Azure Stream Analytics type**  |
 |---------|---------|
 |Bool  |  bigint   |
 |int32  |  bigint   |
 |int64  |  bigint   |
 |float  |  double   |
 |double  |  double   |
 |string  |  nvarchar(max)   |
 |dateTime  |  dateTime   |
 |struct  |  IRecord   |
 |object  |  IRecord   |
 |Array  |  IArray   |
 |dictionary<string, object>  |  IRecord   |

## Limitations
The UDF preview currently has the following limitations:

* C# can only be used for Azure Stream Analytics on IoT Edge. For cloud jobs, you can write JavaScript user-defined functions. To learn more, visit the [Azure Stream Analytics JavaScript UDF](stream-analytics-javascript-user-defined-functions.md) tutorial.

* West Central US is the only enabled region for deployment of the ASA job. Your IoT Hub can be in any region.

* The Azure portal query editor shows an error when using UDF C# functions in the portal. 

* Because the custom code shares context with Azure Stream Analytics engine, custom code cannot reference anything that has a conflicting namespace/dll_name with Azure Stream Analytics code. For example, you cannot reference *Newtonsoft Json*.

## Next steps
