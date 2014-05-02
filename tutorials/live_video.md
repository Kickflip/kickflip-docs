# How to Add Live Video Broadcasting To Your iOS and Android Apps

![Kickflip live broadcast screenshot](http://i.imgur.com/ELljE1a.jpg)

## Overview

This guide will show you everything you need to add simple live video broadcasting  to your iOS and Android applications with the [Kickflip](https://kickflip.io) SDK.

All Kickflip streams are backed by the cloud, so they'll be able to be viewed by thousands of people simultaneously with no loss of quality. They're also stored for later viewing.

This document assumes you've registered a Kickflip application and included the Kickflip SDK in your iOS or Android project as covered in the [Getting Started](https://github.com/Kickflip/kickflip-docs/blob/master/tutorials/getting_started.md) guide.

## Imports
Once you're all set up, you'll first need to import the Kickflip SDK into your projects like so:

##### iOS
```objc
#import "Kickflip.h"
```

##### Android
```java
import io.kickflip.sdk.Kickflip;
import io.kickflip.sdk.av.BroadcastListener;
```

## Providing your Kickflip Client ID and Secret

Next, from your Kickflip dashboard, copy your Application's CLIENT_ID and CLIENT_SECRET into your apps.

##### iOS
```objc
[Kickflip setupWithAPIKey:@"CLIENT_ID" secret:@"CLIENT_SECRET"];
```

##### Android
```java
Kickflip.setup(this, "CLIENT_ID", "CLIENT_SECRET");
```
Where `this` is your host `Activity` instance.


## Starting a Broadcast

These snippets will show the Kickflip broadcasting UI and allow a user to start, share and then stop one live broadcast. You should likely tie these snippets to an interaction event such as a button press.

##### iOS
```objc
[Kickflip presentBroadcasterFromViewController:self ready:^(KFStream *stream, NSError *error){ 
    if (stream) {
    	NSLog(@"Stream is ready to view at URL: %@", stream.streamURL);
    }
} 
completion:nil];
```

##### Android

```java
Kickflip.startBroadcastActivity(this, new BroadcastListener() {
    @Override
    public void onBroadcastStart() {
		// The user began broadcasting
    }

    @Override
    public void onBroadcastLive(Stream stream) { 
        // This device's broadcast is live!
        // The kickflip.io HTML5 / Flash player URL for this stream is:
        // stream.getKickflipUrl();
        // The raw stream address (.m3u8 file for HLS streams) is:
        // stream.getStreamUrl();     
    }

    @Override
    public void onBroadcastStop() {
		// The user stopped broadcasting.
		// The BroadcastActivity will be finished after this call
		// completes.
    }

    @Override
    public void onBroadcastError(KickflipException error) {
    	Log.i("Kickflip" "An error occurred: " + error.getMessage());
    }
});

```

## Ending a Broadcast
A broadcast is ended when the user touches the stop button revealed in the broadcasting UI. This will end the life of your broadcaster view.

## Playback

Your app will likely also need to watch the live and previously recorded streams of other users. This code will allow you to play the streams natively inside your applications, or on the web with native web players and flash players.

### Native

##### iOS
```objc
// TODO
```

##### Android
```java
// TODO
```

### Web

All Kickflip API responses will include a "kickflip_url" field, which will include a link to a page which includes an embedded web player for your stream so that you can share live video links without needing your own web interface.

Of course, you may want to embed your streams directly onto your own website. Here's how to do just that.

###### A note on web playback support

By default, Kickflip streams are in HTTP Live Streaming (HLS) format, using the h.264 video codec and the AAC audio codec. Native support for these codecs and and formats varies wildly across devices. This may be a source of trouble for you, so this guide will attempt to show both native and non-native playback solutions. It is up for you to decide how you will use your streams on your own platform.

#### Native Web Playback

Some browsers, such as Safari on OSX and Mobile Safari on iOS, can play Kickflip streams using the standard HTML5 elements. 

```html
<video controls autoplay id="video_player_0" class="video_player">
    <source id="v_source" src="https://YOUR_STREAM_URL.m3u8" type="application/x-mpegURL">
</video>
```

Since it's all just HTML, you can also style your elements using normal CSS.

#### Playing HLS Live Video Streams with Flash

To ensure larger playback compatibility for browsers such as Firefox and Internet Explorer, you'll need to use a Flash-based player for your streams. Fortunately, there are now free and open source flash players which can process Kickflip HLS streams.

We recommend using Adobe's free and open source [Open Source Media Framework (OSMF)](http://streambox.fr/HLSProvider/osmf/StrobeMediaPlayback.html) player with the free [HLSProvider](https://github.com/mangui/HLSprovider) plugin. HLSProvider also works with JWPlayer5, JWPlayer6, Flowplayer, GrindPlayer, and chromeless without any additional player at all. 

To install HLSProvider, [download the latest release](http://mangui.github.io/HLSprovider/) from the HLSProvider project and unpack it into your web service's static assets directory. You'll also need to include two scripts in the header of your page:

```html
<script type="text/javascript" src="YOUR_STATIC_FOLDER/js/swfobject.js">
        </script>
<script type="text/javascript" src="YOUR_STATIC_FOLDER/js/ParsedQueryString.js">
        </script>
```

Then add this script in a script tag to the bottom of your page:

```javascript
function jsbridge(playerId, event, data) {
  switch(event) {
     case "timeChange":
     case "timeupdate":
     case "progress":
       break;
     default:
      console.log(event, data);
    }
}

function getParameterByName(name) {
    name = name.replace(/[\[]/, "\\\[").replace(/[\]]/, "\\\]");
    var regex = new RegExp("[\\?&]" + name + "=([^&#]*)"),
        results = regex.exec(location.search);
    return results == null ? "" : decodeURIComponent(results[1].replace(/\+/g, " "));
}

// Collect query parameters in an object that we can
// forward to SWFObject:

var v_id = getParameterByName('v');
var src_string = "https://YOUR_STREAM_URL.m3u8";
var pqs = new ParsedQueryString();
var parameterNames = pqs.params(false);
var parameters = {
    src: src_string,
    autoPlay: "true",
    verbose: true,
    controlBarAutoHide: "true",
    controlBarPosition: "bottom",
    poster: "https://YOUR_STREAM_THUMBNAIL_URL.jpg",
    javascriptCallbackFunction: "jsbridge",
    plugin_hls: "YOUR_STATIC_FOLDER/swf/HLSProviderOSMF.swf"
};

for (var i = 0; i < parameterNames.length; i++) {
    var parameterName = parameterNames[i];
    parameters[parameterName] = pqs.param(parameterName) ||
    parameters[parameterName];
}

var wmodeValue = "direct";
var wmodeOptions = ["direct", "opaque", "transparent", "window"];
if (parameters.hasOwnProperty("wmode"))
{
  if (wmodeOptions.indexOf(parameters.wmode) >= 0)
  {
    wmodeValue = parameters.wmode;
  }               
  delete parameters.wmode;
}

// Embed the player SWF:              
swfobject.embedSWF(
  "YOUR_STATIC_FOLDER/swf/StrobeMediaPlayback.swf"
  , "StrobeMediaPlayback"
  , "100%" // width
  , 370 // height
  , "10.1.0"
  , "YOUR_STATIC_FOLDER/swf/expressInstall.swf"
  , parameters
  , {
            allowFullScreen: "true",
            wmode: wmodeValue,
            allowscriptaccess: "always",
            scale: "exactFit",
            menu: false
        }
  , {
            name: "StrobeMediaPlayback"
        }
);

```

Be sure to replace *YOUR_STATIC_FOLDER*, *YOUR_STREAM_URL.m3u8* and *YOUR_STREAM_THUMBNAIL_URL.jpg*.

This should then give you a player which looks something like this:

![The handsome @OnlyInAmerica sporting the latest in web video fashion.](https://i.imgur.com/MjbBm9i.png)

Tada!

## Next Steps

If you're interested in adding more advanced features to your app, such as user account management, stream searching, geotagging and more, check out the [building your own streaming video service guide](cloning_livestream.md).
