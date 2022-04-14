# Text Pose Example

This simple example is designed to help explore Geoalignment in a web browser.

## Interfaces

Two web APIs are used:

 * [Geolocation](https://w3c.github.io/geolocation-api/) for location.
 * [OrientationSensor](https://w3c.github.io/orientation-sensor/) for orientation.

## Test Device & Browser

Three components are required for successful operation in the real world.

 1. **Sensor hardware** such as a Global Navigation Satellite System (GNSS) receiver for location.
 1. **User agent support** for the two APIs listed above.
 1. **User permission** to access sensor data.

A smartphone is a recommended test device for this example which is more likely to have access to the relevant sensors than a desktop or laptop computer, though a _test mode_ is also included to simulate sensor input.

**Web browser support** information for the relevant APIs can be found at the links below.

 * [Geolocation API](https://developer.mozilla.org/en-US/docs/Web/API/Geolocation_API#browser_compatibility)
 * [OrientationSensor API](https://developer.mozilla.org/en-US/docs/Web/API/OrientationSensor#browser_compatibility)

## User Guide

The display shows a live video stream from the device's camera, the status of API support in the user agent and any error messages.

 * The **Snapshot** button captures a still image together with the current time, location and orientation which are displayed below the live and snapshot images.
 * The **Clear** button clears the text output.
 * The **Test Mode** checkbox toggles test mode.

## Test Mode

Test mode generates mock data for location and orientation to facilitate testing on systems which lack the required components.

## Privacy Notice

This example runs entirely within the web client and no user data are transmitted to or recorded by any other party. By running this code, the user accepts full responsibility for the control of their own personal data.
