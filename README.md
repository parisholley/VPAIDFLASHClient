About
===============
JS + Flash wrapper for [VPAID](http://www.iab.net/vpaid).

[VPAID](http://www.iab.net/vpaid) or *Video Player Ad-Serving Interface Definition*, establishes a common interface between video players and ad units, enabling a rich interactive in-stream ad experience.

Because some of the ad's are in flash, this projects will allow to expose the api to be used by js.

**JS** is responsible of:
  - create a **uniqueid** for the vpaid and request swfobject to load the swf
  - to do the bridge between vpaid api between flash and js using [ExternalInterface](http://help.adobe.com/en_US/FlashPlatform/reference/actionscript/3/flash/external/ExternalInterface.html)
  - for security reasons the swf will not be allowed to communicate with flash if not excuting in the server

**Flash** is responsible of:
  - load the ad Unit
  - expose ad Unit interface to outside
  - using the **uniqueid** to identify the swf

TODO
===============
  - try to implement flex unit tests
  - make unit test to js api - [started]

JS
==

The project uses:
  - [gulpjs](http://gulpjs.com/) to compile.
  - [babeljs](https://babeljs.io) to convert es6 to es5 js code.

3rd party
---------
  - [swfobject googlecode](https://code.google.com/p/swfobject/) or [swfobject github](https://github.com/swfobject/swfobject)

Flash
==============

The flash code was compiled with [FlashBuilder](http://www.adobe.com/uk/products/flash-builder.html), but can be as compiled with [FlashDevelop](http://www.flashdevelop.org/) or with [Flex SDK](http://www.adobe.com/devnet/flex/flex-sdk-download.html).

3rd party
---------
  - [BulkLoader](https://github.com/arthur-debert/BulkLoader) - BulkLoader is a minimal library written in Actionscript 3 (AS3) that aims to make loading and managing complex loading requirements easier and faster

Debug
=====
  - when creating a flashVPAID instance the last parameter is debug, if this flag is set to true, will create a visual textfield that allows to show errors
  - as well if the js+flash is working all errors catch by the VPAIDFlash.swf will be sent to js
  - other error's can be caugth with Flash debugger

Flash debugger
--------------
Allows to flash to throw errors in a popup like window. To install go to [adobe support page](https://www.adobe.com/support/flashplayer/downloads.html) and install the flash player with **content debugger** in the name for the correct OS.
PS:
  - in chrome because it has a special version of flash is a *pain* to install, so install for firefox version.

Running the project
===================

  - install nodejs, bower and gulp
  - `npm install` and `bower install` to install all dependencies
  - `gulp serve` to start build script and a demo page should be open in default browser

Example of the usage
==========================================

```javascript
var flashVPaid = new FlashVPAID(myContent, flashVPAIDWrapperLoaded);
function flashVPAIDWrapperLoaded(err, result) {
    if (err) {
        //handle error here
        return;
    }

    flashVPaid.loadAdUnit('TestAd.swf', function (error, creative) {

        if (err) {
            //handle error here
            return;
        }

        creative.on('AdLoaded', function (err, result) {
            console.log('event:AdLoaded', err, result);
            startAd();
        });

        creative.on('AdStarted', function (err, result) {
            console.log('event:AdStarted', err, result);
            checkAdProperties();
        });

        creative.handshakeVersion('2.0', function (err, result) {
            initAd();
        });

        function initAd() {
            creative.initAd('normal', -1, 0, 0, '', '', function (err) {
                console.log('initAd', err);
            });
        }

        function startAd() {
            creative.startAd(function (err, result) {
                console.log('startAd call', err, result);
            });
        }

        function checkAdProperties() {
            creative.adIcons(function (err, result) {
                console.log('adIcons', result);
            });
            creative.setAdVolume(10, function (err, result) {
                console.log('setAdVolume', result);
            });
            creative.getAdVolume(function (err, result) {
                console.log('getAdVolume', result);
            });
        }

    });
}
```

for the api of flashVPAID check [flashVPAID](js/flashVPAID.js), for creative api check [VAPAID](js/VPAID.js).

