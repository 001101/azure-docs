---
title: k4a_depth_mode_t enumeration
description: Depth Sensor capture modes. 
author: brenta
ms.author: brenta
ms.date: 01/18/2019
ms.topic: article
keywords: kinect, kinect for azure, k4a, api, sdk, enumeration
---
# k4a_depth_mode_t enumeration

Depth Sensor capture modes. 

## Syntax

```C
typedef enum {
    K4A_DEPTH_MODE_OFF = 0,
    K4A_DEPTH_MODE_NFOV_2X2BINNED,
    K4A_DEPTH_MODE_NFOV_UNBINNED,
    K4A_DEPTH_MODE_WFOV_2X2BINNED,
    K4A_DEPTH_MODE_WFOV_UNBINNED,
    K4A_DEPTH_MODE_PASSIVE_IR,
} k4a_depth_mode_t;
```

## Requirements

Requirement | Value
------------|--------------------------------
 Header | k4atypes.h (include k4a/k4a.h) 


## Values

 Constant       | Description   
----------------|---------------
K4A_DEPTH_MODE_OFF | Depth sensor will be turned off with this setting. 
K4A_DEPTH_MODE_NFOV_2X2BINNED | RES: 320x288. 
K4A_DEPTH_MODE_NFOV_UNBINNED | RES: 640x576. 
K4A_DEPTH_MODE_WFOV_2X2BINNED | RES: 512x512. 
K4A_DEPTH_MODE_WFOV_UNBINNED | RES: 1024x1024. 
K4A_DEPTH_MODE_PASSIVE_IR | RES: 1024x1024. 

