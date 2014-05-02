Kickflip iOS SDK
=============

Documentation for the iOS Kickflip API.

## Setting up

Before you use the Kickflip API, you must first get your access tokens from the [Kickflip dashboard](https://kickflip.io/dash/).

## Authentication

Kickflip uses OAuth2 as an authentication protocol. The iOS SDK handles this for you during the setup phase.

```obj-c
[Kickflip setupWithAPIKey:KICKFLIP_API_KEY secret:KICKFLIP_API_SECRET];
```


## Users

All Kickflip streams must be started by Kickflip users. Users are necessary to make sure that people cannot overwrite each other. Users can be ephemeral, and clients can create new users as much as they like. However, in most situations
a single user per client will suffice.

The Kickflip iOS client will automatically request a new user on launch if one is not found. You can request a more permanent user with specific information as well.

### Creating a New User

You can attach additional information in the `extraInfo` field. If a username is not supplied, one will be randomly generated on the server. You can use this info to link to your own user account system, or use ours.

```obj-c
#import "KFAPIClient.h"
    [[KFAPIClient sharedClient] requestNewActiveUserWithUsername:@"bob" email:@"bob@example.com" password:@"secret password" displayName:@"Bob Jones" extraInfo:@{@"otherInfo": @"Any other key/values you would want"} callbackBlock:^(KFUser *activeUser, NSError *error) {
        if (activeUser) {
            NSLog(@"great, you've got a new user!");
        }
    }];
```


### Changing a User's Information

To change any information about a user:

```obj-c
    [[KFAPIClient sharedClient] updateMetadataForActiveUserWithNewPassword:nil email:@"test@example.com" displayName:@"Bob Jones II" extraInfo:@{@"otherInfo": @"Any other key/values you would want"}  callbackBlock:^(KFUser *updatedUser, NSError *error) {
        if (updatedUser) {
            NSLog(@"great, you've got updated a user!");
        }
    }];
```

You can also supply `newPassword` when changing an existing user's password.

### Getting a User's Info

Get the _publicly available_ information about any user:

```obj-c
    [[KFAPIClient sharedClient] requestUserInfoForUsername:@"existing-username" callbackBlock:^(KFUser *existingUser, NSError *error) {
        if (existingUser) {
            NSLog(@"you got info for an existing user!");
        }
    }];
```

### Getting an Existing User's Keys

If you wish to persist a user's account accross devices, you'll need to supply a correct **username** and **password**:

```obj-c
    [[KFAPIClient sharedClient] loginExistingUserWithUsername:@"existing-username" password:@"password" callbackBlock:^(KFUser *existingUser, NSError *error) {
        if (existingUser) {
            NSLog(@"successfully logged in existing user");
        }
    }];
```

This will return all of the necessary credentials to use the API and the upload endpoints.

## Streams

Normally you won't have to worry about manually starting and stopping streams because it is all handled for you by `KFBroadcastViewController`.

### Starting a new Stream

Kickflip clients must call the start stream endpoint in order to get the most recent access tokens and the instructions for the stream parameters.

To start a stream, first call:

```obj-c
[[KFAPIClient sharedClient] startNewStream:^(KFStream *newStream, NSError *error) {
        if (newStream) {
            NSLog(@"it's a new stream ready for public broadcast!");
        }
    }];
```

### Stopping

```obj-c
    [[KFAPIClient sharedClient] stopStream:stream callbackBlock:^(BOOL success, NSError *error) {
        if (success) {
            NSLog(@"Stream stopped");
        }
    }];
```

### Updating a Stream

Information about a stream can be set via the stream change endpoint:

```obj-c
    [[KFAPIClient sharedClient] updateMetadataForStream:stream callbackBlock:^(KFStream *updatedStream, NSError *error) {
        if (updatedStream) {
            NSLog(@"stream updated!");
        }
    }];
```

Note, this won't update the stream object, it will return a new updatedStream object.


## Search and Feeds

Kickflip also provides API end points for searching publicly available streams associated with an App.

### Basic Search

To search all streams, simply call the below method with a keyword:

```obj-c
    [[KFAPIClient sharedClient] requestStreamsByKeyword:@"skateboard" pageNumber:1 itemsPerPage:10 callbackBlock:^(NSArray *streams, KFPaginationInfo *paginationInfo, NSError *error) {
        NSLog(@"found %d streams", (int)streams.count);
    }];
```

Otherwise, you can search for all streams by passing a nil keyword.

### User Search

To get all streams associated with a user, simply call:

```obj-c
    [[KFAPIClient sharedClient] requestStreamsForUsername:@"kickflip-user" pageNumber:1 itemsPerPage:25 callbackBlock:^(NSArray *streams, KFPaginationInfo *paginationInfo, NSError *error) {
        NSLog(@"found %d public streams for user", (int)streams.count);
    }];
```

### Location Search

Sometimes you want to find all streams within a certain geographic region. Use a CLLocation and a CLLocationDistance for radius as your parameters.

```obj-c
    [[KFAPIClient sharedClient] requestStreamsForLocation:currentLocation radius:100 pageNumber:1 itemsPerPage:25 callbackBlock:^(NSArray *streams, KFPaginationInfo *paginationInfo, NSError *error) {
        NSLog(@"found %d streams near %@", (int)streams.count, currentLocation);
    }];
```
