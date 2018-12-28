---
title: 'How to create user-defined functions in Azure Digital Twins | Microsoft Docs'
description: Guideline on how to create user-defined functions, matchers, and role assignments with Azure Digital Twins.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: alinast
ms.custom: seodec18
---

# How to create user-defined functions in Azure Digital Twins

[User-defined functions](./concepts-user-defined-functions.md) (UDF) enable users to configure custom logic to be executed from incoming telemetry messages and spatial graph metadata. Users can also send events to predefined [endpoints](./how-to-egress-endpoints.md).

This guide walks through an example demonstrating how to detect and alert on any reading that exceeds a certain temperature from received temperature events.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

## Client library reference

The functions that are available as helper methods in the user-defined functions runtime are listed in the [client reference](./reference-user-defined-functions-client-library.md) document.

## Create a matcher

Matchers are graph objects that determine what user-defined functions run for a given telemetry message.

- Valid matcher condition comparisons:

  - `Equals`
  - `NotEquals`
  - `Contains`

- Valid matcher condition targets:

  - `Sensor`
  - `SensorDevice`
  - `SensorSpace`

The following example matcher evaluates to true on any sensor telemetry event with `"Temperature"` as its data type value. You can create multiple matchers on a user-defined function:

```plaintext
POST YOUR_MANAGEMENT_API_URL/matchers
{
  "Name": "Temperature Matcher",
  "Conditions": [
    {
      "target": "Sensor",
      "path": "$.dataType",
      "value": "\"Temperature\"",
      "comparison": "Equals"
    }
  ],
  "SpaceId": "YOUR_SPACE_IDENTIFIER"
}
```

| Value | Replace with |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | Which server region your instance is hosted on |

## Create a user-defined function (UDF)

After the matchers are created, upload the function snippet with the following **POST** call:

> [!IMPORTANT]
> - In the headers, set the following `Content-Type: multipart/form-data; boundary="userDefinedBoundary"`.
> - The body is multipart:
>   - The first part is about metadata needed for the UDF.
>   - The second part is the JavaScript compute logic.
> - In the **USER_DEFINED_BOUNDARY** section, replace the **SpaceId** and **Machers** values.

```plaintext
POST YOUR_MANAGEMENT_API_URL/userdefinedfunctions with Content-Type: multipart/form-data; boundary="USER_DEFINED_BOUNDARY"
```

| Parameter value | Replace with |
| --- | --- |
| *USER_DEFINED_BOUNDARY* | A multipart content boundary name |

### Body

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "SpaceId": "YOUR_SPACE_IDENTIFIER",
  "Name": "User Defined Function",
  "Description": "The contents of this udf will be executed when matched against incoming telemetry.",
  "Matchers": ["YOUR_MATCHER_IDENTIFIER"]
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="userDefinedFunction.js"
Content-Type: text/javascript

function process(telemetry, executionContext) {
  // Code goes here.
}

--USER_DEFINED_BOUNDARY--
```

| Value | Replace with |
| --- | --- |
| YOUR_SPACE_IDENTIFIER | The space identifier  |
| YOUR_MATCHER_IDENTIFIER | The ID of the matcher you want to use |

### Example functions

Set the sensor telemetry reading directly for the sensor with data type **Temperature**, which is `sensor.DataType`:

```JavaScript
function process(telemetry, executionContext) {

  // Get sensor metadata
  var sensor = getSensorMetadata(telemetry.SensorId);

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Set the sensor reading as the current value for the sensor.
  setSensorValue(telemetry.SensorId, sensor.DataType, parseReading.SensorValue);
}
```

The **telemetry** parameter exposes the **SensorId** and **Message** attributes, corresponding to a message sent by a sensor. The **executionContext** parameter exposes the following attributes:

```csharp
var executionContext = new UdfExecutionContext
{
    EnqueuedTime = request.HubEnqueuedTime,
    ProcessorReceivedTime = request.ProcessorReceivedTime,
    UserDefinedFunctionId = request.UserDefinedFunctionId,
    CorrelationId = correlationId.ToString(),
};
```

In the next example, we log a message if the sensor telemetry reading surpasses a predefined threshold. If your diagnostic settings are enabled on the Azure Digital Twins instance, logs from user-defined functions are also forwarded:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Example sensor telemetry reading range is greater than 0.5
  if(parseFloat(parseReading.SensorValue) > 0.5) {
    log(`Alert: Sensor with ID: ${telemetry.SensorId} detected an anomaly!`);
  }
}
```

