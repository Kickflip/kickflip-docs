# Adding Live Video Broadcasting To Your Apps

## Overview

This document assumes you've created a Kickflip app and included the Kickflip SDK in your iOS or Android project as covered in the [Getting Started](https://github.com/Kickflip/kickflip-docs/blob/master/tutorials/getting_started.md) guide.

## Providing your Kickflip Client ID and Secret

First make sure you setup Kickflip with the Client ID and Secret available from your Kickflip account dashboard.

**iOS**
```objc
[Kickflip setupWithAPIKey:@"CLIENT_ID" secret:@"CLIENT_SECRET"];
```

**Android**
```java
Kickflip.setup(this, "CLIENT_ID", "CLIENT_SECRET");
```
Where `this` is your host `Activity` instance.


## Starting a Broadcast

Show the Kickflip broadcasting UI and allow a user to start and then stop one live broadcast.

**iOS**
```objc
[Kickflip presentBroadcasterFromViewController:self ready:^(KFStream *stream, NSError *error){ 
    if (stream) {
    	NSLog(@"Stream is ready to view at URL: %@", stream.streamURL);
    }
} 
completion:nil];
```
**Android**

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
A broadcast is ended when the user touches the stop button revealed in the broadcasting UI.

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

To ensure larger playback compatibility for browsers such as Firefox and Internet Explorer, you'll need to use a Flash-based player for your streams. Fortunately, there are now free and open source flash players which can process Kickflip HLS streams.
