---
title: Live Video Analytics on IoT Edge quotas - Azure  
description: This article describes Live Video Analytics on IoT Edge quotas and limitations.
ms.topic: conceptual
ms.date: 05/22/2020 
 
---
# Quotas and limitation

This article enumerates the quotas and limitations of the Live Video Analytics on IoT Edge module.

## Maximum period of disconnected use

The edge module can sustain temporary loss of network connectivity. If the module remains disconnected for more than 36 hours, it will deactivate any graph instances that were running, and further Direct Method calls will be blocked.

To resume the edge module to an operational state, you will have to restore network connectivity, and the module needs to be able to successfully communicate with the Azure Media Service account.

## Maximum number of graph instances

You can have at most 1000 graph instances per module (created via GraphInstanceSet).

## Maximum number of graph topologies

You can have at most 50 graph topologies per module (created via GraphTopologySet).

## Limitations on graph topologies at Preview

With the Preview release, there are limitations on different nodes can be connected together in a media graph topology.

* RTSP source
    * Only one RTSP source is allowed per graph topology.
* Frame rate filter processor
    * Must be immediately downstream from RTSP source or motion detection processor.
    * Can be chained together, but cannot be used on separate.
* HTTP extension processor
    * Only one HTTP extension processor per graph topology.
* Motion detection processor
    * Must be immediately downstream from RTSP source.
    * There can be at most one such processor per topology.
* Signal gate processor
    * Must be immediately downstream from RTSP source.
* Asset sink 
    * Only one Asset sink is supported per graph.
    
        * If an Asset sink is used, then a file sink cannot be present, or vice versa.
    * Must be immediately downstream from RTSP source or signal gate processor.
* File sink
    * Only one file sink is supported per graph (see above note regarding asset sink).
    * Must be immediately downstream from signal gate processor.
    * Cannot be immediately downstream of HTTP extension processor, or motion detection processor.
* IoT Hub Sink
    * Cannot be immediately downstream of an IoT Hub source.

## Next steps

[Overview](overview.md)
