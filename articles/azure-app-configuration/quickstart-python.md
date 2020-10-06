---
title: Quickstart for using Azure App Configuration with Python apps | Microsoft Docs
description: In this quickstart, use Python with Azure App Configuration to centralize storage and management of application settings separate from your code.
services: azure-app-configuration
author: drewbatgit
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-python
ms.date: 9/17/2020
ms.author: drewbat

#Customer intent: As a Python developer, I want to manage all my app settings in one place.
---
# Quickstart: Create a Python app with Azure App Configuration

In this quickstart, you will use Azure App Configuration to centralize storage and management of application settings using the [Azure App Configuration client library for Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).

## Prerequisites

- Azure subscription - [create one for free](https://azure.microsoft.com/free/)
- Python 2.7, or 3.5 or later - For information on setting up Python on Windows, see the [Python on Windows documentation]( https://docs.microsoft.com/windows/python/)

## Create an App Configuration store

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Select **Configuration Explorer** > **Create** > **Key-value** to add the following key-value pairs:

    | Key | Value |
    |---|---|
    | TestApp:Settings:Message | Data from Azure App Configuration |

    Leave **Label** and **Content Type** empty for now.

8. Select **Apply**.



## Setting up

1. In this tutorial, you'll create a new directory for the project named *app-configuration-quickstart*.

    ```console
    mkdir app-configuration-quickstart
    ```

1. Switch to the newly created *app-configuration-quickstart* directory.

    ```console
    cd app-configuration-quickstart
    ```

1. Install the Azure App Configuration client library by using the `pip install` command.

    ```console
    pip install azure-appconfiguration
    ```

1. Create a new file called *app-configuration-quickstart.py* in the *app-configuration-quickstart* directory and add the following code:

    ```python
    import os
    from azure.appconfiguration import AzureAppConfigurationClient, ConfigurationSetting
    
    try:
        print("Azure App Configuration - Python Quickstart")
        # Quickstart code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```





## Configure your App Configuration connection string


1. Set an environment variable named **AZURE_APP_CONFIG_CONNECTION_STRING**, and set it to the access key to your App Configuration store. At the command line, run the following command:

    ```cmd
    setx AZURE_APP_CONFIG_CONNECTION_STRING "connection-string-of-your-app-configuration-store"
    ```

    If you use Windows PowerShell, run the following command:

    ```azurepowershell
    $Env:AZURE_APP_CONFIG_CONNECTION_STRING = "connection-string-of-your-app-configuration-store"
    ```

    If you use macOS or Linux, run the following command:

    ```console
    export AZURE_APP_CONFIG_CONNECTION_STRING='connection-string-of-your-app-configuration-store'
    ```

2. Restart the command prompt to allow the change to take effect. Print out the value of the environment variable to validate that it is set properly.

## Code samples

The sample code snippets in this section show you how to perform common operations with the Azure App Configuration client library for Python. Add these snippets to the `try` block in *app-configuration-quickstart.py* file you created earlier.

* [Connect to an App Configuration store](#connect-to-an-app-configuration-store)
* [Get a configuration setting](#get-a-configuration-setting)
* [Set a configuration setting](#set-a-configuration-setting)
* [Get a list of configuration settings](#get-a-list-of-configuration-settings)
* [Lock a configuration setting](#lock-a-configuration-setting)
* [Update a configuration setting](#update-a-configuration-setting)
* [Delete a configuration setting](#delete-a-configuration-setting)


### Connect to an App Configuration store

The following code snippet creates an instance of **AzureAppConfigurationClient** using the connection string stored in your environment variables.

```python
    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')
    app_config_client = AzureAppConfigurationClient.from_connection_string(connection_string)
```

### Get a configuration setting

The following code snippet retrieves the configuration setting by `key` name.

```python
    fetched_config_setting = client.get_configuration_setting(key = 'TestApp:Settings:Message')
    print("Retrieved configuration setting:")
    print("Key: " + fetched_config_setting.key + ", Value: " + fetched_config_setting.value)
```


### Add a configuration setting

Previously in this quickstart, you added a configuration key and value using the Azure portal. You can also add configuration settings using the Python client library. The following code snippet initialzies a **ConfigurationSetting** object with a key, value, and a label which can be used to categorize settings. Next, the configuration setting is added to the remote store by calling **add_configuration_setting** and passing in the **ConfigruationSetting** object. Add this code to the end of the `try` block.

```python
    config_setting = ConfigurationSetting(
        key='TestApp:Settings:Message',
        label='TestAppLabel',
        value='Value from labeled key'
    )

    added_config_setting = client.add_configuration_setting(config_setting)
```

Note that attempting to call **add_configuration_setting** with a key and label pair that already exist will throw an exception.

### Get a list of configuration settings

The following code snippet retrieves a list of configuration settings. The `key_filter` and `label_filter` arguments can be provided to filter key-values based on `key` and 'label` respectively. For more information on filtering, see how to [query configuration settings](./concept-key-value.md#query-key-values).

```python
    filtered_settings_list = client.list_configuration_settings( key_filter="TestApp*")
    print("Retrieved configuration settings:")
    for item in filtered_listed:
        print("Key: " + item.key + ", Value: " + item.value)
```

### Lock a configuration setting

The following code snippet shows how to lock a configuration setting by calling the `set_read_only` method and providing the `ConfigurationSetting` object to be locked.

```python
    locked_config_setting = client.set_read_only(config_setting)
    print("Read-only status: ", locked_config_setting.read_only)

    # Update the value of the ConfigurationSetting object
    config_setting.value = "Updated value"

    try:
        client.set_configuration_setting(config_setting)
    except Exception as ex:
        print('Exception:')
        print(ex)

    unlocked_config_setting = client.set_read_only(config_setting, False)
    print("Read-only status: ", unlocked_config_setting.read_only)
```

Settings that are locked with the Python client API are also locked in the Azure portal, as indicated by the lock icon next to the setting's value in the **Configuration explorer**.

![Select Create](media/quickstarts/azure-portal-app-configuration-locked-setting.png)

Unlock a setting in the portal by clicking the elipses at the end of the setting's row and selecting **Unlock**. 

![Select Create](media/quickstarts/azure-portal-app-configuration-unlocked-setting.png)

### Update a configuration setting

The following code snippet updates an existing setting by calling **set_configuration_settings** and passing in the **ConfigurationSetting** object for which the value was updated in the previous step. You can use **set_configuration_settings** to update an existing setting or create a new setting. Add this code to the end of the `try` block.

```python
    updated_config_setting = client.set_configuration_setting(config_setting)
    
    filtered_settings_list = client.list_configuration_settings( key_filter="TestApp*")
    print("Key list:")
    for item in filtered_settings_list:
        print("   key: ", item.key, " label: ", item.label, "value: ", item.value)
```

### Delete a configuration setting

The following code snippet deletes a configuration setting by calling the **delete_configuration_settings** method.

```python
    deleted_config_setting = client.delete_configuration_setting(
        key="TestApp:Settings:Message", label="TestAppLabel"
    )
    
    filtered_settings_list = client.list_configuration_settings( key_filter="TestApp*")
    print("Key list:")
    for item in filtered_settings_list:
        print("   key: ", item.key, " label: ", item.label, "value: ", item.value)
```

## Run the app

This app retrieves an configuration setting created through the Azure portal. Next, using the Azure App Configuration client library for Python, the app creates a new setting, retrieves a list of existing settings, locks and unlocks a setting, updates a setting, and finally deletes a setting.

The following is the full code listing.

```python
import os
from  azure.appconfiguration import *


try:
    print("Azure App Configuration - Python quickstart sample")
    # Quick start code goes here

    # Retrieve the connection string for use with the application. The app configuration
    # connection string is stored in an environment variable on the machine
    # running the application called AZURE_APP_CONFIG_CONNECTION_STRING. If the
    # environment variable is created after the application is launched in a
    # console or with Visual Studio, the shell or application needs to be
    # closed and reloaded to take the environment variable into account.
    connection_string = os.getenv('AZURE_APP_CONFIG_CONNECTION_STRING')

    client = AzureAppConfigurationClient.from_connection_string(connection_string)

    fetched_config_setting = client.get_configuration_setting(key = 'TestApp:Settings:Message')
    print(
        "Fetched key: ", fetched_config_setting.key,
        " value: ", fetched_config_setting.value
    )

    config_setting = ConfigurationSetting(
        key='TestApp:Settings:Message',
        label='TestAppLabel',
        value='Value from labeled key'
    )

    added_config_setting = client.add_configuration_setting(config_setting)

    filtered_settings_list = client.list_configuration_settings( key_filter="TestApp*")
    print("Key list:")
    for item in filtered_settings_list:
        print("   key: ", item.key, " label: ", item.label, "value: ", item.value)

    locked_config_setting = client.set_read_only(config_setting)
    print("Read-only status: ", locked_config_setting.read_only)

    try:
        config_setting.value = "Updated value"
        client.set_configuration_setting(config_setting)
    except Exception as ex:
        print('Exception:')
        print(ex)

    unlocked_config_setting = client.set_read_only(config_setting, False)
    print("Read-only status: ", unlocked_config_setting.read_only)

    updated_config_setting = client.set_configuration_setting(config_setting)
    
    filtered_settings_list = client.list_configuration_settings( key_filter="TestApp*")
    print("Key list:")
    for item in filtered_settings_list:
        print("   key: ", item.key, " label: ", item.label, "value: ", item.value)


    deleted_config_setting = client.delete_configuration_setting(
        key="TestApp:Settings:Message", label="TestAppLabel"
    )
    
    filtered_settings_list = client.list_configuration_settings( key_filter="TestApp*")
    print("Key list:")
    for item in filtered_settings_list:
        print("   key: ", item.key, " label: ", item.label, "value: ", item.value)

except Exception as ex:
    print('Exception:')
    print(ex)
```

In your console window, navigate to the directory containing the *app-configuration-quickstart.py* file, then execute the following `python` command to run the app.

```console
python app-configuration-quickstart.py
```

The output of the app is similar to the following example:

```output
Azure App Configuration - Python quickstart sample
Fetched key:  TestApp:Settings:Message  value:  Data from Azure App Configuration
Key list:
   key:  TestApp:Settings:Message  label:  None value:  Data from Azure App Configuration
   key:  TestApp:Settings:Message  label:  TestAppLabel value:  Value from labeled key
Read-only status:  True
Exception:
Operation returned an invalid status 'Conflict'
Read-only status:  False
Key list:
   key:  TestApp:Settings:Message  label:  None value:  Data from Azure App Configuration
   key:  TestApp:Settings:Message  label:  TestAppLabel value:  Updated value
Key list:
   key:  TestApp:Settings:Message  label:  None value:  Data from Azure App Configuratione
```
## Clean up resources

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## Next steps

In this quickstart, you created a new App Configuration store and accessed it from Python. 

For tutorials, samples, quick starts and other documentation, visit:

> [!div class="nextstepaction"]
> [Azure for Python Developers](https://docs.microsoft.com/azure/python/)

* To learn more, see the [Azure App Configuration client library for Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/appconfiguration/azure-appconfiguration).
