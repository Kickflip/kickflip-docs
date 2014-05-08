![Nollie heel indy.](http://i.imgur.com/Q4KTloe.gif)

Kickflip API Docs
=============

Documentation for the web Kickflip API.
http://i.imgur.com/WzMG5mv.gif
## Setting up

Before you use the Kickflip API, you must first get your access tokens from the [Kickflip dashboard](https://kickflip.io/dash/).

## Authentication

Kickflip uses OAuth2 as an authentication protocol.

Whatever OAuth2 library that you're using should be smart enough to understand what it needs to do simply by pointing it
to

    https://kickflip.io/o/

as the appropriate OAuth2 endpoint.

## Users

All Kickflip streams must be started by Kickflip users. Users are necessary to make sure that people cannot overwrite
each other . Users can be ephemeral, and clients can create new users as much as they like. However, in most situations
a single user per client will suffice.

### Creating a New User

To create a new user, call

    https://kickflip.io/api/1.0/user/new/

You can also POST an optional **username**, **email**, **password**, **display_name**, and **extra_info** to define a user's account information. If a username is not supplied, one will be randomly generated.

Note that all responses will be in JSON format.

Sample response:

```javascript
{
   "aws_secret_key":"5R/B7GiXXXXXXmR13Tk6HlTz95hQNnuHFd2dP/R",
   "aws_access_key":"AKIAJREVXXXEOMGZ3A",
   "app":"kickflip-ios-123",
   "name":"kickflip-ios-123-w3pi7o",
   "uuid":"26ac0eea-14c1-4d29-afd7-5d8576aXc1ea",
   "success":true
}
```

### Changing a User's Information

To change any information about a user, call this URL

    https://kickflip.io/api/1.0/user/change/

and supply a **password**. You can then also define **email**, **display_name**, **extra_info** and **new_password**.

### Getting a User's Info

Get the _publicly available_ information about any user, call this URL

    https://kickflip.io/api/1.0/user/info/

and supply a **'username'**.

### Getting a User's Keys

If you wish to persist a user's account accross devices, you'll need to supply a correct **username** and **password** to this endpoint, 

    https://kickflip.io/api/1.0/user/uuid/

which will return all of the necessary credentials to use the API and the upload endpoints.

## Streams

### Starting a new Stream

Kickflip clients must call the start stream endpoint in order to get the most recent access tokens and the instructions
for the stream parameters.

To start a stream, first call:

    https://kickflip.io/api/1.0/stream/start/

supplying the user's **uuid**, as well as an optional **lat**, **lon**, **city**, **state** and **country** for geo-coded streams, and an optional **private** for private streams.

Sample response:

```javascript
{
   "aws_secret_key":"5R/B7GiAzXXXkGmR13Tk6HlTz95hQNnuHFd2dP/R",
   "aws_access_key":"AKIAJREVSXXXXQEOMGZ3A",
   "success":true,
   "chat_url":"Not implemented yet",
   "stream_type":"HLS",
   "kickflip_url":"http://kickflip.io/cd29739a-a9da-4deb-ba9c-e8f9f2a72c85",
   "bucket_name":"kickflip-ios-123",
   "stream_id":"cd29739a-a9da-4deb-ba9c-e8f9f2a72c85",
   "stream_url":"https://kickflip-ios-123.s3.amazon.com/kickflip-ios-123-w3pi7o/cd29739a-a9da-4deb-ba9c-e8f9f2a72c85/stream.m3u8",
   "upload_url":"https://kickflip-ios-123.s3.amazon.com/kickflip-ios-123-w3pi7o/cd29739a-a9da-4deb-ba9c-e8f9f2a72c85/"
}
```

### Pausing

Kickflip clients may 'pause' a live stream to indicate that they will resume this same stream later.

To pause a stream, call:

    https://kickflip.io/api/1.0/stream/pause/

Clients must include a user's **uuid** and **stream\_id**, and may include an optional **lat** and **lon** to update the location of the stream.

### Resuming

Similarly, clients can 'resume' a live stream.

To resume a stream, call:

    https://kickflip.io/api/1.0/stream/resume/

Clients must include a user's **uuid** and **stream\_id**, and may include an optional **lat** and **lon** to update the location of the stream.

### Stopping

Streams which are finished must call:

    https://kickflip.io/api/1.0/stream/stop/

Clients must include a user's **uuid** and **stream\_id**, and may include an optional **lat** and **lon** to define the end of a stream location.

### Updating a Stream

Information about a stream can be set via the stream change endpoint:

    https://kickflip.io/api/1.0/stream/change/

Which will set a stream's **start_lat**, **start_lon**, **end_lat**, **end_lon**, **city**, *state**, **country**, **private**, **title**, **description**, **extra_info**, **thumbnail_url** and **deleted** fields.

### Stream Information

Information about a public stream can be retreived via the stream info endpoint:

    https://kickflip.io/api/1.0/stream/info/

for any public stream which you send a **stream_id** for.

### Flagging

Streams which are finished must call:

    https://kickflip.io/api/1.0/stream/flag/

If users wish to report inappropriate streams, they may do so by calling the flag endpoint with any valid **stream_id** to increase the flag count of that object. Users may only flag a Stream once per stream.

## Search and Feeds

Kickflip also provides API end points for searching publicly available streams associated with an App.

### Basic Search

To search all streams, simply call:

     https://kickflip.io/api/1.0/search/

to receive a paginated list of all streams. Adding a **keyword** field to the POST request will filter all streams with that value. Adding a **results_per_page** field will set the pagination number, and **page** will get the page offset. The resulting streams will be returned as a list under the **streams** key.

### User Search

To get all streams associated with a user, simply call:

    https://kickflip.io/api/1.0/search/user/

with a valid **username**.

### Location Search

To get all streams associated with a location, call:

    https://kickflip.io/api/1.0/search/location/

with a valid **lat**, **lon** and a **radius** (in degrees).