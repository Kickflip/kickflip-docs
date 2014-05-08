Kickflip Android SDK
=============

Documentation for the Android Kickflip API.

## Setting up

Before you use the Kickflip API, you must first get your access tokens from the [Kickflip dashboard](https://kickflip.io/dash/).

## Authentication

Kickflip uses OAuth2 as an authentication protocol. The Android SDK handles this for you during the setup phase.

```java
Kickflip.setup(this, "CLIENT_ID", "CLIENT_SECRET");
```

The `setup` method returns a `KickflipApiClient` initialized with the provided credentials. You can also retrieve the client at any time following with `Kickflip#getApiClient(Context context)`

## Users

All Kickflip streams must be started by Kickflip users. Users are necessary to make sure that people cannot overwrite each other. Users can be ephemeral, and clients can create new users as much as they like. However, in most situations
a single user per client will suffice.

The Kickflip Android client will automatically request a new user on launch if one is not found. You can request a more permanent user with specific information as well.

### Creating a New User

You can attach additional information in the `extraInfo` field. If a username is not supplied, one will be randomly generated on the server. You can use this info to link to your own user account system, or use ours.

```java
KickflipApiClient apiClient = Kickflip.getApiClient(this);
apiClient.createNewUser(
		"username", 
		"password", 
		"Any other key/values you would want", 
		new KickflipCallback() {
            @Override
            public void onSuccess(Response response) {
            	User newUser = (User) response;
            	Log.i("Hooray", "You've got a new user: " + newUser.getName());
            }

            @Override
            public void onError(KickflipException error) {
                Log.w(TAG, "createNewUser Error: " + error.getMessage());
            }
        }
);
```


### Changing a User's Information

To change any information about a user:

```java
KickflipApiClient apiClient = Kickflip.getApiClient(this);
apiClient.setUserInfo("newPassword", "email", "displayName", "extraInfo", 
	new KickflipCallback() {
        @Override
        public void onSuccess(Response response) {
        	User newUser = (User) response;
        	Log.i("Hooray", "You modified user: " + newUser.getName());
        }

        @Override
        public void onError(KickflipException error) {
            Log.w(TAG, "setUserInfo Error: " + error.getMessage());
        }
    });
```

### Getting a User's Info

Get the _publicly available_ information about any user:

```java
KickflipApiClient apiClient = Kickflip.getApiClient(this);
apiClient.getUserInfo("userName" 
	new KickflipCallback() {
        @Override
        public void onSuccess(Response response) {
       		User newUser = (User) response;
        	Log.i("Hooray", "You modified user: " + newUser.getName());
        }

        @Override
        public void onError(KickflipException error) {
            Log.w(TAG, "setUserInfo Error: " + error.getMessage());
        }
    });

```

### Getting an Existing User's Keys

If you wish to persist a user's account accross devices, you'll need to supply a correct **username** and **password**:

```java
KickflipApiClient apiClient = Kickflip.getApiClient(this);
apiClient.loginUser("userName", "password",
	new KickflipCallback() {
        @Override
        public void onSuccess(Response response) {
        	User newUser = (User) response;
        	Log.i("Hooray", "You logged in as: " + newUser.getName());
        }

        @Override
        public void onError(KickflipException error) {
            Log.w(TAG, "loginUser Error: " + error.getMessage());
        }
    });

```

This will return all of the necessary credentials to use the API and the upload endpoints.

## Streams

Normally you won't have to worry about manually starting and stopping streams because it is all handled for you by `KFBroadcastViewController`.

### Starting a new Stream

Kickflip clients must call the start stream endpoint in order to get the most recent access tokens and the instructions for the stream parameters.

To start a stream, first call:

```java
KickflipApiClient apiClient = Kickflip.getApiClient(this);
apiClient.startStream(newStream,
	new KickflipCallback() {
        @Override
        public void onSuccess(Response response) {
        	Stream newStream = (Stream) response;
        	Log.i("Hooray", "You started stream " + stream);
        }

        @Override
        public void onError(KickflipException error) {
            Log.w(TAG, "startStream Error: " + error.getMessage());
        }
    });

```

### Stopping

```java
KickflipApiClient apiClient = Kickflip.getApiClient(this);
apiClient.stopStream(stream,
	new KickflipCallback() {
        @Override
        public void onSuccess(Response response) {
        	Stream newStream = (Stream) response;
        	Log.i("Hooray", "You stopped stream " + stream);
        }

        @Override
        public void onError(KickflipException error) {
            Log.w(TAG, "stopStream Error: " + error.getMessage());
        }
    });

```

### Updating a Stream

Information about a stream can be set via the stream change endpoint:

```java
KickflipApiClient apiClient = Kickflip.getApiClient(this);
apiClient.setStreamInfo(updatedStream,
	new KickflipCallback() {
        @Override
        public void onSuccess(Response response) {
        	Stream newStream = (Stream) response;
        	Log.i("Hooray", "You updated stream " + stream);
        }

        @Override
        public void onError(KickflipException error) {
            Log.w(TAG, "setStreamInfo Error: " + error.getMessage());
        }
    });

```

Note, this won't update the stream object, it will return a new updatedStream object.


## Search and Feeds

Kickflip also provides API end points for searching publicly available streams associated with an App.

### Basic Search

To search all streams, simply call the below method with no keyword:

```java
KickflipApiClient apiClient = Kickflip.getApiClient(this);
apiClient.getStreamsByKeyword(null,
	new KickflipCallback() {
        @Override
        public void onSuccess(Response response) {
        	List<Stream> streams = (List<Stream>) response;
        	Log.i("Hooray", "You got " + streams.size() + " streams");
        }

        @Override
        public void onError(KickflipException error) {
            Log.w(TAG, "getStreamsByKeyword Error: " + error.getMessage());
        }
    });

```

Otherwise, you can search streams by keyword by passing in a value.

### User Search

To get all streams associated with a user, simply call:


```java
KickflipApiClient apiClient = Kickflip.getApiClient(this);
apiClient.getStreamsByUser("username",
	new KickflipCallback() {
        @Override
        public void onSuccess(Response response) {
        	List<Stream> streams = (List<Stream>) response;
        	Log.i("Hooray", "You got " + streams.size() + " streams");
        }

        @Override
        public void onError(KickflipException error) {
            Log.w(TAG, "getStreamsByUser Error: " + error.getMessage());
        }
    });
```

### Location Search

Sometimes you want to find all streams within a certain geographic region. Use a [Location](http://developer.android.com/reference/android/location/Location.html) and an int radius in meters as your parameters.

```java
KickflipApiClient apiClient = Kickflip.getApiClient(this);
apiClient.getStreamsByLocation(currentLocation, 5000
	new KickflipCallback() {
        @Override
        public void onSuccess(Response response) {
        	List<Stream> streams = (List<Stream>) response;
        	Log.i("Hooray", "You got " + streams.size() + " streams");
        }

        @Override
        public void onError(KickflipException error) {
            Log.w(TAG, "getStreamsByLocation Error: " + error.getMessage());
        }
    });
```
