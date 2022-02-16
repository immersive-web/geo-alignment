# What Do We Mean by Geo-Alignment?

When AR devices are used outside, developers will want to include geospatial data in what the user sees and interacts with (e.g., data specified relative to the earth, such as via {Longitude, Latitude, Altitude}). 

The web already has a geolocation API, but it is not sufficient for these purposes since it gives position but not orientation of the device relative to the world.  Device geolocation returned by this API tends to be of very poor quality (but that may improve over time). The deviceorientation API is not likely to be satisfactory as a basis for device geo-orientation: it is of very poor quality, was never standardized (and is potentially going to be removed from existing browsers) and is not synchronized with the WebXR frame data.  The lack of synchronization (e.g., reliable, common timestamps) makes it almost impossible to reliably align the WebXR and deviceOrientation frames of reference in Javascript.

ARKit offers an alternative approach.  When initializing ARKit, developers have the option to have the platform attempt to align it's local coordinate system with geospatial EUS orientation (e.g., X east, Y up, Z south). This provides a possible direction for how geospatial might be handled: have the WebXR API expose a capability to request that the the coordinate frame be aligned with geospatial EUS coordinates, and provide a way for the developer to inspect if the resulting session is aligning the coordinate frame as requested. 

Crude/simple geospatial positional alignment in Javascript (between the user and the local coordinates) is (relatively) easy, if you are guaranteed to have the local device coordinates aligned with EUS.  An example implementation can be found [here](https://github.com/MozillaReality/webxr-geospatial/), based on an early version of the webxr spec. Each time a geospatial value is received from the local coordinates, an estimate of the geospatial location of the origin of the local coordinate frame can be updated (based on error values, etc). This approach won't be any better than the error of the geolocation API, but can be stable (because the local coordinates are used for locating and rendering content, not the very-slowly-changing geolocation values).  Combining a geo-aligned WebXR coordinate frame with geospatial location, applicationss can easily display geospatially aligned content. Math libraries (such as the underlying libraries in cesium.js, used in the example repository here) exist to make this easy.

In theory, if a platform also provided better estimates of geoposition, it could simply provide those improved values through the geolocation API, benefiting both geospatial AR and other applications that use location. Permission to access location would always be done through the current web permissions approach.  For example, AR displays such as Hololens might one day support geospatially locating (position and orientation) their saved tracking spaces, allowing both geo-orientation as described here, along with precise geolocation.

# Scope of this Repository

This proposal repository is aimed at discussing the viability of a "request geo-aligned base coordinate frame" capability for WebXR, and not solving all the the other issues related to geospatial data and applications.  For example, changes to the the geolocation API, dealing with geospatial data that does not include an altitude, how to best align a WebXR frame of reference with geolocation in the presence of this capability.

The Generic Sensor group is incubating a next generation Geolocation API called [Geolocation Sensor](https://wicg.github.io/geolocation-sensor/) (based on the Generic Sensor API) and this discussion could possibly impact that work.

There is a lot of work and discussion in the AR community right now in relation to the *AR Cloud*, the idea that by building a model of the world (whether public or private, shared or limited to a device, etc.) it will be possible to precisely position a device in the world, align multiple devices to common coordinate frame, persist data points, and so on.  This discussion is orthogonal to that topic:  AR Cloud technology might be used to implement this capability (for example), simultaneously providing precise geolocation, or it could be integrated via web apps and cloud services if the user gives the right permissions to the app.  Having a platform expose a geo-aligned coordinate frame is independent of this.

# Requesting geo-alignment

WebXR session creation supports the notion of requesting features on a session. The geoAlignment feature could be requested simply by adding a feature request:

```
  navigator.xr.requestSession('immersive-ar', {
    requiredFeatures: [ 'geo-alignment' ]
  })
```
or 
```
  navigator.xr.requestSession('immersive-ar', {
    optionalFeatures: [ 'geo-alignment' ]
  })
```

This feature would request that the underlying session aligns it's coordinate system such that the XYZ axes corresponded to a standard geospatial frame of reference, as as East-Up-South.

If geo-alignment is possible, the platform will attempt to enable it.  Following the semantics of `requiredFeatures` and `optionalFeatures`, if geo-alignment is not possible, the session request would fail if it is specified as a required feature.  In all other cases, the session request would not fail because of this feature.

The feature can expose geospatially aligned coordinates without provide geospatial position to the web application. This mean that we can request this without asking the user for location permission (assuming they have already given the browser location permission).


# Testing for geo-alignment at runtime

Once a session is created, geo-alignment may or may not work at any given moment.  For example, if there is significant magnetic interference, the platform may signal that alignment has failed.  Or, if alignment is done via a geospatial alignment of saved device tracking space, some spaces might not be aligned and others might be.

One way to expose the state of geo-alignment would be to build on the proposed API for [real world geometry](https://github.com/immersive-web/real-world-geometry/blob/master/plane-detection-explainer.md), and include it in the `frame.worldInformation` passed into the rAF callback.

``` 
// Function that's passed in to XRSession.requestAnimationFrame().
function onXRFrame(timestamp, frame) {
  let geoAlignment = frame.worldInformation.geoAlignment;
  if (geoAlignment.active) {
    // in our application, we want to work with EUS, so if the platform has exposed a different standard, such as ENU,
    // retrieve an adjustment matrix to account for this different fixed coordinate frame
    let alignmentMatrix = computeAlignmentMatrixToEUS(geoAlignment.geospatialFrame) 

    // in our application, we want to present content differently if the orientation accuracy is poor quality
    if (geoAlignment.headingAccuracy > 0.17) {
        // alignment error greater than 10 degrees, or 0.17 radians
    } else {
        // alignment is pretty good
    }
  } else {
    // coordinate frames aren't aligned, handle appropriately
  }
 });
 
 frame.session.requestAnimationFrame(onXRFrame);
}
```

Here, we assume the `worldInformation.geoAlignment` property has three properties:
- `active`, a boolean indicating if the coordinate system is currently geoaligned
- `geospatialFrame`, a string corresponding to one of the possible geospatial reference frames (EUS, ENU, etc).
- `headingAccuracy`, a floating point number specifying to accuracy estimate in radians (similar to the values reported for position by the web geolocation API)

If the web page also requests geospatial location access, it will be trivial to create geospatial AR applications. 

# Considerations

The benefit of integrating geo-alignment into the platform is that the underly XR platform has the best chance of dealing with sensor errors, and the underlying platform can adjust it's understanding of the alignment in the same manner as it adjust the rest of it's world understanding.

A major challenge with geoalignment in current platforms is that (absent some external AR Cloud service that aligns the underlying Cloud data with the world), a magnetic compass is required to detect and estimate North. Compasses are sensitive to magnetic interference and in some situations the north value can rapidly change, such as when the device moves near a magnet or large metal structure.

As ARKit demonstrates, the OS is the best place to handle this, as it can leverage other sensors (e.g., accelerometers, camera motion flow) and other data (the model of the world) to detect and manage these errors.  Even if the underlying XR platform does not support geo-alignment, by integrating with the WebXR platform more can be done that can be done by reading the compass independently at the Javascript level: the platform has access to more sensor data than the web might (e.g., OS-level compass, accelerometer AND visual data), so it can distinguish between more than can be done separately.

The alignment would be updated continuously, such that the coordinate frame returned by WebXR is aligned with EUS (or some other frame) as best as is possible.  ARKit does this, and as the sensor knowledge changes, it will rotate the base coordinate system and update anchors poses to reflect the changing orientation.  The Z axis may rotate around Y (for example), but the visual scene remains stable because the anchor poses are updated by the system at the same time.

The key to making this work is that the WebXR API's never present the base coordinate system as static. Each frame, the base coordinate system may change, and have all the devices, controllers and anchors updated appropriately. 