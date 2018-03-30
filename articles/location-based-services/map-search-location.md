---
title: How to show search results on the Azure Location Based Services' map | Microsoft Docs
description: Show search results
services: location-based-services
keywords: 

author: dsk-2015
ms.author: shubhaj
ms.date: 03/01/2018
ms.topic: tutorial
ms.service: location-based-services

documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
---

# Show search results on the map

## Overview
This tutorial shows you how to perform a search request and render the search results on the map. 

## Understand the code

<iframe height='523' scrolling='no' title='Show search results on a map' src='//codepen.io/azuremaps/embed/KQbaeM/?height=523&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>See the Pen <a href='https://codepen.io/azuremaps/pen/KQbaeM/'>Show search results on a map</a> by Azure LBS (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) on <a href='https://codepen.io'>CodePen</a>.
</iframe>

In the code above, the first block of code constructs a map object. You can see [create a map](./map-create.md) for instructions.

The second block of code creates and adds a layer of search pins on the map. You can see [add a pin on the map](./map-add-pin.md) for instructions.

The third block of code sends an [XMLHttpRequest](https://xhr.spec.whatwg.org/) to [Azure Maps Fuzzy Search API](https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchfuzzy).

The last block of code parses the incoming response. For a successful response, it collects the latitude and longitude information for each location returned. It adds all the location points to the map as pins and adjusts the bounds of the map to render all the pins.

In the preceding code, a request is sent to the search service to get the coordinates of the gas stations, and then pins and popups are added to the map to represent those coordinates. Learn more about the [Map](https://docs.microsoft.com/en-us/javascript/api/location-based-services-javascript/map?view=azure-iot-typescript-latest) and [Popup](https://docs.microsoft.com/en-us/javascript/api/location-based-services-javascript/popup?view=azure-iot-typescript-latest) classes and how to [addPins](https://docs.microsoft.com/en-us/javascript/api/location-based-services-javascript/map?view=azure-iot-typescript-latest#location_based_services_javascript_Map_addPins) and [setPopupOptions](https://docs.microsoft.com/en-us/javascript/api/location-based-services-javascript/popup?view=azure-iot-typescript-latest#location_based_services_javascript_Popup_setPopupOptions). 


## Related reference

To add a pin to the map, you need to use the following API and class:
* [Azure Maps Fuzzy Search API](https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchfuzzy)
* [Map](https://docs.microsoft.com/en-us/javascript/api/location-based-services-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/en-us/javascript/api/location-based-services-javascript/map?view=azure-iot-typescript-latest#location_based_services_javascript_Map_addPins)
