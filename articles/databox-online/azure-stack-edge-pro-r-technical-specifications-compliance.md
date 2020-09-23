---
title: Microsoft Azure Stack Edge Pro R technical specifications and compliance| Microsoft Docs
description: Learn about the technical specifications and compliance for your Azure Stack Edge Pro R device
services: databox
author: alkohli

ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: alkohli
---
# Azure Stack Edge Pro R technical specifications

The hardware components of your Azure Stack Edge Pro R device adhere to the technical specifications outlined in this article. The technical specifications describe the Power supply units (PSUs), storage capacity, enclosures, and environmental standards.


## Compute, memory specifications

The Azure Stack Edge Pro R device have the following specifications for compute and memory:

| Specification       | Value                  |
|---------------------|------------------------|
| CPU    | 2 X 10 core CPU, Intel Xeon Silver 4114 |
| Memory              | 256 GB RAM (2666 MT/s)     |


## Compute acceleration specifications

A Graphics Processing Unit (GPU) is included on every device that enables Kubernetes, deep learning, and machine learning scenarios.

| Specification           | Value                  |
|-------------------------|----------------------------|
| GPU   | One nVidia T4 GPU <br> For more information, see [NVIDIA T4](https://www.nvidia.com/en-us/data-center/tesla-t4/).| 

## Power supply unit specifications

The Azure Stack Edge Pro R device has two 100-240 V Power supply units (PSUs) with high-performance fans. The two PSUs provide a redundant power configuration. If a PSU fails, the device continues to operate normally on the other PSU until the failed module is replaced. The following table lists the technical specifications of the PSUs.

| Specification           | 550 W PSU                  |
|-------------------------|----------------------------|
| Maximum output power    | 550 W                      |
| Heat                    | 2891 BTU/hr                |
| Frequency               | 50/60 Hz                   |
| Voltage range selection | Auto ranging: 115-230 V AC |
| Hot pluggable           | Yes                        |


## Storage specifications

The Azure Stack Edge Pro R devices have 8 data disks and 2 M.2 SATA disks that serve as operating system disks. For more information, go to [M.2 SATA disks](https://en.wikipedia.org/wiki/M.2).

#### Storage for 1-node device

The following table has the details for the storage capacity of the 1-node device.

|     Specification                          |     Value             |
|--------------------------------------------|-----------------------|
|    Number of solid-state drives (SSDs)     |    8                  |
|    Single SSD capacity                     |    8 TB               |
|    Total capacity                          |    64 TB              |
|    Total usable capacity*                  |    ~ 42 TB          |

**Some space is reserved for internal use.*

<!--#### Storage for 4-node device

The following table has the details for the storage capacity of the 4-node device.

|     Specification                          |     Value             |
|--------------------------------------------|-----------------------|
|    Number of solid-state drives (SSDs)     |    32 (4 X 8 disks for 4 devices)                |
|    Single SSD capacity                     |    8 TB               |
|    Total capacity                          |    256 TB              |
|    Total usable capacity*                  |    ~ 163 TB          |

**After mirroring and parity, and reserving some space for internal use.* -->


## Enclosure dimensions and weight specifications

The following tables list the various enclosure specifications for dimensions and weight.

### Enclosure dimensions 

The following table lists the dimensions of the device and the UPS with the rugged case in millimeters and inches.

|     Enclosure     |     Millimeters     |     Inches     |
|-------------------|---------------------|----------------|
|    Height         |    301.2            |    11.86       |
|    Width          |    604.5            |    23.80       |
|    Length         |    740.4            |    35.50       |

<!--#### For the 4-node system

For the 4-node system, the servers and the heater are shipped in a 5U case and the UPS are shipped in a 4U case.

The following table lists the dimensions of the 5U device case:  

|     Enclosure     |     Millimeters   |     Inches     |
|-------------------|-------------------|----------------|
|    Height         |    387.4          |    15.25       |
|    Width          |    604.5          |    23.80       |
|    Length         |    901.7          |    35.50       |

The following table lists the dimensions of the 4U UPS case: 

|     Enclosure     |     Millimeters   |     Inches    |
|-------------------|-------------------|---------------|
|    Height         |    342.9          |    13.5       |
|    Width          |    604.5          |   23.80       |
|    Length         |    901.7          |   35.50       |
-->

### Enclosure weight 

The weight of the device depends on the configuration of the enclosure.

|     Enclosure                                 |     Weight          |
|-----------------------------------------------|---------------------|
|    Total weight of 1-node device + UPS + rugged case with end caps     |    ~105 lbs.          |

<!--#### For the 4-node system

|     Enclosure                                 |     Weight          |
|-----------------------------------------------|---------------------|
|   Approximate weight of fully populated 4 devices + heater in 5U case     |    ~200 lbs.          |
|   Approximate weight of fully populated 4 UPS in 4U case    |    ~145 lbs.          |
-->

<!--## Enclosure environment specifications


This section lists the specifications related to the enclosure environment such as temperature, humidity, and altitude.


### Temperature and humidity

|     Enclosure         |     Ambient    temperature range     |     Ambient relative    humidity     |     Maximum dew point     |
|-----------------------|--------------------------------------|--------------------------------------|---------------------------|
|    Operational        |    10°C - 35°C (50°F - 86°F)         |    10% - 80% non-condensing.         |    29°C (84°F)            |
|    Non-operational    |    -40°C to 65°C (-40°F - 149°F)     |    5% - 95% non-condensing.          |    33°C (91°F)            |

### Airflow, altitude, shock, vibration, orientation, safety, and EMC

|     Enclosure                           |     Operational specifications                                                                                                                                                                                         |
|-----------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    Airflow                              |    System airflow is front to rear. System must be operated with a low-pressure, rear-exhaust installation. <!--Back pressure created by rack doors and obstacles should not exceed 5 pascals (0.5 mm water gauge).   |
|    Maximum altitude, operational        |    3048 meters (10,000 feet) with maximum operating temperature de-rated determined by [Operating temperature de-rating specifications](#operating-temperature-de-rating-specifications).                                                                                |
|    Maximum altitude, non-operational    |    12,000 meters (39,370 feet)                                                                                                                                                                                         |
|    Shock, operational                   |    6 G for 11 milliseconds in 6 orientations                                                                                                                                                                         |
|    Shock, non-operational               |    71 G for 2 milliseconds in 6 orientations                                                                                                                                                                           |
|    Vibration, operational               |    0.26 G<sub>RMS</sub> 5 Hz to 350 Hz random                                                                                                                                                                                     |
|    Vibration, non-operational           |    1.88 G<sub>RMS</sub> 10 Hz to 500 Hz for 15 minutes (all six sides tested.)                                                                                                                                                  |
|    Orientation and mounting             |    19" rack mount                                                                                                                                                                                        |
|    Safety and approvals                 |    EN 60950-1:2006 +A1:2010 +A2:2013 +A11:2009 +A12:2011/IEC 60950-1:2005 ed2 +A1:2009 +A2:2013 EN 62311:2008                                                                                                                                                                       |
|    EMC                                  |    FCC A, ICES-003 <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 +A1:2015/CISPR 24:2010 +A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (Class D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013                                                                                                                                                                                         |
|    Energy             |    Commission Regulation (EU) No. 617/2013                                                                                                                                                                                        |
|    RoHS           |    EN 50581:2012                                                                                                                                                                                        |


### Operating temperature de-rating specifications

|     Operating    temperature de-rating     |     Ambient    temperature range                                                         |
|--------------------------------------------|------------------------------------------------------------------------------------------|
|    Up to 35°C (95°F)                       |    Maximum temperature is reduced by 1°C/300 m (1°F/547 ft) above 950 m (3,117 ft).    |
|    35°C to 40°C (95°F to 104°F)            |    Maximum temperature is reduced by 1°C/175 m (1°F/319 ft) above 950 m (3,117 ft).    |
|    40°C to 45°C (104°F to 113°F)           |    Maximum temperature is reduced by 1°C/125 m (1°F/228 ft) above 950 m (3,117 ft).    |
End of this section-->

## Next steps

- [Deploy your Azure Stack Edge](azure-stack-edge-placeholder.md)
