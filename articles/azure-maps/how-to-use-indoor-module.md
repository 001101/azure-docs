---
title: Use the Azure Maps Indoor Maps module | Microsoft Azure Maps
description: Learn how to use the Microsoft Azure Maps Indoor Maps module to render maps by embedding the module's JavaScript libraries.
author: anastasia-ms
ms.author: v-stharr
ms.date: 04/20/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
---

# Use the Azure Maps Indoor Maps module

The Azure Maps Web SDK provides the *Indoor Maps* module. This module offers extended functionalities to the Azure Maps *Map Control* library. It conveniently renders indoor maps created in Creator, and it integrates the indoor map data into a web application.

## Prerequisites

To calls Azure Maps APIs, [make an Azure Maps account](quick-demo-map-app.md#create-an-account-with-azure-maps) and [obtain a primary subscription key](quick-demo-map-app.md#get-the-primary-key-for-your-account). This key may also be referred to as the primary key or the subscription key.

Obtain an Azure Maps account with Creator enabled and an indoor map created using Creator. The necessary steps are described in [make a Creator account](how-to-manage-creator.md) and [create an indoor map using Creator](tutorial-creator-indoor-maps.md). When you complete these steps, note your tile set identifier and feature state set identifier. You'll need to use these identifiers to render indoor maps with the Azure Maps Indoor Maps module.

## Embed the Indoor Maps module

You can load the Azure Maps Indoor Maps module in one of two ways.

- Use the globally hosted Azure Content Delivery Network version of the Azure Maps Indoor Maps module. Reference the Indoor Module JavaScript and Style Sheet in the `<head>` element of the HTML file:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css">
    ```

- Or, you can load the Indoor Maps module for the Azure Maps Web SDK locally by using the [azure-maps-rest](https://www.npmjs.com/package/azure-maps-rest) npm package, and then host it with your app. This package also includes TypeScript definitions. Use this command:

    > **npm install azure-maps-rest**

    Then, reference the Indoor Module JavaScript and Style Sheet in the `<head>` element of the HTML file:

     ```html
    <script src="node_modules/azure-maps-rest/dist/atlas-indoor.min.js"></script>
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css">
     ```

## Indoor Manager

To load the indoor tile sets and the map style of the tiles, you must instantiate the Indoor Manager. Instantiate the manager by providing the map object and the corresponding tileset ID. You may optionally include the `statesetId`, if you're using [dynamic map styling](). The code below shows you how to instantiate the indoor manager:

```javascript
const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "YOUR TILE SET ID HERE",
    statesetId: "YOUR STATE SET ID HERE" // Optional
});
```

If you provide a `statesetId`, you must call `indoorManager.setDynamicStyling(true)` to enable polling of the state data. Polling state data lets you dynamically update the state of the features. Recall, that we refer to feature properties as states, if the properties can be dynamically modified. For example, the color of a feature within a tile can be a state. The code below shows you how to enable state polling:

```javascript

const tilesetId = "";
const statesetId = "";

const indoorManager = new atlas.indoor.IndoorManager(map, {
    tilesetId: "YOUR TILE SET ID HERE",
    statesetId: "YOUR STATE SET ID HERE" // Optional
});

if (statesetId.length > 0) {
    indoorManager.setDynamicStyling(true);
}

```

## Indoor Level Picker Control

You can optionally initialize the level control picker via the Indoor Manager. This option allows you to change the level of the rendered map. Here's the code to initialize the level control picker:

```javascript
const levelControl = new atlas.control.LevelControl({ position: "top-right" });
indoorManager.setOptions({ levelControl });
```

## Indoor Events

The Map Control events are supported for the Indoor Maps module. Additionally, this module provides event listeners that are invoked when a level or when a facility change. The event listeners can be added to the map object, as shown in the code below. If you want to run some code, only after a level or a facility have changed, then place your code inside the event listener.

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

The `eventData` variable will hold information about the level or the facility that invoked the `levelchanged` or `facilitychanged` event, respectively. When a level changes, the `eventData` object will contain: the `facilityId`, the new `levelNumber`, and other metadata. When a facility changes, the `eventData` object will contain the: the new `facilityId`, the new `levelNumber`, and other metadata. And you can incorporate this data in your web application. For example, you can keep a log of the facilities the user visited, and at the end of the session, you can ask the user to rate their experience at each facility. You can also visualize your facility's IoT devices at each level. Use the event handlers to reset and redraw the IoT devices every time the level of the facility changes.

## Use the Indoor Maps Module

This exercise demonstrates how to integrate the Indoor Maps module with the Azure Maps Web SDK. Although the exercise is limited in scope, it covers the foundational knowledge to start using the Indoor Maps module.

1. Create a new HTML file

2. Load the Indoor Maps module and use the CDN option.

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css"
    ```

3. Initialize a map object, add style rules, and authenticate to your Azure Maps account. The HTML file should look similar to the code below. Use your primary key in the `<your Azure Maps Primary Subscription Key>` place holder. Alter the `center` option to use a latitude and longitude for your indoor map center location. Instead, if you want to use the `bounds` option, you can retrieve your map bounds by calling the [Tileset List API](). The API response returns the `bbox`, which you can parse and assign for your bounding box values. Recall, that the `bbox` is the smallest rectangular shape that encloses all the map data. You may also change the map `style` option, the code below uses the `blank` value to show the indoor map on a white background.

    ```html
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Indoor Maps App</title>

        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css" />

        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
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

          const map = new atlas.Map("map-id", {
            //use your facility's location
            center: [-122.13315, 47.63637],
            //or, you can use bounds: [ # , # , # , # ] and replace # with your map's bounds
            style: "blank",
            subscriptionKey,
            zoom: 19,
          });

        </script>
      </body>
    </html>
    ```

4. Assign the Indoor Maps `tilesetId`, and optionally add the `statesetId`. Enable polling, if you provide a state set ID. Instantiate the Indoor Manager, Indoor Level Picker Control, and add the Indoor Maps event listeners. The level control is helpful to pick and view a specific level when the map renders, if the tiles contain multiple levels. The code below is how the HTML file would look like after completing the described steps:

    ```html
    <!DOCTYPE html>
    <html lang="en">
      <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Indoor Maps App</title>

        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.css" type="text/css" />

        <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.js"></script>
        <script src="https://atlas.microsoft.com/sdk/javascript/indoor/0.1/atlas-indoor.min.js"></script>
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

5. Remember to provide your primary subscription key, tile set ID, and optionally the state set ID. Then, open your HTML file to see the indoor map rendered in the web browser. The image below shows the indoor map for a  single facility, rendered on a white background. 
     ![indoor map image](media/how-to-use-indoor-module/indoor-map-image.png)

 

## Next steps

Read about the APIs that are related to the Indoor Maps module:

> [!div class="nextstepaction"]
> [Drawing package requirements](drawing-requirements.md)

>[!div class="nextstepaction"]
> [Creator for indoor mapping](creator-for-indoor-maps.md)

Learn more about how to add more data to your map:

> [!div class="nextstepaction"]
> [Indoor Maps dynamic styling](indoor-map-dynamic-styling.md)

> [!div class="nextstepaction"]
> [Code samples](https://docs.microsoft.com/samples/browse/?products=azure-maps)