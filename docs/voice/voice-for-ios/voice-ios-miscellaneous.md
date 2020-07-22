---
title: Miscellaneous
excerpt: ''
---
## Minimum requirements

We officially support the 3 latest major iOS versions. You can try older versions but there are no guarantees it will work as expected.

_Note:_ The Sinch SDK library uses Automatic Reference Counting (ARC). However, it can still be used in non-ARC projects.

## Note on Sinch.framework file size vs. linked size

The _Sinch.framework_ file includes a FAT-binary containing the architectures _armv7_, _armv7s_, _arm64_, _i386_ and _x86_64_. When linking an application target against the _Sinch.framework_ targeting an iOS device, it will add a approximately 6.1Mb per _armv7_ slice, and 6.3Mb for the _arm64_ slice.

**Example**: Assuming linking _armv7_ and _arm64_ into the final application, it would add approximately 12.4Mb to the application.

(Even though _armv7s_ is supported and included, we recommend to not build your application for armv7s to reduce the final application binary size)

## Production environments

Sinch provides two environments:

> - Production - Used for applications deployed in production.

The environment is passed as the parameter _environmentHost_ when instantiating the Sinch client.

| Environment | EnvironmentHost parameter |
| ----------- | ------------------------- |
| Production  | clientapi.sinch.com       |

## Restrictions on User IDs

User IDs can only contain characters in the _printable ASCII character set_. That is:

