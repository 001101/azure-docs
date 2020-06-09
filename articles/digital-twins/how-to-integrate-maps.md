---
# Mandatory fields.
title: Using Digital Twins to update an Azure Map Indoor map
titleSuffix: Azure Digital Twins
description: See how to create an Azure function that can use the twin graph and Digital Twins notifications to update information shown in Azure Maps.
author: alexkarcher-msft
ms.author: alkarche # Microsoft employees only
ms.date: 6/3/2020
ms.topic: how-to
ms.service: digital-twins

# Optional fields. Don't forget to remove # if you need a field.
# ms.custom: can-be-multiple-comma-separated
# ms.reviewer: baanders
# manager: MSFT-alias-of-manager-or-PM-counterpart
---

# Using Digital Twins to update an Azure Map Indoor map

This article walks through the steps required to use Digital Twins data to update information displayed on an indoor map using Azure Maps. Digital Twins stores a graph of your IoT device relationships and routes telemetry to different endpoints, making it is the perfect service for updating informational overlays on maps.

This how-to will cover:

1. Configuring your Digital Twins instance to send twin update events to an Azure Function.
2. Creating an Azure Function to update an Azure Maps Indoor maps feature stateset.
3. How to store your maps ID and feature stateset ID in the Twins graph.

### Prerequisites

1. [Follow the Digital Twins end to end tutorial](./tutorial-end-to-end.md)
    1. We'll be extending this twin with an additional endpoint and route. We will also be adding another function to your Function app from that tutorial. 
