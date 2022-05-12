### Gap Analysis

This document identifies missing components required for geo-alignment in a user agent.

#### W3C Interfaces

Geo-alignment is defined by location and orientation with respect to the Earth's axes: east, north and up. These data can be accessed via the following interfaces already proposed in W3C:

| W3C Document | Current Status | Interface |
| :-- | :-- | :-- |
| [Geolocation API](https://www.w3.org/TR/geolocation/) | Proposed Recommendation | [Geolocation interface and callbacks](https://www.w3.org/TR/geolocation/#geolocation_interface) |
| [Orientation Sensor](https://www.w3.org/TR/orientation-sensor/) | Working Draft | [OrientationSensor interface](https://www.w3.org/TR/orientation-sensor/#orientationsensor-interface) |
| [Permissions](https://www.w3.org/TR/permissions/) | Working Draft | [Permissions interface](https://www.w3.org/TR/permissions/#permissions-interface) |

[W3C Devices and Sensors (DAS)](https://www.w3.org/das/) Working Group published a roadmap giving a useful [overview of W3C sensor document status](https://www.w3.org/das/roadmap) including motion sensors.

#### Key Issues

Three key issues have been identified:

 1. **Agnostic interface**
Geo-alignment is a [high-level sensor](https://www.w3.org/TR/motion-sensors/#highlevel-sensors) interface which should be agnostic of the underlying [low-level sensors](https://www.w3.org/TR/motion-sensors/#low-level-sensors) since position and orientation can be calculated by many different means. Position can be determined from visual, inertial or satellite navigation data and this is reflected in the [Geolocation API](https://www.w3.org/TR/geolocation/). Orientation should be handled in a similar way.

 1. **Capability**
 Geo-alignment can be determined only if three elements are present and correct:

    1. **User agent support** in the chosen web client. If this is not supported, sensor information cannot be accessed by the browser to calculate position and/or orientation.

    1. **User permission** to access sensors obtained through [Permissions](https://www.w3.org/TR/permissions/). If this is not supported or not allowed, accessing sensor information could pose a security risk to the user since location is personal information and the [data privacy rights of web users should be properly respected](https://www.w3.org/Privacy/).

    1. **Suitable sensors** to provide position and orientation data. If less than the required number of sensors are available, geo-alignment calculations may be less accurate or completely impossible.

 1. **Accuracy**
  Correct assessment of geo-alignment accuracy is critical since identified use cases require sufficient precision to yield a successful result. For example, Augmented Reality (AR) use cases need high accuracy geo-alignment to correctly overlay virtual assets on a video stream. Mobile devices may lack this accuracy and sufficient information should be provided in the API to determine the quality of geo-alignment available to the user agent so best use can be made of the available data.

#### Gap Analysis

Several omissions for geo-alignment have been identified in the current W3C APIs:

 1. **Data accuracy**

  Data accuracy is quantified for position by [accuracy](https://www.w3.org/TR/geolocation/#dom-geolocationcoordinates-accuracy) and [altitudeAccuracy](https://www.w3.org/TR/geolocation/#dom-geolocationcoordinates-altitudeaccuracy) attributes in the [Geolocation interface](https://www.w3.org/TR/geolocation/#coordinates_interface). No accuracy metrics are available in the [OrientationSensor interface](https://www.w3.org/TR/orientation-sensor/#orientationsensor-interface) so geo-alignment accuracy cannot be determined.

 1. **Geo-alignment permission**

   [Standardised permissions](https://w3c.github.io/permissions-registry/#registry-table-of-standardized-permissions) in the [Permissions Registry](https://w3c.github.io/permissions-registry) include `geolocation` to allow position data access. No entry exists to request permission for `orientation` (or individual sensors such as [accelerometer](https://www.w3.org/TR/accelerometer/#accelerometer-interface), [magnetometer](https://www.w3.org/TR/magnetometer/#magnetometer-interface) and [gyroscope](https://www.w3.org/TR/gyroscope/#gyroscope-interface)) in the [provisional permissions](https://w3c.github.io/permissions-registry/#registry-table-of-provisional-permissions). Proper consideration should be given to whether `orientation` (or `motion`) is a [powerful feature](https://www.w3.org/TR/permissions/#dfn-powerful-feature) with and without `geolocation`.

 1. **Permission Registry**

  No entries for `camera` and `microphone` exist in the [provisional permissions](https://w3c.github.io/permissions-registry/#registry-table-of-provisional-permissions) though both are [implemented in Chrome (v64 onwards)](https://developer.mozilla.org/en-US/docs/Web/API/Permissions_API#permissions_interface) and this should be updated.

 1. **Device capability**

  Geo-alignment is a high-level interface which relies on [fusion of data from multiple sensors](https://w3c.github.io/motion-sensors/#fusion-sensors). No high-level permission exists to request orientation sensor access which is [inconsistent with the high-level interface to `geolocation`](https://www.w3.org/TR/geolocation/#check-permission).

 1. **LiDAR support**

  No W3C interface documentation has been found for LiDAR (light detection and ranging) which is already included as standard in some high-end tablets and can be used to improve geo-alignment accuracy. [AbsoluteOrientationSensor](https://w3c.github.io/orientation-sensor/#absoluteorientationsensor-interface) and [RelativeOrientationSensor](https://w3c.github.io/orientation-sensor/#relativeorientationsensor-interface) could benefit from such support.

 1. **Geo-aligned orientation**

  [Orientation sensor interface](https://w3c.github.io/orientation-sensor/#orientationsensor-interface) does not indicate whether or not orientation is geo-aligned. This is inconsistent with the [DeviceOrientationEvent interface](https://www.w3.org/TR/orientation-event/#deviceorientationevent).

 1. **Yaw, pitch and roll angles**

  [Orientation sensor interface](https://w3c.github.io/orientation-sensor/#orientationsensor-interface) does not include yaw, pitch and roll angles which represent rotations about the up, left and front axes respectively of an object. See [w3c/orientation-sensor#43](https://github.com/w3c/orientation-sensor/issues/43)

#### Convenience API

An [alternative access model](https://www.w3.org/TR/mediacapture-streams/#dom-mediadevices-getusermedia) for video capture is proposed by WebRTC with implicit `camera` and `microphone` permission requests using `MediaDevices.getUserMedia`. This may serve as a useful template for a convenience API for geo-alignment.