```text
!"#$%&'()*+,-./0123456789:;<=>?@ABCDEFGHIJKLMNOPQRSTUVWXYZ[\]^_`abcdefghijklmnopqrstuvwxyz{|}~
```

User IDs **must not** be longer than **40** characters.

## Encryption export regulations

Please check the Summary of U.S. Export Controls Applicable to Commercial Encryption Products and ensure that the application is registered for the Encryption Regulations, if applicable. It can be found under this [link](http://www.sinch.com/).

## Statistics

The Sinch SDK client uploads statistics to the Sinch servers at the end of a call, a call failure, or similar event. The statistics are used for monitoring of network status, call quality, and other aspects regarding the general quality of the service.

Some of the information is not anonymous and may be associated with the User ID call participants.

The statistics upload is done by the client in the background.

## Linking against the C++ standard library

Since Sinch SDK version 3.4.0, it is required to link against _libc++_. Though if your application is also dependent on _libstdc++_ (which is now considered deprecated by Apple for use on iOS), you can actually link against both _libc++_ and _libstdc++_ by passing the following linker flags:

- Other Linker Flags -\> `-ObjC -Xlinker -lc++ -Xlinker -lstdc++`

## Request user permission for using the microphone

Since iOS 7, additional user privacy constraints are enforced which requires the application to be granted permission to use the device microphone. Unless the application has explicitly requested permission to use the microphone, the user is shown a dialog the first time the microphone is activated.

In the context of the Sinch SDK, this occurs once the first call is established _unless_ the application has been granted permission earlier. We **strongly recommend** you explicitly request permission to use the microphone in your application at an appropriate time such as when the user first sets up Sinch. You should not rely on the permission dialog shown when the first Sinch call is established as this will create an awkward user experience.

By explicitly requesting permission using the methods available in the iOS SDK, the application has more control over when the dialog is shown to the user. This results in a better user experience.

Starting with iOS 10.0, apps that access any of the device’s microphones must declare their intent to do so. This is done by including the NSMicrophoneUsageDescription key and a corresponding purpose string in your app’s Info.plist. When the system prompts the user to allow access, the purpose string is displayed as part of the alert. If an application attempts to access any of the device’s microphones without a corresponding purpose string, the app will exit.

Please see the [Apple iOS SDK documentation on the class AVAudioSession](http://developer.apple.com/library/ios/#documentation/AVFoundation/Reference/AVAudioSession_ClassReference/Reference Reference.html) for details on how to request permission to use the microphone.

## Request user permission for using the camera

The same rule applies to request user permission for using the camera. In iOS, the user must explicitly grant your app permission to access device cameras or microphones for photo, video, or audio capture. Your app must provide an explanation for its use of capture devices using the NSCameraUsageDescription and NSMicrophoneUsageDescription Info.plist keys; iOS displays this explanation when initially asking the user for permission, and thereafter in the Settings app.

Please see the [Apple iOS SDK documentation on the class AVCaptureDevice](https://developer.apple.com/documentation/avfoundation/avcapturedevice?language=objc) for details on how to request permission to use the camera.

## App Extensions

_App Extensions_ is a feature introduced in iOS 8. App extensions are compiled into executables that are separate from the main application executable. The Sinch SDK are using parts of the iOS SDK APIs that are unavailable to app extensions, thus it’s not supported to use the Sinch SDK in an app extension.

## Xcode and Bitcode intermediate representation

The Sinch SDK supports Bitcode intermediate representation.

## Deprecated features and APIs

### Active Connection in Background

Apple has since iOS 10 discontinued support for maintaining a _VoIP_ control connection alive via `-[UIApplication setKeepAliveTimeout:handler:]`. Attempting to use this method on an iOS device running iOS 10 results in the following warning log: `Legacy VoIP background mode is deprecated and no longer supported`. The Sinch feature _Active connection in background_ was using the keep alive handler API and is as a consequence no longer supported on iOS. It is recommended to use [VoIP Push Notifications](doc:voice-ios-local-and-remote-push-notifications) to achieve the equivalent functionality.

### Missed Call Push Notifications

Sinch SDK primarily use VoIP push notifications. Since iOS 13, Apple and iOS imposed stricter limitations and requirements on how each VoIP push notification that an application receive must be reported to _CallKit_ as an incoming call. This has the implication that Sinch SDK no longer supports separate "Missed Call" push notifications.

We recommend using your own non-VoIP push notification mechanism to deliver "Missed Call" push notifications.

Please also see [Apple Developer documentation on this topic](https://developer.apple.com/documentation/pushkit/pkpushregistrydelegate/2875784-pushregistry).

## Push Notifications sent via your application server

In general we strongly recommend using _“managed push notifications”_, i.e. when push notifications are sent directly from the Sinch cloud, which is described in the section \[Local and Remote Push Notifications\]\[\]. The following section on the contrary describes integrating support for push notifications but given that your application server maintain the connection with Apple Push Notification Service.

An application is considered offline in the following scenarios:

> - When the application is not running
> - When background mode has been disabled for the Sinch client, and
>   the application is not in the foreground

For these scenarios, push notifications can be used to be able to receive incoming calls. The following sections cover how to support receiving calls and messages using push notifications.

When offline, the recipient of a call or message receives a push notification containing a Sinch-specific payload which enables the Sinch client to connect the incoming call or message. Acting on the push notification brings the application to the foreground which allows the user to answer the call or view the message.
![push-sequence-diagram_apple.png](images\44701e4-push-sequence-diagram_apple.png)
The figure above describes the following sequence of events: Both users start their applications and Sinch clients. When A (the caller) calls B (the callee), B’s application is in a state where it is not considered online (that is reachable using an active socket connection). Sinch notices that B is not online, and tells A to send a push notification to B so that B can answer the call.

When the Sinch client on the caller’s (or sender’s) side observes that the destination client is offline, it notifies the application to trigger the sending of a push notification to the callee’s device.

### Push notification data

On startup, each instance of the application is expected to register a device identifier. The identifier is referred to as _push notification data_ and should be provided to the Sinch client by the method `registerPushNotificationData:`.

Push notifications can be addressed to that identifier in the event that the application goes offline.

The push notification data can be any byte sequence; it is up to you to define its structure and what it contains. However, the push notification data must not exceed 1024 bytes. It should contain enough information to allow your application or application server to use a push service to send a push notification to a specific user of the application on a specific device. For example, an iOS exclusive application would likely use the Apple Push Notification Device Token as push notification data. Multi-platform applications may use a mix of different push services. The following sections assume that Apple Remote Notifications are used, but the use pattern for other push services is similar.

The push notification data can be unregistered by calling the `unregisterPushNotificationData` method. This disables incoming calls using push notifications addressed to the specific device.

### Enable push notifications

Start by enabling support for push notifications when initiating the _SINClient_:

```objectivec
#import <Sinch/Sinch.h>

id<SINClient> client = [Sinch clientWithApplicationKey:@"<application key>"
                                         applicationSecret:@"<application secret>"
                                           environmentHost:@"clientapi.sinch.com"
                                                    userId:@"<user id>"];

[client setSupportPushNotifications:YES];

client.delegate = ...;

