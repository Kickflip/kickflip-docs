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

#### Native
#### Web

