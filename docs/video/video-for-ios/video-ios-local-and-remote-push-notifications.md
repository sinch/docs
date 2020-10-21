---
title: Local and Remote Push Notifications
excerpt: >-
  Notify users of incoming calls by using local notifications or a remote push
  notification in iOS.
next:
  pages:
    - video-ios-audio-handling
---
When an application is no longer in the foreground, the user must be notified of an incoming call by means of either a local notification or a remote push notification.

If the app is allowed to execute while in background then the Sinch SDK will primarily use local notifications to initially notify the user. Whether the app is allowed to execute while in background depends on whether you as a developer choose to enable [VoIP push notifications](doc:video-ios-local-and-remote-push-notifications#enabling-voip-push-notifications) and/or [Active Connection in Background](doc:video-ios-miscellaneous#active-connection-in-background). If only regular remote push notifications are used then those will also be used to notify the user (as a regular remote push notification will be displayed to the user by the iOS without handing over any control to the application).

## Remote Push Notifications

An application is considered _offline_ in the following scenarios:

> - Application is not running at all
> - Application is not in the foreground, and the feature \[Active
>   Connection in Background\]\[\] is not enabled

For these scenarios, push notifications can be used to receive incoming calls. The following sections cover how to enable receiving calls and messages using remote push notifications (both the VoIP type and regular.)

### Acquiring a device token and user consent

_SINManagedPush_ is a component used to simplify acquiring a push device token and registering it with a Sinch client. It also simplifies in terms of abstracting away some of the iOS SDK API differences between iOS verions and APIs, as well as differences for regular remote push notifications and VoIP push notifications.

_SINManagedPush_ should be created as early as possible in the application’s life-cycle (and it’s lifecycle can be independent of a _SINClient’s_ life-cycle.)

```objectivec
@interface AppDelegate () <SINManagedPushDelegate>
@property (nonatomic, readwrite, strong) id<SINManagedPush> push;
@end

- (BOOL)application:(UIApplication *)application
    didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {

    self.push = [Sinch managedPushWithAPSEnvironment:SINAPSEnvironmentDevelopment];
    self.push.delegate = self;

    [self.push setDesiredPushTypeAutomatically];
    [self.push registerUserNotificationSettings]; // This can be delayed to later in the app's life-cycle, e.g. once a user logs in.
}
```

> **undefined**
>
> When creating the managed push instance, the Apple Push Service Environment (also referred to as _APS Environment_) must be specified and it must match how your application is code signed and provisioned. Please see the section [Apple Push Service environments and provisioning](#apple-push-service-environments-and-provisioning) for details.

### Enable push notifications on a Sinch client

When creating a Sinch client, managed push notifications must be enabled:

```objectivec
id<SINClient> client = [Sinch clientWithApplicationKey:@"<application key>"
                                     applicationSecret:@"<application secret>"
                                       environmentHost:@"clientapi.sinch.com"
                                                userId:@"<user id>"];

[client enableManagedPushNotifications];
```

### Forward incoming push notifications to a Sinch client

Implement the protocol `SINManagedPushDelegate` and forward any incoming push notifications to a Sinch client:

```objectivec
// SINManagedPushDelegate
- (void)managedPush:(id<SINManagedPush>)unused
    didReceiveIncomingPushWithPayload:(NSDictionary *)payload
                              forType:(NSString *)pushType {
    id<SINClient> client; // get previously created client
    [client relayRemotePushNotification:userInfo];
}
```

The purpose of `SINManagedPushDelegate` and the delegate method `managedPush:didReceiveIncomingPushWithPayload:type:` is that it provides a single unified code path for handling incoming push notifications, no matter whether it is a regular remote push notification or a VoIP push notification (which is received via _PushKit_ and _PKPushRegistry_). In the case of a regular remote push; independently of whether it is arriving via `-[UIApplicationDelegate didReceiveRemoteNotification:` or as a launch option via `-[UIApplication applicationDidFinishLaunching:didFinishLaunchingWithOptions:`.

### Unregister a push device token

If the user of the application logs out or performs a similar action, the push notification device token can be unregistered via `-[SINClient unregisterPushNotificationDeviceToken]` to prevent further notifications to be sent to the particular device.

### Supporting regular remote push notifications (i.e. non-VoIP type push notifications)

Because of differences in the iOS SDK API with respect to regular and VoIP push notifications, incoming push notifications have different code paths through an application. _SINManagedPush_ unifies this to the extent possible, but for regular remote notifications which are traditionally received via methods on _UIApplicationDelegate_, the following methods should be forwarded from the application delegate:

```objectivec
- (void)application:(UIApplication *)application
    didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
        [self.push application:application didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
}

- (void)application:(UIApplication *)application
    didReceiveRemoteNotification:(NSDictionary *)userInfo {
        [self.push application:application didReceiveRemoteNotification:userInfo];
}
```

### Sinch localization strings for push notification alerts

When the Sinch dashboard sends a remote push notification for an incoming call or message it will by default use one of the localization strings shown below. You will need to add these to your `Localizable.strings` file.

```objectivec
SIN_INCOMING_CALL = "Incoming call";
SIN_INCOMING_CALL_DISPLAY_NAME = "Incoming call from %@";
SIN_INCOMING_IM = "Incoming message";
SIN_INCOMING_IM_DISPLAY_NAME = "Incoming message from %@";
SIN_INCOMING_VIDEO_CALL = "Incoming video call";
SIN_INCOMING_VIDEO_CALL_DISPLAY_NAME = "Incoming video call from %@";
```

`SIN_INCOMING_CALL_DISPLAY_NAME` (or `SIN_INCOMING_IM_DISPLAY_NAME`) will be used if display name have been set by the caller via `-[SINManagedPush setDisplayName:` or `-[SINClient setPushNotificationDisplayName:]`. Display name is included in a push notification on a best-effort basis. For example, if the target device has very limited push payload size constraints (e.g. iOS 7 can only handle 255 byte push notification payload), then the display name may not be included.

> **Note**
>
> This is not applicable to VoIP type push notifications because in that case the Sinch SDK will present a local notification (see* `-[SINCallClientDelegate client:localNotificationForIncomingCall:`*)\*

Please see [Apple’s Local and Remote Notification Programming Guide](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1) on remote push notification and localization strings for more details.

## Presenting local notifications for incoming calls

If the application is not in foreground when receiving an incoming call, the `SINCallClient` will ask it’s delegate `SINCallClientDelegate` to specify how a local notification should be presented. The delegate is responsible for composing a `SINLocalNotification` with content that is to be shown to the user when the local notification is presented to the user by iOS. The delegate can specify all things as available for a regular `UILocalNotification`, e.g. alert body, actions, a badge number, and the path to a sound file that is played when the notification is
presented.

Example:

```objectivec
// implementation of SINCallClientDelegate
- (SINLocalNotification *)client:(id<SINClient>)client
  localNotificationForIncomingCall:(id<SINCall>)call {
    SINLocalNotification *notification = [[SINLocalNotification alloc] init];
    notification.alertAction = @"Answer";
    notification.alertBody = @"Incoming call";
    return notification;
}
```

If the user taps the notification, iOS brings the app back into the foreground. The notification object contains information that is needed by the Sinch SDK to continue initiating the incoming call. To hand over the notification object to the Sinch client, use the method `relayLocalNotification:`:

```objectivec
- (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification {

    if ([notification sin_isSinchNotification]){

      // This will trigger -[SINClientDelegate didReceiveIncomingCall:] if the notification
      // represents a call (i.e. contrast to that it may represent an instant-message)
      id<SINNotificationResult> result = [client relayLocalNotification:notification];
    }
}
```

The `SINCallNotificationResult` object provides details about the caller, whether the call timed out and whether the call offers video.

### Answering a call received while in background

Once the Sinch SDK has processed the call information extracted from the notification, it calls the delegate method `client:didReceiveIncomingCall:`. Depending on the desired behavior for the app, the incoming call may either be treated as any other incoming call and let the user tap an additional button to answer it, or the call may be answered automatically when the user has acted on the local notification.

In the latter case, the app can determine whether the call originated from background mode or not by examining the `applicationStateWhenReceived` property of the call details object. If the application was active when it received the call, it means the app was in the foreground. This approach is also applicable to offline calls with Apple Push Notifications.

```objectivec
- (void)client:(id<SINClient>)client didReceiveIncomingCall:(id<SINCall>)call {
    call.delegate = self;

    if (call.details.applicationStateWhenReceived == UIApplicationStateActive) {
        // Show an answer button or similar in the UI
    } else {
        // Application was in not in the foreground when the call was initially received,
        // and the user has opened the application (e.g. via a Local Notification),
        // which we then interpret as that the user want to answer the call.
        [call answer];
    }
}
```

## Presenting local notifications for missed calls

When the push notification is enabled on a Sinch client, besides the incoming call notification, the Sinch SDK will also send a push notification for a canceled call when the caller cancels the call before it is answered. This gives developers a good opportunity to present local notifications for missed calls in their apps.

A `SINNotificationResult` will be returned when an incoming push notification is forwarded to a Sinch client. It could then be inspected to determine if the push notification is a canceled call, it also provides detailed information such as `remoteUserId` to construct a more informative local notification for a missed call.

```objectivec
// SINManagedPushDelegate
- (void)managedPush:(id<SINManagedPush>)unused
    didReceiveIncomingPushWithPayload:(NSDictionary *)payload
                              forType:(NSString *)pushType {
    id<SINClient> client; // get previously created client

    id<SINNotificationResult> result = [client relayRemotePushNotification:userInfo];
    if ([result isCall] && [[result callResult] isCallCanceled]) {
        // present a local notification for the missed call.
    }
}
```

## Enabling VoIP push notifications

In iOS 8 Apple introduced remote VoIP push notifications and a new framework _PushKit.framework_. VoIP push is more battery efficient than using an active VoIP socket, and still provides the possibility of background execution which allows for faster call setup time. The Sinch SDK supports both VoIP and regular remote push notifications.

The Sinch SDK will automatically use VoIP push if _PushKit.framework_ is linked into your app, given that `-[SINManagedPush:setDesiredPushTypeAutomatically]` is used. Using _setDesiredPushTypeAutomatically_ will fall back to using regular remote push notifications on devices that don’t support VoIP push (e.g. devices running on iOS \< 8.0).

It is required to implement `-[SINCallClientDelegate localNotificationForIncomingCall:]` when using VoIP push.

To enable VoIP push on devices that are capable of it and also fall back on regular remote notifications for iOS 6 and 7, we recommended to _weak link_ PushKit.framework. In the Xcode target settings, go to _Build Phases_ and the section _Link Binary With Libraries_ and in the _Status_ field on the right-hand side, select **Optional** (instead of _Required_):
![weak_link_pushkit.png](images\e8e9dbd-weak_link_pushkit.png)

For more details on VoIP push notifications, see [here](https://developer.apple.com/library/prerelease/content/documentation/Performance/Conceptual/EnergyGuide-iOS/OptimizeVoIP.html)

## Sinch local notifications and the Notification Center

The following details explain how the Sinch SDK handles local notifications and how it affects what is presented in the Notification Center.

> - A notification that is passed in to the method `-[SINClient relayLocalNotification:notification:]` is removed from the Notification Center after it has been handled.
> - A local notification representing an incoming call is removed if the call times out or the call is canceled.
> - Invoking `-[SINCall hangup]` from `-[SINClient client:localNotificationForIncomingCall]` is a valid operation and can be used to dismiss a call while the user is busy talking in the regular phone app. This effectively prevents the SDK from invoking the `-[SINClientDelegate client:didReceiveIncomingCall:]` method when the app returns to foreground.
> - Invoking `-[SINCall answer]` while being in the background is possible. The call is not immediately answered but the operation is considered pending and the call answered once the app returns to the foreground.

## Sending and receiving custom headers via Sinch managed push

The Sinch SDK supports adding custom headers in push notification messages when initiating a call, so developers do not need to implement their own push mechanism if they only need to deliver small pieces of information along the Sinch managed push between their app instances. The Sinch SDK allows up to _1024_ bytes of custom headers.

Setting headers on the sender side when initiating a call:

```objectivec
// Enable push notification on Sinch client before making the call
...
// Set headers
NSDictionary *headers = @{
                          @"id" : @"0429",
                          @"message" : @"Greetings from Alice."
                          };
id<SINCall> call = [self.callClient callUserWithId:@"Bob"
                                           headers:headers];
```

On the receiver side, the headers are received and encoded in the push payload, and can be queried by `[SINPushHelper queryPushNotificationPayload:payload]`. The helper will return a `SINNotificationResult` which contains `headers` inside `callResult`.

```objectivec
+ (void)managedPush:(id<SINManagedPush>)unused
didReceiveIncomingPushWithPayload:(NSDictionary *)payload
            forType:(NSString *)pushType {

  id<SINNotificationResult> result = [SINPushHelper queryPushNotificationPayload:payload];
  if ([result isCall]) {
    NSLog(@"%@", result.callResult.headers);

    // You can then invoke relayRemotePushNotification:userInfo
    // on a SINClient instance to further process the incoming call.
    ...
  }
}
```

## Apple Push Notification Certificates

Sending and receiving push notifications via Sinch requires you to create Apple Push Certificates and upload them to the Sinch Dashboard. For each application, Sinch allows you to upload up to three certificates corresponding, one for each type: _Development_, _Production_ and _VoIP Services_.

### Generating Apple Push Certificates

Apple Push Certificates are generated from the [Apple Developer Member Center](https://developer.apple.com/account/overview.action) which requires a valid Apple ID to login. If you do not have this information, find out who manages the Apple Developer Program for your organization.

### Setting up your Apple Push Certificates with Sinch

> - Login to the [Dashboard](https://www.sinch.com/dashboard)
> - Open the _Apps_ tab, select the application of your choice and click on the Push Notification icon on the left-most side.
> - Drag and drop the certificates associated to your application
> - If your certificate has a password, enter it and hit Enter

Your push notification certificates are now uploaded and ready for use.

Certificates configured with Sinch can be replaced or renewed by uploading new ones. New certificates will automatically replace the previous ones for their respective type (_Development_, _Production_ and _VoIP Services_).

### Apple Push Service environments and provisioning

When an iOS application is code signed, it is the embedded _Provisioning Profile_ that will specify which _Apple Push Notification Service Environment_ (_APS Environment_) the acquired push notification device token will be bound to. Depending on how an application is provisioned it has an effect on what should be passed to `[Sinch managedPushWithAPSEnvironment:]`. For example if your application is signed with a _Development_ provisioning profile it will be bound to the APS _Development_ environment. If it’s code signed with a _Distribution_ provisioning profile (also referred to as _Universal_) it will be bound to the APS _Production_ environment.

Typically a _Debug_ build will be code signed with a _Development_ provisioning profile and thus `SINAPSEnvironmentDevelopment` should be used. And typically a _Release_ build will be code signed with a _Distribution_ provisioning profile and thus `SINAPSEnvironmentProduction` should be used. **You are responsible for selecting proper entitlements depending on your build type and signing profile.**
