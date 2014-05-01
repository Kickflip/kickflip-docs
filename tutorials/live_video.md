# Adding Live Video Broadcasting To Your Apps

## Overview

## Starting a Broadcast

## Ending a Broadcast

## Playback

### Native

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

To ensure larger playback compatibility for browsers such as Firefox and Internet Explorer, you'll need to use a Flash-based player for your streams. Fortunately, there are now free and open source flash players which can process Kickflip HLS streams. Note that many of these free flash players are still under active development. There are also

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