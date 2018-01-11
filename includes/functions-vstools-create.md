The Azure Functions project template in Visual Studio creates a project that can be published to a function app in Azure. A function app lets you group functions as a logical unit for easier management, deployment, and sharing of resources.   

1. In Visual Studio, select **New** > **Project** from the **File** menu. 

2. In the **New Project** dialog, expand **Installed** > **Visual C#** > **Cloud**, select **Azure Functions**, type a **Name** for your project, and click **OK**. The function app name must be valid as a C# namespace, so don't use underscores, hyphens, or any other nonalphanumeric characters. 

    ![New project dialog to create a function in Visual Studio](./media/functions-vstools-create/functions-vstools-add-new-project.png) 

2. Use the settings as specified in the table below the image when creating the function app project and function.
 
    ![New function dialog in Visual Studio](./media/functions-vstools-create/functions-vstools-add-new-function.png) 

    | Setting      | Suggested value  | Description                      |
    | ------------ |  ------- |----------------------------------------- |
    | Version | Azure Functions v1 <br />(.NET Framework) | This creates a function app that uses the version 1 runtime of Azure Functions. The version 2 runtime, which supports .NET Core, is currently in preview. For more information, see [How to target Azure Functions runtime version](../articles/azure-functions/functions-versions.md).   | 
    | Template | HTTP trigger | This creates a function triggered by an HTTP request. |
    | **Storage account**  | Storage Emulator | An HTTP trigger doesn't use the Storage account connection. All other trigger types require a valid Storage account connection string. |
    | **Access rights** | Anonymous | The created function can be triggered by any client without providing a key. This authorization setting makes it easy to test your new function. Function key values can be found in the [Azure portal](https://portal.azure.com).|

    If you don't see **New Template** dialog box, you may need to update your installed version of Azure Functions and WebJobs Tools. Do this under **Updates** > **Visual Studio Marketplace** in **Extensions and Updates**.   
    
