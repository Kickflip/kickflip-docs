# Adding Live Video Broadcasting To Your Apps

## Overview

## Starting a Broadcast

## Ending a Broadcast

## Playback

#### Native

#### Web

All Kickflip API responses will include a "kickflip_url" field, which will include a link to a page which includes an embedded web player for your stream so that you can share live video links without needing your own web interface.

Of course, you may want to embed your streams directly onto your own website. Here's how to do just that.

###### A note on playback support

By default, Kickflip streams are in HTTP Live Streaming (HLS) format, using the h.264 video codec and the AAC audio codec. Native support for these codecs and and formats varies wildly across devices. This may be a source of trouble for you, so this guide will attempt to show both native and non-native playback solutions. It is up for you to decide how you will use your streams on your own platform.

##### Native Playback

Some browsers, such as Safari on OSX and Mobile Safari on iOS, can play Kickflip streams using the standard HTML5 elements. 

```html
<video controls autoplay id="video_player_0" class="video_player">
    <source id="v_source" src="https://YOUR_STREAM_URL.m3u8" type="application/x-mpegURL">
</video>
```

Since it's all just HTML, you can also style your elements using normal CSS.

##### Playing HLS Live Video Streams with Flash

To ensure larger playback compatibility for browsers such as Firefox and Internet Explorer, you'll need to use a Flash-based player for your streams.