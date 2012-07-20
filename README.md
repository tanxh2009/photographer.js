Photographer.js
---------------

HTML5 Apps Shouldn't Require Flash
==================================

For a long time (too long), Flash was required if a web app wanted to capture images through a user's webcam. Things are changing though and bleeding edge browsers now allow access to media devices (video and audio input) through the WebRTC API. Photographer.js is a library built to help abstract away the WebRTC API and make capturing photos in a web app dead simple. 

Example Usage
=============

```html
<html>
  <head>
    <!-- load Photographer.js -->
    <script src='photographer.js'></script>
  </head>
  <body>
    <!-- this element will contain the video element Photographer.js
         pipes the webcam video stream to -->
    <div id='photobooth' style='width: 640px; height: 480px;'></div>

    <!-- when this button is clicked, a photo will be captured -->
    <button id='camera-click'>Take Photo</button>

    <script>
      // DOM references
      var body = document.getElementsByTagName('body')[0];
      var photobooth = document.getElementById('photobooth');
      var cameraClickBtn = document.getElementById('camera-click');

      // this function will get called right before Photographer.js
      // captures a photo. you can use it to provide a custom visual
      // cue to the user that their photo is about to be taken
      var flash = function(container) {
        alert('Say cheese!');
      };

      // create a Photographer instance  
      var photographer = new Photographer({
        flash: flash,
        container: photobooth
      });

      // when the user clicks on the 'Take Photo' button, capture their
      // image and append that image to the body
      takePhotoBtn.onclick = function() {
        var photo = photographer.takePhoto();

        // create the img element to be appended
        var img = new Image();
        img.src = photo.src;
        img.width = photo.width;
        img.height = photo.height;

        // add the image to the body
        body.appendChild(img);
      };
    </script>
  </body>
</html>
```

API
===

### Photographer(config)

```javascript
  var photographer = new Photographer({ 
    container: element
  });
```

The constructor function. It takes one argument, an object containing configuration details. There are 5 configuration properties:

* `container`(DOM element, **required**) - The DOM element the `Photographer` instance will display the live preview video. If this property isn't provided, an error will be thrown.

* `flash`(function, optional, `null` by default) - Before a `Photographer` instance captures an image, it'll call this function if provided. `flash` will have one argument passed to it, the `container` element.

* `imgFormat`(string, optional, `png` by default) - The preferred image format of the captured images.

* `imgWidth`(number, optional, width of `container` by default) - The preferred width of the capture images.

* `imgHeight`(number, optional, height of `container` by default) - The preferred height of the capture images.

### photographer#start()

```javascript
  photographer.start();
```

If the browser supports `navigator.getUserMedia`, this method will return `true` and start piping the video stream from the user's camera device to the live preview video. If the browser doesn't support `navigator.getUserMedia`, `false` will be returned. This method will also throw an error if `navigator.getUserMedia` results in an error.

### photographer#stop()

```javascript
  photographer.stop();
```

If the browser supports `navigator.getUserMedia`, this method will stop the video stream from the user's camera device and return `true`. If the browser doesn't support `navigator.getUserMedia`, `false` will be returned.

### photographer#takePhoto()

```javascript
  var photo = photographer.takePhoto();
```

If the browser supports `navigator.getUserMedia` and calling `photographer.start()` was successful, `photographer.takePhoto()` will capture an image and return a photo object (see below for more info on the photo object). If a `flash` function was configured, that function will get called right before the image is captured.

If the browser doesn't support `navigator.getUserMedia`, this method will return `false`.

The photo object `photographer.takePhoto` returns contains the following properties: 

* `src`(string) - The base64 encoding of the captured image.

* `format`(string) - The format the image was captured as. 

* `width`(number) - The width of the captured image.

* `height`(number) - The height of the captured image.

### photographer#getPhotos()

```javascript
  var photos = photographer.getPhotos();
```

Each `Photographer` instance stores an array of all of the photos it has taken. `photographer.getPhotos()` will return a copy of that array.

Side Effects
============

Loading Photographer.js will result in 3 potential side-effects:

1. `Photographer` will be added to the global namespace.

2. If `window.URL` is browser prefixed, it'll be normalized. 

3. If `navigator.getUserMedia` is browser prefixed, it'll also be normalized.

Bugs and Limitations
====================

Photographer.js is pretty much useless in browsers that don't support the WebRTC API. Unfortunately most browsers fall into that category right now, but hopefully more and more browsers will pick up support as time goes on.

License
=======

Copyright (c) 2012 Jake Harding
Licensed under the MIT license.
[https://raw.github.com/jharding/photographer.js/master/LICENSE-MIT](https://raw.github.com/jharding/photographer.js/master/LICENSE-MIT)