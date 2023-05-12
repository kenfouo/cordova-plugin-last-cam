# Last Cam

> Last Cam is a Cordova Plugin that brings to the table what other camera plugins lack: Video Capture capabilities 
within a custom Camera view.

![LastCam](assets/last-cam-logo.png)

# Cordova Plugin Last Cam

[![GitHub](https://img.shields.io/badge/version-0.0.1-blue.svg)](https://github.com/Bengejd/cordova-plugin-last-cam)
![Github](https://img.shields.io/badge/Platform-IOS-lightgrey.svg)
![GitHub issues](https://img.shields.io/github/issues-raw/bengejd/cordova-plugin-last-cam/.svg)
[![GitHub](https://img.shields.io/github/license/mashape/apistatus.svg)](https://github.com/Bengejd/cordova-plugin-last-cam)

**Releases are being kept up to date when appropriate. However, this plugin is under constant development. As such it is recommended to use master to always have the latest fixes & features.**

**PR's are greatly appreciated. Maintainer(s) wanted.**

## Table of Contents
- [Features](#features)
- [Install](#install)
- [IOS Quirks](#ios-quirks)
- [Usage](#usage)
    - [Start Camera](#startcameraoptions-successcallback-errorcallback)
    - [Stop Camera](#stopcamerasuccesscallback-errorcallback)
    - [Switch Camera](#switchcamerasuccesscallback-errorcallback)
    - [Switch Flash](#switchflashsuccesscallback-errorcallback)
    - [Take Picture](#takepicturesuccesscallback-errorcallback)
    - [Start Video Recording](#startvideocapturesuccesscallback-errorcallback)
    - [Stop Video Recording](#stopvideocapturesuccesscallback-errorcallback)
    - [Recording Timer](#recordingtimersuccesscallback-errorcallback)
- [Alternative Plugins](#alternative-plugins)
- [License](#license)
- [Credits](#credits)

## Features

<ul>
  <li>Start a camera preview from HTML code.</li>
  <li>Maintain HTML interactivity.</li>
  <li>Set a custom position for the camera preview box.</li>
  <li>Set a custom size for the preview box.</li>
</ul>

## Install

Use any one of the installation methods listed below depending on which framework you use.

To install the master version with latest fixes and features

```
cordova plugin add https://github.com/bengejd/cordova-plugin-last-cam.git

ionic cordova plugin add https://github.com/bengejd/cordova-plugin-last-cam.git
```

And Ionic-Native inclusion.

```
npm install @ionic-native/LastCam
```

Import `LastCam` into your constructor.
 ```
import {LastCam} from '@ionic-native/last-cam';
...
 
constructor(private lastCam: LastCam) {
   }
...
 ```

### iOS Quirks

It is not possible to test in a simulator. You must use a real device for all testing.

#### config.xml
If you are developing for iOS 10+ you must also add the following to your config.xml

```xml
<config-file platform="ios" target="*-Info.plist" parent="NSCameraUsageDescription" overwrite="true">
  <string>Allow the app to use your camera</string>
</config-file>
```

```xml
<config-file platform="ios" target="*-Info.plist" parent="NSPhotoLibraryUsageDescription" overwrite="true">
  <string>Allow the app to save media to your camera roll</string>
</config-file>
```

```xml
<config-file platform="ios" target="*-Info.plist" parent="NSMicrophoneUsageDescription" overwrite="true">
  <string>Allow access to your microphone to record video with audio</string>
</config-file>
```

#### app.scss

Be sure to include the following in your app.scss, or else the camera-preview will not display.

```css
html, body, .ion-app, .ion-content {
  background-color: transparent !important;
}
```

#### Note

LastCam will build and link the underlying framework on it's own, but it will display a few errors within your XCode 
project on run-time. This is purely a cosmetic error, and will not affect the functioning of the plugin. I am working
 on a solution for this in the meantime, but do not worry. The plugin will compile and run without issue. 


## Usage

#### startCamera(options, [successCallback, errorCallback])

<info>Starts the camera preview instance.</info><br/>

<strong>Options:</strong>
All options stated are optional and will default to values here

* `x` - Defaults to 0
* `y` - Defaults to 0
* `width` - Defaults to window.screen.width
* `height` - Defaults to window.screen.height
* `camera` - Defaults to back camera

```javascript
let options = {
  x: 0,
  y: 0,
  width: window.screen.width,
  height: window.screen.height,
  camera: ".Back",
};

CameraPreview.startCamera(options)
.then(() => { })
.catch(error => console.log(error));
```

#### stopCamera([successCallback, errorCallback])

<info>Stops the camera preview instance. </info>

```javascript
CameraPreview.stopCamera();
```

#### switchCamera([successCallback, errorCallback])

<info>
Switches between the front and back camera, if they are available. 

Returns a string containing the camera direction ('front' or 'back')
</info>

```javascript
CameraPreview.switchCamera()
.then(direction => {
    console.log('New camera direction: ', direction)
})
.catch(err => { 
    console.log(err);
});
```

#### switchFlash([successCallback, errorCallback])

Switches the flash mode between `0 (off)`, `1 (on)` and `2(auto)`.

Returns an int containing the flash mode: `0, 1, or 2`

```javascript
CameraPreview.switchFlash()
.then(mode => {
    console.log('New flash mode: ', mode)
})
.catch(err => { 
    console.log(err);
});
```

#### takePicture([successCallback, errorCallback])

<info>Takes a picture</info>

```javascript
CameraPreview.takePicture().then(base64PictureData) {
  /*
    base64PictureData is base64 encoded jpeg image. Use this data to store to a file or upload.
    Its up to the you to figure out the best way to save it to disk or whatever for your application.
  */
};
```

<info>
Here is and example of how you use the `base64PictureData`
</info>
 
```javascript
  imageSrcData = 'data:image/jpeg;base64,' + base64PictureData;
```

Now it can be put into a regular `<img src="imageSrcData">` tag.

<info> 
If you run into issues with the imageSrcData being caught by Angular or Ionic as an unsafeXss, you can do the 
following the fix the error:
</info><br><br>


In your `component.ts` file
```typescript

import {DomSanitizer} from '@angular/platform-browser';
...

constructor(private DomSanitizer: DomSanitizer){
...
}
```

In your `component.html` file
```html
<img [src]="DomSanitizer.bypassSecurityTrustResourceUrl(imageSrcData)">
```

#### startVideoCapture([successCallback, errorCallback])

Starts the video recording session. You will see a brief flicker. This is not a bug.

```javascript
CameraPreview.startVideoCapture().then(() => {
    console.log('Video Capture started');
})
.catch(err => { 
    console.log(err);
});
```

#### stopVideoCapture([successCallback, errorCallback])

Stops the video recording session. You will see a brief flicker. This is not a bug.

Example fileURI that is returned: `/var/mobile/Containers/Data/Application/.../filename.mp4`

```javascript
CameraPreview.startVideoCapture().then(fileURI => {
	  /*
        fileURI is a local path to the video recording on the user's device.
        Its up to the you to figure out the best way display and upload the video source.
      */
})
.catch(err => { 
    console.log(err);
});
```

#### recordingTimer([successCallback, errorCallback])

This returns the current recording time, while actively recording.

```javascript
CameraPreview.recordingTimer().then(time => {
	console.log('Recording length: ', time);
})
.catch(err => { 
    console.log(err);
});
```

## Alternative Plugins

[cordova-plugin-camera-preview](https://github.com/cordova-plugin-camera-preview/cordova-plugin-camera-preview) - 
Custom Preview, but does not allow video capture & has high processing usage.

[cordova-background-video](https://github.com/Neos21/cordova-background-video) - Custom preview, only allows video  capture.

[cordova-plugin-camera](https://github.com/apache/cordova-plugin-camera) - Native camera interface. Allows for Video 
& Photo capture. Limited customization.

[cordova-plugin-video-capture-plus](https://github.com/danielsogl/cordova-plugin-video-capture-plus) - No longer 
maintained. Limited customizations. 

[cordova-plugin-media-capture](https://github.com/apache/cordova-plugin-media-capture) - Last time I checked, this 
had some processing issues & bugs that had not been addressed.

## License

MIT License

Copyright (c) 2018 Jordan Benge

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.

## Credits

LastCam was Created by Jordan Benge [@bengejd](https://github.com/bengejd)

CameraManager, which LastCam utilizes was created by 
imaginary-cloud [@imaginary-cloud](https://github.com/imaginary-cloud/CameraManager)


