---
title: Speech Devices SDK Microphone array recommendations - Speech Services
titleSuffix: Azure Cognitive Services
description: Speech Devices SDK Microphone array recommendations.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: erhopf
ms.custom: seodec18
---
# Speech Devices SDK Microphone array recommendations

## Microphone Geometry

The following array geometries are recommended for use with the
Microsoft Audio Stack. Location of sound sources and rejection of
ambient noise is generally improved with greater number of microphones
with dependencies on specific applications, user scenarios, and the
device form factor.

|          | Circular Array    |       |  Linear Array              |                |
|----------|-------------------|-------------------|----------------|----------------|
|          |![7 mic circular array](media/speech-devices-sdk/7-mic-c.png)|![4 mic circular array](media/speech-devices-sdk/4-mic-c.png)|![4 mic linear array](media/speech-devices-sdk/4-mic-l.png)|![2 mic linear array](media/speech-devices-sdk/2-mic-l.png)|
| \# Mics  | 7                 | 4                 | 4              | 2              |
| Geometry | 6 Outer, 1 Center, Radius = 42.5mm,  Evenly Spaced| 3 Outer, 1 Center, Radius = 42.5mm, Evenly Spaced | Length = 120mm, Spacing = 40mm | Spacing = 40mm |

## Component Selection

Microphone components should be selected in such that accurately
reproduces a signal free of noise and distortion.

The recommended properties when selecting microphones are:

| Parameter                         | Recommended                       |
|-----------------------------------|-----------------------------------|
| SNR                               | \> 65 dB (1kHz signal 94 dBSPL, A-weighted noise)   |
| Amplitude Matching                | ± 1 dB @ 1kHz                     |
| Phase Matching                    | ± 2° @ 1kHz                       |
| Acoustic Overload Point (AOP)     | \> 120 dBSPL (THD = 10%)          |
| Bit Rate                          | Minimum 24-bit                    |
| Sampling Rate                     | Minimum 16kHz\*                   |
| Directivity                       | Omnidirectional                   |
| Frequency Response                | ± 3 dB, 200-8000Hz Floating Mask\*|
| Reliability                       | Storage Temperature Range -40°C  to 70°C |
|                                   | Operating Temperature Range -20°C to 55°C|

*\*Higher sampling rates or "wider" frequency ranges may be necessary
for high quality communications (VoIP) applications*

Note that good component selection must be paired with good
electroacoustic integration in order to avoid impairing the performance
of the components used. Unique use cases may also necessitate additional
requirements (e.g. operating temperature ranges).

## Microphone Array (System) Integration

The performance of the arrays when integrated into a device and after
any fixed gain or EQ should meet the following recommendations:

|  Parameter        |    Recommended |
|--------------------|----------------------------------------------------|
|  SNR                 | \> 65 dB (1 kHz signal 94 dBSPL, A-weighted noise) |
|  Output Sensitivity  | -26 dBFS/Pa @ 1 kHz (recommended) |
|  Amplitude Matching  | ± 2 dB, 200-8000 Hz |
|  Phase Matching      | ± 5°, 200-8000 Hz |
| THD%                 | ≤ 1%, 200-8000 Hz, 94 dBSPL, 5th Order |
|  Frequency Response  | ± 6 dB, 200-8000Hz Floating Mask\* |

*\*"Wider" frequency ranges may be necessary for high quality
communications (VoIP) applications*

## Speaker Integration Recommendations

As echo cancellation is necessary for speech recognition devices that
contain speakers, additional recommendations are provided for speaker
selection and integration.

| Parameter                         | Recommended                       |
|-----------------------------------|-----------------------------------|
| Linearity Considerations          | No non-linear processing after speaker reference, otherwise a hardware based loopback reference stream is required  |
| Speaker Loopback                  | Provided via WASAPI, private APIs, custom ALSA plug-in (Linux), or provided via firmware channel      |
| THD%                              | 3rd Octave Bands minimum 5th Order, 70 dBA Playback @ 0.8m  ≤ 6.3%, 315-500 Hz ≤ 5%, 630-5000 Hz                 |
| Echo Coupling to Microphones      | \> -10 dB TCLw using ITU-T G.122 Annex B.4 method, normalized to mic level   |
|                                   | TCLw = TCLwmeasured \+ (Measured Level - Target Output Sensitivity)         |
|                                   | TCLw = TCLwmeasured \+ (Measured Level - (-26))|

## Integration Design Architecture

The following guidelines for architecture are necessary when integrating
microphones into a device:

| Parameter                         | Recommendation                    |
|-----------------------------------|-----------------------------------|
| Mic Port Similarity               | All microphone ports are same length in array    |
| Mic Port Dimensions               | Port size Ø0.8-1.0mm. Port Length / Port Diameter \< 2              |
| Mic Sealing                       | Sealing gaskets uniformly implemented in stack-up. Recommend \> 70% compression ratio for foam gaskets     |
| Mic Reliability                   | Mesh should be used to prevent dust and ingress (between PCB for bottom ported microphones and sealing gasket/top cover)  |
| Mic Isolation                     | Rubber gaskets and vibration decoupling through structure, particularly for isolating any vibration paths due to integrated speakers      |
| Sampling Clock                    | Device audio must be free of jitter and drop-outs with low drift    |
| Record Capability                 | The device must be able to record individual channel raw streams simultaneously |
| USB                               | All USB audio input devices must set descriptors according to the [USB Audio Devices Rev3 Spec](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Microphone Geometry               | Drivers must implement [Microphone Array Geometry Descriptors](https://docs.microsoft.com/en-us/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) correctly  |
| Discoverability                   | Devices must not have any undiscoverable or uncontrollable hardware, firmware or 3rd party software-based non-linear audio processing algorithms to/from the device|
| Capture Format                    | Capture formats must use a minimum sampling rate of 16kHz  and recommended 24-bit depth      |

## Electrical Architecture Considerations

Where applicable, arrays may be connected to a USB host (i.e. SoC that
runs the Microsoft Audio Stack) and interfaces to speech services or
other applications.

Hardware components such as PDM-to-TDM conversion should ensure that the
dynamic range and SNR of the microphones is preserved within
re-samplers.

High speed USB Audio Class 2.0 should be supported within any audio MCUs
in order to provide the necessary bandwidth for up to 7 channels at
higher sample rates and bit depths.
