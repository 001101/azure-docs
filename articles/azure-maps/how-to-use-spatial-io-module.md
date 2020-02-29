---
title: How to use the Azure Maps spatial IO module | Microsoft Azure Maps
description: Learn how to use the Spatial IO module provided by the Azure Maps Web SDK. This module provides robust features to make it easy for developers to integrate spatial data with the Azure Maps web sdk.
author: farah-alyasari
ms.author: v-faalya
ms.date: 02/28/2020
ms.topic: concepts
ms.service: azure-maps
services: azure-maps
manager: philmea
---


# How to use the Azure Maps Spatial IO module

The Azure Maps Web SDK provides a **Spatial IO module**. The  Spatial IO module makes it easy to integrate spatial data with the Azure Maps web SDK using JavaScript or TypeScript. This module provides robust features, which allow developers to:

- Read and write common spatial files. Supported file formats include: KML, KMZ, GPX, GeoRSS, GML, and CSV files containing columns with spatial information.
- Connect to Open Geospatial Consortium (OGC) services and easily integrate with Azure Maps web SDK.
- Overlay Web Mapping Services (WMS) and Web Map Tile Services (WMTS) as layers on the map.
- Query data in a Web Feature Service (WFS).
- Overlay complex data sets that contain style information and have them render automatically.
- Leverage high-speed XML and delimited file reader and writer classes.

In this guide, we'll learn how to install the Spatial IO module and use it in a web application.

## Prerequisites

Before you can use the Spatial IO module, you'll need to [make an Azure Maps account](https://docs.microsoft.com/en-us/azure/azure-maps/quick-demo-map-app#create-an-account-with-azure-maps) and [get the primary subscription key for your account](https://docs.microsoft.com/en-us/azure/azure-maps/quick-demo-map-app#get-the-primary-key-for-your-account).

## Use the Spatial IO module

Let's now walk through the processes of installing the spatial IO module. We'll use the Spatial IO module to read the XML file from a URL, then we'll load the features data from the XML file to the map.

1. Create a new HTML file.

2. Load the Azure Maps Web SDK and initialize the map control. See the [Azure Maps map control](https://docs.microsoft.com/en-us/azure/azure-maps/how-to-use-map-control) guide for the details. Once you're done with this step, your HTML file should look like this:

```html
<!DOCTYPE html>
<html>

<head>
    <title></title>

    <meta charset="utf-8">

    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

    <script type='text/javascript'>

        var map;
  
        function GetMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function() {

                // Write your code here to make sure it runs once the map resources are ready

            });
        }
    </script>
</head>

<body onload="GetMap()">
    <div id="myMap"></div>
</body>

</html>
```

2. Load the Azure Maps spatial IO module. You can load it using one of the two options:

    a. Use the globally hosted, Azure Content Delivery Network of the Azure Maps spatial IO module. Add a reference to the JavaScript file in the `<head>` element:

    ```html
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>
    ```

    b. Load the spatial IO module for the Azure Maps Web SDK source code locally using the [azure-maps-spatial-io](https://www.npmjs.com/package/azure-maps-spatial-io) npm package, and then host it with your app. This package also includes TypeScript definitions. Use the following command to install the package:

    ```sh
    npm install azure-maps-spatial-io
    ```

    Then, add a reference to the JavaScript in the `<head>` element of the HTML document:

    ```html
    <script src="node_modules/azure-maps-spatial-io/dist/atlas-spatial.min.js"></script>
    ```

3. Initialize a `datasource`, and add the data source variable to the map. Initialize a `layer`, and add the data source to the map layer. Then, render both the data source and the layer. Before you scroll down to see the full code, think about the best places to put the data source and layer code snippets. Recall that, before we programmatically manipulate the map, we wait until the map resource are ready. For readability, we define our variables near the top.

```javascript
	var datasource, layer;
```

and

```javascript
    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Add a simple data layer for rendering the data.
    layer = new atlas.layer.SimpleDataLayer(datasource);
    map.layers.add(layer);
```

4. Putting it all together, your HTML file should look like the following code. This code demonstrates how to read an XML file from a URL. Then, load and display the file's feature data on the map. 

```html
<!DOCTYPE html>
<html>

<head>
    <title>Spatial IO Module Example</title>

    <meta charset="utf-8">

    <!-- Ensures that IE and Edge uses the latest version and doesn't emulate an older version -->
    <meta http-equiv="x-ua-compatible" content="IE=Edge">

    <!-- Ensures the web page looks good on all screen sizes. -->
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

    <!-- Add references to the Azure Maps Map control JavaScript and CSS files. -->
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.min.css" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/javascript/mapcontrol/2/atlas.js"></script>

    <!-- Add reference to the Azure Maps Spatial IO module. -->
    <script src="https://atlas.microsoft.com/sdk/javascript/spatial/0/atlas-spatial.js"></script>

    <script type='text/javascript'>
        var map, datasource, layer;

        function GetMap() {
            //Initialize a map instance.
            map = new atlas.Map('myMap', {
                view: 'Auto',

                //Add your Azure Maps subscription key to the map SDK. Get an Azure Maps key at https://azure.com/maps
                authOptions: {
                    authType: 'subscriptionKey',
                    subscriptionKey: '<Your Azure Maps Key>'
                }
            });

            //Wait until the map resources are ready.
            map.events.add('ready', function() {

                //Create a data source and add it to the map.
                datasource = new atlas.source.DataSource();
                map.sources.add(datasource);

                //Add a simple data layer for rendering the data.
                layer = new atlas.layer.SimpleDataLayer(datasource);
                map.layers.add(layer);

                //Read an XML file from a URL or pass in a raw XML string.
                atlas.io.read('superCoolKmlFile.xml').then(r => {
                    if (r) {
                        //Add the feature data to the data source.
                        datasource.add(r);

                        //If bounding box information is known for data, set the map view to it.
                        if (r.bbox) {
                            map.setCamera({
                                bounds: r.bbox,
                                padding: 50
                            });
                        }
                    }
                });
            });
        }
    </script>
</head>

<body onload="GetMap()">
    <div id="myMap"></div>
</body>

</html>
```

5. Remember to replace `<Your Azure Maps Key>` with your primary key. Then, open your HTML file. You'll see results similar to the following demo:

<br/>

<iframe height='500' scrolling='no' title='Spatial Data Example' src='//codepen.io/azuremaps/embed/MWwojKV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/azuremaps/pen/MWwojKV/'>Spatial Data Example</a> by Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

The feature we demonstrated here is only one of the many features available in the Spatial IO module. Read the guides in the next steps section to learn how to use other functionalities in the Spatial IO module.

## Next steps

See the following articles to learn more of the functionalities in the spatial IO module:

> [!div class="nextstepaction"]
> [Add a simple data layer](spatial-io-add-simple-data-layer.md)

> [!div class="nextstepaction"]
> [Read and write spatial data](spatial-io-read-write-spatial-data.md)

> [!div class="nextstepaction"]
> [Add an OGC map layer](spatial-io-add-ogc-map-layer.md)

> [!div class="nextstepaction"]
> [Connect to a WFS service](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [Leverage core operations](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [Supported data format details](spatial-io-supported-data-format-details.md)
