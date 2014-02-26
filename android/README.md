Kickflip Android SDK
=============

This document provides a more in-depth look at the Kickflip Android SDK. 

See the [kickflip-android-sdk](https://github.com/Kickflip/kickflip-android-sdk) project for [QuickStart](https://github.com/Kickflip/kickflip-android-sdk#quickstart) and [Build instructions](https://github.com/Kickflip/kickflip-android-sdk#building-this-project).

## What is the Kickflip Android SDK?

The Kickflip Android SDK strives to make streaming video from an Android device to your [kickflip.io](http://kickflip.io) account a joy.

Beyond a kickflip.io client, this SDK is a collection of modular audio and video components that greatly ease developing video products for the Android platform. Our development focus is currently live streaming, but the great majority of this SDK is agnostic to that particular cause.


## Architecture

### Activities and Fragments

[`BroadcastActivity`](https://github.com/Kickflip/kickflip-android-sdk/blob/preview/sdk/src/main/java/io/kickflip/sdk/BroadcastActivity.java) hosts a single [`BroadcastFragment`](https://github.com/Kickflip/kickflip-android-sdk/blob/preview/sdk/src/main/java/io/kickflip/sdk/fragment/BroadcastFragment.java) which manages every aspect of the broadcasting use-case for you. `BroadcastFragment` take care of passing Activity lifecycle hooks to the underlying classes and is a great reference if you decide to directly interface with the base classes.

### Recording (The Full Stack)

[`Broadcaster`](https://github.com/Kickflip/kickflip-android-sdk/blob/preview/sdk/src/main/java/io/kickflip/sdk/av/Broadcaster.java) and [`AVRecorder`](https://github.com/Kickflip/kickflip-android-sdk/blob/preview/sdk/src/main/java/io/kickflip/sdk/av/AVRecorder.java) are the high-level recording classes with APIs that are limited to starting, stopping, and a few other configuration methods. Think of `AVRecorder` as a supercharged version of Android's [`MediaRecorder`](http://developer.android.com/reference/android/media/MediaRecorder.html). `Broadcaster` extends `AVRecorder` and handles streaming to your Kickflip account transparently. 

### Encoding

`CameraEncoder` and `MicrophoneEncoder` abstract the management of device hardware like the Camera and Microphone behind a simple API for configuring, starting, and stopping.

Their parent classes are `VideoEncoderCore` and `AudioEncoderCore`, respectively. These two classes handle video/audio specific configuration of `AndroidEncoder`: our wrapper around Android's `MediaCodec`.

Under the hood, we use Android's `MediaCodec` class for encoding H.264 Video and AAC audio with hardware acceleration. Due to the performance requirements of real-time HD encoding, it's not currently feasible to leverage FFmpeg as a software encoder.

### Muxing

The Muxer is the heart of any recording configuration. Every recording session synchronizes on a single Muxer, which handles combining all Encoder outputs into a comprehensible output format.

Kickflip currently includes `FFmpegMuxer` and `AndroidMuxer`, which both implement the `Muxer` interface and thus can be used interchangeably. `AndroidMuxer` employs Android's built-in `MediaMuxer` and supports only MPEG-4 output. `FFmpegMuxer` harnesses the power of FFmpeg to write Encoder data to a variety of outputs such as HLS streams (MPEG-TS segments and a .m3u8 manifest file), RTMP streams (Flash Video written directly to an `rtmp://` endpoint), as well as local MPEG-4 files. `FFmpegMuxer` can be further developed to write most any output format that supports H.264 video and AAC audio.