2. [Create an Azure Maps Indoor map with a feature stateset.](../azure-maps/tutorial-creator-indoor-maps.md)
    1. You will need your feature stateset ID and maps subscription ID.
    1. [Feature statesets](../azure-maps/creator-indoor-maps.md#feature-statesets) are collections of dynamic properties (states) assigned to dataset features such as rooms or equipment. In the Azure Maps tutorial above, the feature stateset stores room status that we will be displaying on a map.


### Topology

:::image type="content" source="media/how-to-integrate-maps/maps-integration-topology.png" alt-text="An office map showing room 121 colored orange":::

## Create a Function to update a map using twin update notifications

We'll be first creating a route in Digital Twins to forward all twin update events to an event grid topic. Then, we'll be using an Azure Function to read those update messages and update a feature stateset in Azure Maps. 

## Create a route and filter to twin update notifications

Digital Twins instances can emit twin update events whenever a twin's state is updated. The [Digital Twins end to end sample linked above](./tutorial-end-to-end.md) walks through a scenario where a thermometer is used to update a temperature attribute attached to a room's twin. We'll be extending that solution by subscribing to update notifications for twins, and using that information to update our maps.

This pattern reads from the room twin directly, rather than the IoT device, which gives us the flexibility to change the underlying data source for temperature without needing to update our mapping logic. For example, we can add multiple thermometers or set this room to share a thermometer with another room, all without needing to update our map logic.

1. Create an event grid topic, which will receive event from our Digital Twins instance.
```azurecli
az eventgrid topic create -g <your-resource-group-name> --name <your-topic-name> -l <region>
```

2. Create an endpoint to link your event grid topic to Digital Twins.
```azurecli
az dt endpoint create eventgrid --endpoint-name <Event-Grid-endpoint-name> --eventgrid-resource-group <Event-Grid-resource-group-name> --eventgrid-topic <your-Event-Grid-topic-name> -n <your-Azure-Digital-Twins-instance-name>
```

3. Create a route in Digital Twins to send twin update events to your endpoint.

```azurecli
az dt route create -n <your-Azure-Digital-Twins-instance-name> --endpoint-name <Event-Grid-endpoint-name> --route-name <my_route> --filter "{ "endpointId": "<endpoint-ID>","filter": "type = 'Microsoft.DigitalTwins.Twin.Update'"}"
```

## Create an Azure Function to update maps

We're going to create an EventGrid triggered Function inside our function app from the [end-to-end tutorial.](./tutorial-end-to-end.md) This function will unpack those notifications and send updates to an Azure Maps feature stateset to update the temperature of one room. 

See the following document for reference info: [Azure Event Grid trigger for Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-trigger)

Replace the function code with the following code. It will filter out only updates to space twins, read the updated temperature, and send that information to Azure Maps.

```C#
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
using System;
using System.Threading.Tasks;
using System.Net.Http;

namespace SampleFunctionsApp
{
    public static class ProcessDTUpdatetoMaps
    {   //Read maps credentials from application settings on function startup
        private static string statesetID = Environment.GetEnvironmentVariable("statesetID");
        private static string subscriptionKey = Environment.GetEnvironmentVariable("subscription-key");
        private static HttpClient httpClient = new HttpClient();

        [FunctionName("ProcessDTUpdatetoMaps")]
        public static async Task Run([EventGridTrigger]EventGridEvent eventGridEvent, ILogger log)
        {
            JObject message = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
            log.LogInformation("Reading event from twinID:" + eventGridEvent.Subject.ToString() + ": " +
                eventGridEvent.EventType.ToString() + ": " + message["data"]);

            //Parse updates to "space" twins
            if (message["data"]["modelId"].ToString() == "dtmi:contosocom:DigitalTwins:Space;1")
            {   //Set the ID of the room to be updated in our map. 
                //Replace this line with your logic for retrieving featureID. 
                string featureID = "UNIT103";

                //Iterate through the properties that have changed
                foreach (var operation in message["data"]["patch"])
                {
                    if (operation["op"].ToString() == "replace" && operation["path"].ToString() == "/Temperature")
                    {   //Update the maps feature stateset
                        var postcontent = new JObject(new JProperty("States", new JArray(
                            new JObject(new JProperty("keyName", "temperature"),
                                 new JProperty("value", operation["value"].ToString()),
                                 new JProperty("eventTimestamp", DateTime.Now.ToString("s"))))));

                        var response = await httpClient.PostAsync(
                            $"https://atlas.microsoft.com/featureState/state?api-version=1.0&statesetID={statesetID}&featureID={featureID}&subscription-key={subscriptionKey}",
                            new StringContent(postcontent.ToString()));

                        log.LogInformation(await response.Content.ReadAsStringAsync());
                    }
                }
            }
        }
    }
}
```

You'll need to set two environment variables in your Function App. One for your [Azure Maps primary subscription key](../azure-maps/quick-demo-map-app.md#get-the-primary-key-for-your-account) and one for your [Azure Maps stateset ID](../azure-maps/tutorial-creator-indoor-maps.md#create-a-feature-stateset).

```azurecli-interactive
az functionapp config appsettings set --settings "subscription-key=<your-Azure-Maps-primary-subscription-key> -g <your-resource-group> -n <your-App-Service-(function-app)-name>"
az functionapp config appsettings set --settings "statesetID=<your-Azure-Maps-stateset-Id> -g <your-resource-group> -n <your-App-Service-(function-app)-name>
```

### View live updates on your map

To see live updating temperature follow the steps below:

1. Begin sending faux IoT data using the IoT device simulator in the Digital Twins end-to-end sample. [Click here for instructions](././tutorial-end-to-end.md#configure-and-run-the-simulation).
    1. run the **DeviceSimulator** project from that tutorial
2. Use [the Azure Maps Indoor module](../azure-maps/how-to-use-indoor-module.md) to render your indoor maps created in Azure Maps Creator.
    1. Copy the HTML from the indoor maps tutorial [here](../azure-maps/how-to-use-indoor-module.md#example-use-the-indoor-maps-module) to a local file.
    1. Replace the tilesetId and statesetID in the local HTML file with your values.
    1. Open that file in your browser.

Both samples send temperature in a compatible range, so you should see the color of room 121 update on the map about every 30 seconds.

:::image type="content" source="media/how-to-integrate-maps/maps-temperature-update.png" alt-text="An office map showing room 121 colored orange":::

## Next steps - Store your maps information in the Twins graph

Now that you have a hardcoded solution to updating your maps information, you can use the Digital Twins graph to store all of the information necessary for updating your indoor map. This would include the stateset ID, maps subscription ID, and feature ID of each map and location respectively. 

A solution for this specific example would involve updating each top-level space to have a stateset ID and maps subscription ID attribute, and updating each room to have a feature ID. You would need to set these values once when initializing the twin graph, then query those values for each twin update event.

Depending on the configuration of your topology, you will be able to store these three attributes at different levels correlating to the granularity of your map.

To read more about managing, upgrading, and retrieving information from the twins graph, see the following references:

1. [How-to manage a digital twin](./how-to-manage-twin.md)
2. [How-to query the twin graph](./how-to-query-graph.md)