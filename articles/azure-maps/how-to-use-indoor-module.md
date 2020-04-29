---
title: Use the Azure Maps Indoor Maps module | Microsoft Azure Maps
description: Learn how to use the Microsoft Azure Maps Indoor Maps module to render maps by embedding the module's JavaScript libraries.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/22/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
---

# Use the Azure Maps Indoor Maps module

The Azure Maps Web SDK includes the *Indoor Maps* module. The  *Indoor Maps* module allows you to render indoor maps created in Azure Maps Creator.

## Prerequisites

1. [Make an Azure Maps account](quick-demo-map-app.md#create-an-account-with-azure-maps)
2. [Enable Creator](how-to-manage-creator.md)
3. [Obtain a primary subscription key](quick-demo-map-app.md#get-the-primary-key-for-your-account), also known as the primary key or the subscription key.
4. Get a `tilesetId` and a `statesetId` by completing the [tutorial for creating Indoor maps](tutorial-creator-indoor-maps.md).
 You'll need to use these identifiers to render indoor maps with the Azure Maps Indoor Maps module.

## Embed the Indoor Maps module

You can install and embed the Azure Maps *Indoor Maps* module in one of two ways.

To use the globally hosted Azure Content Delivery Network version of the Azure Maps *Indoor Maps* module, reference the Indoor Module JavaScript and Style Sheet in the `<head>` element of the HTML file:

  ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
    <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css">
  ```

 Or, you can download the *Azure Maps Services* module. The *Azure Maps Services* module contains a client library for accessing Azure Maps services. Follow the steps below to install and load the *Indoor Maps* module into your web application.  
  
  1. Install the NPM package. Make sure you use administrator privileges in the console:

      ```powershell
        >npm install azure-maps-control
        >npm install azure-maps-indoor
      ```

  2. Reference the *Indoor Maps* module JavaScript and Style Sheet in the `<head>` element of the HTML file:

      ```html
      <script src="node_modules/azure-maps-rest/dist/atlas-indoor.min.js"></script>
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
      <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css">

        ```

## Instantiate the Map object

The first thing you must do is create a *map object*. The *map object* will be used in the next step to instantiate the *Indoor Manager* object.  The code below shows you how to instantiate the *map object*:

```javascript
  const subscriptionKey = "<your Azure Maps Primary Subscription Key>";

  const map = new atlas.Map("map-id", {
    //use your facility's location
    center: [-122.13315, 47.63637],
    //or, you can use bounds: [ # , # , # , # ] and replace # with your map's bounds
    style: "blank",
    subscriptionKey,
    zoom: 19,
  });
```

## Instantiate the Indoor Manager

To load the indoor tilesets and map style of the tiles, you must instantiate the *Indoor Manager*. Instantiate the *Indoor Manager* by providing the *map object* and the corresponding tilesetID. You may optionally include the `statesetId`, if you're using [dynamic map styling](indoor-map-dynamic-styling.md). The code below shows you how to instantiate the indoor manager:

```javascript
const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "<tilesetId>",
    statesetId: "<statesetId>" // Optional
});
```

To enable polling of state data you provide, you must provide the `statesetId` and call `indoorManager.setDynamicStyling(true)`. Polling state data lets you dynamically update the state of dynamic properties or *states*. For example, a feature, such as room, within a tile can have a dynamic property (*state*) called `occupancy`. Your application may wish to poll for any *state* changes to reflect the change inside the visual map. The code below shows you how to enable state polling:

```javascript

const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "<tilesetId>",
    statesetId: "<statesetId>" // Optional
});

if (statesetId.length > 0) {
    indoorManager.setDynamicStyling(true);
}

