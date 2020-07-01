---
title: 'Model variables - Azure Time Series Insights | Microsoft Docs'
description: 'Model variables ... '
author: shreyasharma
ms.author: shresha
ms.service: time-series-insights
ms.topic: conceptual
ms.date: 06/30/2020
---

# Model variables

This article describes the Time Series Model variables that specify formula and computation rules on events.

Each variable can be one of three kinds: *numeric*, *categorical*, and *aggregate*.

* **Numeric** kinds work with continuous numeric values.
* **Categorical** kinds work with a defined set of discrete values.
* **Aggregate** kinds combine multiple variables of a single kind (either all numeric or all categorical).

The following table displays which properties are relevant for each variable kind.

[![Time Series Model variable table](media/v2-update-tsm/time-series-model-variable-table.png)](media/v2-update-tsm/time-series-model-variable-table.png#lightbox)

#### Numeric variables

| Variable property | Description |
| --- | ---|
| Variable filter | Filters are optional conditional clauses to restrict the number of rows being considered for computation. |
| Variable value | Telemetry values used for computation coming from the device or sensors or transformed by using Time Series Expressions. Numeric kind variables must be of the type *Double*.|
| Variable interpolation | Interpolation specifies how to reconstruct a signal by using existing data. *Step* and *Linear* interpolation options are available for numeric variables. |
| Variable aggregation | Support computation through *Avg*, *Min*, *Max*, *Sum*, *Count*, *First*, *Last* and time-weighted (*Avg*, *Min*, *Max*, *Sum*, *Left*) operators. |

Variables conform to the following JSON example:

```JSON
"Interpolated Speed": {
  "kind": "numeric",
  "value": {
    "tsx": "$event.[speed].Double"
  },
  "filter": null,
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span": "P1D"
    }
  },
  "aggregation": {
    "tsx": "left($value)"
  }
}
```

#### Categorical variables

| Variable property | Description |
| --- | ---|
| Variable filter | Filters are optional conditional clauses to restrict the number of rows being considered for computation. |
| Variable value | Telemetry values used for computation coming from the device or sensors. Categorical kind variables must be either *Long* or *String*. |
| Variable interpolation | Interpolation specifies how to reconstruct a signal by using existing data. The *Step* interpolation option is available for categorical variables. |
| Variable categories | Categories create a mapping between the values coming from the device or sensors to a label. |
| Variable default category | The default category is for all values that aren't being mapped in the "categories" property. |

Variables conform to the following JSON example:

```JSON
"Status": {
  "kind": "categorical",
  "value": {
     "tsx": "toLong($event.[Status].Double)" 
},
  "interpolation": {
    "kind": "step",
    "boundary": {
      "span" : "PT1M"
    }
  },
  "categories": [
    {
      "values": [0, 1, 2, 3],
      "label": "Good"
    },
    {
      "values": [3],
      "label": "Bad"
    }
  ],
  "defaultCategory": {
    "label": "Not Applicable"
  }
}
```

#### Aggregate variables

| Variable property | Description |
| --- | ---|
| Variable filter | Filters are optional conditional clauses to restrict the number of rows being considered for computation. |
| Variable aggregation | Support computation through *Avg*, *Min*, *Max*, *Sum*, *Count*, *First*, *Last*. |

Variables conform to the following JSON example:

```JSON
"Aggregate Speed": {
  "kind": "aggregate",
  "filter": null,
  "aggregation": {
    "tsx": "avg($event.Speed.Double)"
  }
}
```

Variables are stored in the type definition of a time series model and can be provided inline via APIs to override or complement the stored definition.

## Next steps

* Learn more about [Time Series Model](./concepts-model-overview.md).

* Read more about how to define variables inline using [Query APIs](./concepts-query-overview.md).

