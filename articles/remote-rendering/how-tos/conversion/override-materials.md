---
title: Overriding materials during model conversion
description: Explains the material overriding workflow at conversion time
author: FlorianBorn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
---

# Overriding materials during model conversion

During conversion, the material settings in the source model are used to define the [PBR materials](../../overview/features/pbr-materials.md) used by the renderer.
Sometimes the default conversion does not give the desired results and adjustments need to be made.
When a model is converted for use in Azure Remote Rendering, a material-override file can be provided to customize how material conversion is done on a per-material basis.
The section on [configuring model conversion](configure-model-conversion.md) has instructions for declaring the material override filename.

## The override file used during conversion

As a simple example, let's say that a box model has a single material, called "Default", but the albedo color needs to be adjusted for use in ARR.
In this case, a `box_materials_override.json` file can be created as follows:

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

The `box_materials_override.json` file is placed in the input container, and a `ConversionSettings.json` is added beside `box.fbx`, which tells conversion where to find the override file (see [Configuring the model conversion](configure-model-conversion.md)):

```json
{
    "material-override" : "box_materials_override.json"
}
```

When the model is converted, the new settings will apply.

### Color materials

The [color material](../../overview/features/color-materials.md) model describes a constantly shaded surface that is independent of lighting.
This is useful for assets made by Photogrammetry algorithms, for example.
In material override files, a material can be declared to be a color material by setting `unlit` to `true`.

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

## JSON schema

The full JSON schema for materials files is given here. With the exception of `unlit`, the properties available are a subset of the properties described in the sections on the [color material](../../overview/features/color-materials.md) and [PBR material](../../overview/features/pbr-materials.md) models.

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/color" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent" : { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean"},
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## Next steps

* [Color materials](../../overview/features/color-materials.md)
* [PBR materials](../../overview/features/pbr-materials.md)
