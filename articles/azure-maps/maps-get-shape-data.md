---
title: Get shapes data from drawing manager in Azure Maps| Microsoft Docs
description: How to get shape data using Azure Maps Web SDK
author: walsehgal
ms.author: v-musehg
ms.date: 08/22/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
---

# Get shape data

This article shows how to get shapes data that have been drawn on the map using the [drawing manager](https://docs.microsoft.com/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager?view=azure-node-latest#getsource--) **drawingManager.getSource()** function. The can be various scenarios where you might want to extract geojson data of a drawn shape and utilize it elsewhere.  


## Get data from drawn shape data

The following function gets the drawn shapes source and outputs it on the screen. 

```Javascript
function getDrawnShapes() {
    var source = drawingManager.getSource();

    document.getElementById('CodeOutput').value = JSON.stringify(source.toJson(), null, '    ');
}
```

Below is the complete running code sample, where you can draw a shape to test the functionality:

<iframe height="700" title="Get shape data" src="//codepen.io/azuremaps/embed/xxKgBVz/?height=265&theme-id=0&default-tab=result" frameborder="no" allowtransparency="true" allowfullscreen="true" style='width: 100%;'>See the Pen <a href='https://codepen.io/azuremaps/pen/xxKgBVz/'>Get shape data</a> by Azure Maps
  (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>
