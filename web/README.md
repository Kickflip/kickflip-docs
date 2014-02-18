![http://i.imgur.com/WzMG5mv.gif](This would be funnier if we used Rails.)

Kickflip API Docs
=============

Documentation for the web Kickflip API.

## Setting up

Before you use the Kickflip API, you must first get your access tokens from the Kickflip dashboard.

## Authentication

Kickflip uses OAuth2 as an authentication protocol.

Whatever OAuth2 library that you're using should be smart enough to understand what it needs to do simply by pointing it
to

    https://app.kickflip.io/o/

as the appropriate OAuth2 endpoint.

## Users

All Kickflip streams must be started by Kickflip users. Users are necessary to make sure that people cannot overwrite
each other . Users can be ephemeral, and clients can create new users as much as they like. However, in most situations
a single user per client will suffice.

To create a new user, call

    https://app.kickflip.io/new/user/

You can also POST an optional **username** to define a username, otherwise one will be randomly generated.

Note that all responses will be in JSON format.

## Streams

### Starting a new Stream

Kickflip clients must call the start stream endpoint in order to get the most recent access tokens and the instructions
for the stream parameters.

To start a stream, first call:

    https://app.kickflip.io/stream/start/

supplying the user's **uuid**, as well as an optional **lat** and **lon** for geo-coded streams, and an optional
**private** for private streams.

### Pausing

Kickflip clients may 'pause' a live stream to indicate that they will resume this same stream later.

To pause a stream, call:

    https://app.kickflip.io/stream/pause/

Clients must include a user's uuid and stream\_id, and may include an optional *lat* and *lon* to update the location of the stream.

### Resuming

Similarly, clients can 'resume' a live stream.

To resume a stream, call:

    https://app.kickflip.io/stream/resume/

Clients must include a user's uuid and stream\_id, and may include an optional *lat* and *lon* to update the location of the stream.

### Stopping

Streams which are finished must call:

    https://app.kickflip.io/stream/stop/

Clients must include a user's uuid and stream\_id, and may include an optional *lat* and *lon* to define the end of a stream location. 
