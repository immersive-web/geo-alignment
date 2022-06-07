# Orientation Event Example

This simple example is designed to help explore orientation in a web browser and uses an entirely separate interface to the [text-pose example](https://immersive-web.github.io/geo-alignment/examples/text-pose/).

## Interfaces

[DeviceOrientation Event](https://www.w3.org/TR/orientation-event/) API includes support for two events:

 * `deviceorientation` [event](https://www.w3.org/TR/orientation-event/#deviceorientation) - relative to the device
 * `deviceorientationabsolute` [event](https://www.w3.org/TR/orientation-event/#deviceorientationabsolute) - relative to the Earth

## Test Device & Browser

Two components are required for successful operation in the real world.

 1. **Sensor hardware** such as an accelerometer, gyroscope and magnetometer to determine orientation.
 1. **User agent support** for the API listed above.

A smartphone or tablet is a recommended test device for this example which is more likely to have access to the relevant sensors than a desktop or laptop computer.

**Web browser support** information for the relevant API can be found at the link below.

 * [DeviceOrientation Event](https://developer.mozilla.org/en-US/docs/Web/API/DeviceOrientationEvent#browser_compatibility)

## User Guide

The display shows a live values from the device's orientation sensors and the status of API support in the user agent.

 * The **Absolute** button toggles between displaying `deviceorientation` and `deviceorientationabsolute` events.

If the display shows that [DeviceOrientation Event](https://www.w3.org/TR/orientation-event/) API is supported but no events are displayed, the user agent `console` output should be checked for diagnostic information as this interface is deprecated by some web browsers.

## Privacy Notice

This example runs entirely within the web client and no user data are transmitted to or recorded by any other party. By running this code, the user accepts full responsibility for the control of their own personal data.
