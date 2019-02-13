---
title: k4a_calibration_intrinsics_t structure
description: Camera sensor intrinsic calibration data. 
author: brenta
ms.author: brenta
ms.date: 01/18/2019
ms.topic: article
keywords: kinect, kinect for azure, k4a, api, sdk, structure
---
# k4a_calibration_intrinsics_t structure

Camera sensor intrinsic calibration data. 

## Syntax

```C
typedef struct {
    k4a_calibration_model_type_t type;
    unsigned int parameter_count;
    k4a_calibration_intrinsic_parameters_t parameters;
} k4a_calibration_intrinsics_t;
```

## Requirements

Requirement | Value
------------|--------------------------------
 Header | k4atypes.h (include k4a/k4a.h) 


## Members

[`k4a_calibration_model_type_t`](~/api/current/k4a-calibration-model-type-t.md) `type`

type of calibration model used 

`unsigned int` `parameter_count`

number of valid entries in parameters 

[`k4a_calibration_intrinsic_parameters_t`](~/api/current/k4a-calibration-intrinsic-parameters-t.md) `parameters`

calibration parameters 