[client start];
```

Supporting offline calls and/or messages requires that the application registers for remote push notifications, which in the example that follows is done in the method _-\[UIApplicationDelegate application:didFinishLaunchingWithOptions:\]_.

```objectivec
- (BOOL)application:(UIApplication *)app didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    UIRemoteNotificationType types = UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeSound;
    [[UIApplication sharedApplication] registerForRemoteNotificationTypes:types];
}
```

The next step is to register the _push notification data_ with the _SINClient_, which in the example below is done by using the _APNS_ device token as _push notification data_. Upon receiving the the device token from Apple Push Notification Service using the _UIApplicationDelegate_-method, it is registered with the _SINClient_.

```objectivec
- (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
    // get previously initiated Sinch client
    id<SINClient> client = [self sinchClient];

    [client registerPushNotificationData:deviceToken];
}
```

Please refer to Apple’s [Local and Push Notification Programming Guide](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) for more information on how to obtain the Apple Push Notification Device Token.

### Sending and receiving Apple push notifications

To send push messages, you must have a server that is configured for sending push notifications to Apple Push Notification Service. Please see the [Sinch REST API User Guide](doc:using-rest) for details on how to handle feedback from Apple Push Notification Service.

Please also refer to Apple’s [Local and Push Notification Programming Guide](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) for further details on push notifications.

#### On the caller side

When the recipient’s application is offline and the app needs to notify the user using a push notification, the caller’s or sender’s application is notified using the delegate method `call:shouldSendPushNotifications:`.

Because there might be multiple registered devices for the recipient user (for example, the same user is using the application on both an iPhone and an iPad) the callback is passed an array of `SINPushPair`s. The pairs contain a payload that is Sinch- and call-specific. Moreover, the pairs contain a push data byte array. The Sinch-specific payload should be embedded in the push notification sent to the recipient’s device(s). The push data is the same push data that the recipient’s application registered earlier.

```objectivec
- (void)call:(id<SINCall>)call shouldSendPushNotifications:(NSArray *) pushPairs {
    // Send payload and push data to application server
    // which should communicate with Apple Push Notification Service
    // to send push notifications.
}
```

> **Note**
>
> This example shows the calling case. Messaging works the same way.

A push notification should be sent to each device, where each `pushPair.pushData` entry in the array corresponds to one device. The push notification should include the Sinch-specific payload so it can be forwarded to the Sinch client running on the destination device.

The Sinch-specific payload should be embedded as custom payload data in the Apple Push Notification Payload, see JSON example below.

```objectivec
{
    "aps" : {
        "alert" : "Incoming call from <user>",
        "sound" : "bingbong.aiff"
    },
    "SIN" : <payload>,
}
```

The Sinch-specific payload will not exceed 100 bytes, meaning that there should be 156 bytes available in the push notification payload for application-specific purposes.

Please refer to Apple’s [Local and Push Notification Programming Guide](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/) for further details.

#### On the callee side

As a prerequisite, offline calling and messaging must have been enabled on the receiver’s side (see \[Push Notifications\]\[\]).

When the application receives a push notification from the Apple Push Notification Service, the application launches and extracts the Sinch-specific payload from the push notification. Once extracted the payload is forwarded to the Sinch client using the method `relayRemotePushNotificationPayload:`.

```objectivec
- (BOOL)application:(UIApplication *)app didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    NSDictionary* remotePush = [launchOptions objectForKey:UIApplicationLaunchOptionsRemoteNotificationKey];

    if (remotePush) {

        // Extract the Sinch-specific payload from the Apple Remote Push Notification
        NSString* payload = [remotePush objectForKey:@"SIN"];

        // Get previously initiated Sinch client
        id<SINClient> client = [self sinchClient];

        id<SINNotificationResult> result = [client relayRemotePushNotificationPayload:payload];

        if (result.isCall && result.callResult.isTimedOut) {
            // Present alert notifying about missed call
        } else if (!result.isValid) {
            // Handle error
        }
    }
}
```

> **Note**
>
> You should have similar logic of relaying the push notification payload to the _SINClient_-instance in your implementation of `-[UIApplicationDelegate application: didReceiveRemoteNotification:]`.

## Glossary

This glossary defines some of the domain specific terms used throughout this document.

| Term                 | Explanation                                                                                                                                                       |
| -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Application          | The mobile application running on iOS and/or Android. A partner can have more than one application.                                                               |
| Application Instance | One installation of the application on a single device.                                                                                                           |
| Application Key      | A key generated by Sinch. The key is unique to the application. A key looks like `196087a1-e815-4bc4-8984-60d8d8a 43f1d` (lowercase hexadecimal formatted GUID).  |
| Application Secret   | A string generated by Sinch. The secret is used to verify the application. A secret looks like `oYdgGRXoxEuJhGDY2KQ/HQ==` (Base64-encoded string representation). |
| Callee               | The person receiving a call.                                                                                                                                      |
| Caller               | The person making a call.                                                                                                                                         |
| User                 | A user of the mobile application. The actual person holding the mobile device.                                                                                    |
| User Identity        | Identity of a user in the application domain. Can be any string, for instance a user name, user id, phone number or email address.                                |
| Active Connection    | A socket connection for signaling purposes where incoming calls are received.                                                                                     |