```

## Indoor Level Picker Control

 The *Indoor Level Picker* control allows you to change the level of the rendered map. You can optionally initialize the *Indoor Level Picker* control via the *Indoor Manager*. Here's the code to initialize the level control picker:

```javascript
const levelControl = new atlas.control.LevelControl({ position: "top-right" });
indoorManager.setOptions({ levelControl });
```

## Indoor Events

 The *Indoor Maps* module supports *map object* events. The *map object* event listeners are invoked when a level or facility has changed. If you want to run code when a level or a facility have changed, place your code inside the event listener. The code below shows how event listeners can be added to the *map object*.

```javascript
map.events.add("levelchanged", indoorManager, (eventData) => {

    //code that you want to run after a level has been changed
    console.log("The level has changed: ", eventData);

});
map.events.add("facilitychanged", indoorManager, (eventData) => {

    //code that you want to run after a facility has been changed
    console.log("The facility has changed: ", eventData);
});
```

The `eventData` variable holds information about the level or facility that invoked the `levelchanged` or `facilitychanged` event, respectively. When a level changes, the `eventData` object will contain the `facilityId`, the new `levelNumber`, and other metadata. When a facility changes, the `eventData` object will contain the new `facilityId`, the new `levelNumber`, and other metadata.

You can incorporate this data in your web application. For example, you can keep a log of the facilities the user has visited. At the end of a session, you can ask the user to rate their experience at each facility. You can also visualize your facility's IoT devices at each level. Use the event handlers to reset and redraw the IoT devices whenever the level of the facility changes.

## Example: Use the Indoor Maps Module

This example shows you how to use the *Indoor Maps* module in your web application. Although the example is limited in scope, it covers the basics of what you need to get started using the *Indoor Maps* module. The complete HTML code is below these steps.

1. Use the Azure Content Delivery Network [option](#embed-the-indoor-maps-module) to install the *Indoor Maps* module.

2. Create a new HTML file

3. In the HTML header, reference the *Indoor Maps* module JavaScript and style sheet styles.

4. Initialize a *map object*. The *map object* supports the following options:
    - `Subscription key` is your Azure Maps primary subscription key.
    - `center` defines a latitude and longitude for your indoor map center location. Provide a value for `center` if you do not want to provide a value for `bounds`. Format should appear as `center`: [-122.13315, 47.63637].
    - `bounds` is the smallest rectangular shape that encloses the tileset map data. Set a value for `bounds` if you do not want to set a value for `center`. You can find your map bounds by calling the [Tileset List API](https://docs.microsoft.com/rest/api/maps/tileset/listpreview). The Tileset List API returns the `bbox`, which you can parse and assign to `bounds`. Format should appear as `bounds`: [ # , # , # , # ].
    - `style` allows you to set the color of the background. To display a white background, define `style` as "blank".
    - `zoom` allows you to specify the min and max zoom levels for your map.

5. Next, create the *Indoor Manager* module. Assign the *Indoor Maps* `tilesetId`, and optionally add the `statesetId`.

6. Instantiate the *Indoor Level Picker* control.

7. Add *map object* event listeners.  

Your file should now look similar to the HTML below.

  ```html
  <!DOCTYPE html>
  <html lang="en">
    <head>
      <meta charset="utf-8" />
      <meta name="viewport" content="width=device-width, user-scalable=no" />
      <title>Indoor Maps App</title>
       <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css">
      <style>
        html,
        body {
          width: 100%;
          height: 100%;
          padding: 0;
          margin: 0;
        }

        #map-id {
          width: 100%;
          height: 100%;
        }
      </style>
    </head>

    <body>
      <div id="map-id"></div>
      <script>
        const subscriptionKey = "<your Azure Maps Primary Subscription Key>";
        const tilesetId = "<your tile set id>";
        const statesetId = "<your state set id>";

        const map = new atlas.Map("map-id", {
          //use your facility's location
          center: [-122.13315, 47.63637],
          //or, you can use bounds: [ # , # , # , # ] and replace # with your Map bounds
          style: "blank",
          subscriptionKey,
          zoom: 19,
        });

        const levelControl = new atlas.control.LevelControl({
          position: "top-right",
        });

        const indoorManager = new atlas.indoor.IndoorManager(map, {
          levelControl, //level picker
          tilesetId,
          statesetId, //optional
        });

        if (statesetId.length > 0) {
          indoorManager.setDynamicStyling(true);
        }

        map.events.add("levelchanged", indoorManager, (eventData) => {
          //put code that runs after a level has been changed
          console.log("The level has changed:", eventData);
        });

        map.events.add("facilitychanged", indoorManager, (eventData) => {
          //put code that runs after a facility has been changed
          console.log("The facility has changed:", eventData);
        });
      </script>
    </body>
  </html>
  ```

To see your indoor map, load it into a web browser. It should appear like the image below. If you click on the stairwell feature, the *level picker* will appear in the upper right-hand corner.

  ![indoor map image](media/how-to-use-indoor-module/indoor-map-image.png)

## Next steps

Read about the APIs that are related to the *Indoor Maps* module:

> [!div class="nextstepaction"]
> [Drawing package requirements](drawing-requirements.md)

>[!div class="nextstepaction"]
> [Creator for indoor mapping](creator-for-indoor-maps.md)

Learn more about how to add more data to your map:

> [!div class="nextstepaction"]
> [Indoor Maps dynamic styling](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Code samples](https://docs.microsoft.com/samples/browse/?products=azure-maps)