The following code triggers a notification if the temperature level rises above the predefined constant:

```JavaScript
function process(telemetry, executionContext) {

  // Retrieve the sensor value
  var parseReading = JSON.parse(telemetry.Message);

  // Define threshold
  var threshold = 70;

  // Trigger notification 
  if(parseInt(parseReading) > threshold) {
    var alert = {
      message: 'Temperature reading has surpassed threshold',
      sensorId: telemetry.SensorId,
      reading: parseReading
    };

    sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(alert));
  }
}
```

For a more complex UDF code sample, [check available spaces with a fresh air UDF](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availability.js).

## Create a role assignment

We need to create a role assignment for the user-defined function to run under. If we don't, it won't have the proper permissions to interact with the Management API to perform actions on graph objects. The actions that the user-defined function performs aren't exempt from the role-based access control within the Azure Digital Twins Management APIs. They can be limited in scope by specifying certain roles or certain access control paths. For more information, see [role-based access control](./security-role-based-access-control.md) documentation.

1. Query for roles and get the ID of the role you want to assign to the UDF. Pass it to **RoleId**:

    ```plaintext
    GET YOUR_MANAGEMENT_API_URL/system/roles
    ```

1. **ObjectId** will be the UDF ID that was created earlier.
1. Find the value of **Path** by querying your spaces with `fullpath`.
1. Copy the returned `spacePaths` value. You'll use that in the following code:

    ```plaintext
    GET YOUR_MANAGEMENT_API_URL/spaces?name=YOUR_SPACE_NAME&includes=fullpath
    ```

    | Parameter value | Replace with |
    | --- | --- |
    | *YOUR_SPACE_NAME* | The name of the space you wish to use |

1. Paste the returned `spacePaths` value into **Path** to create a UDF role assignment:

    ```plaintext
    POST YOUR_MANAGEMENT_API_URL/roleassignments
    {
      "roleId": "YOUR_DESIRED_ROLE_IDENTIFIER",
      "objectId": "YOUR_USER_DEFINED_FUNCTION_ID",
      "objectIdType": "YOUR_USER_DEFINED_FUNCTION_TYPE_ID",
      "path": "YOUR_ACCESS_CONTROL_PATH"
    }
    ```

    | Your value | Replace with |
    | --- | --- |
    | YOUR_DESIRED_ROLE_IDENTIFIER | The identifier for the desired role |
    | YOUR_USER_DEFINED_FUNCTION_ID | The ID for the UDF you want to use |
    | YOUR_USER_DEFINED_FUNCTION_TYPE_ID | The ID specifying the UDF type |
    | YOUR_ACCESS_CONTROL_PATH | The access control path |

   

## Send telemetry to be processed

Telemetry generated by the sensor described in the graph triggers the run of the user-defined function that was uploaded. The data processor picks up the telemetry. Then a run plan is created for the invocation of the user-defined function.

1. Retrieve the matchers for the sensor the reading was generated off of.
1. Depending on what matchers evaluated successfully, retrieve the associated user-defined functions.
1. Run each user-defined function.

## Next steps

- Learn how to [create Azure Digital Twins endpoints](./how-to-egress-endpoints.md) to send events to.
- For more details about routing in Azure Digital Twins, read [Routing events and messages](./concepts-events-routing.md).
- Review the [client library reference documentation](./reference-user-defined-functions-client-library).
