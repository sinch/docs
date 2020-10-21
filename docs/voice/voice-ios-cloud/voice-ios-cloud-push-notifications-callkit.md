---
title: Push Notifications and CallKit
excerpt: >-
  Using VoIP push notifications and _CallKit_ with the Sinch SDK.
hidden: false
next:
  pages:
    - voice-ios-cloud-playing-ringtones
---

Use the Sinch SDK together with Apple _VoIP_ push notifications and [CallKit](https://developer.apple.com/documentation/callkit) to provide the best possible end user experience. _VoIP_ push notifications are a special type of push notifications that Apple support as part of _Apple Push Notification service_ (_APNs_) which enables fast and high-priority notifications. [CallKit](https://developer.apple.com/documentation/callkit) is an iOS framework that lets you integrate the Sinch VoIP calling functionality with a iOS native system look and feel.

To fully enable VoIP push notifications in your application, the following steps are required, and this document will guide you through these in more detail:

- Configure your iOS app to use VoIP push notifications and _CallKit_.
- Create and upload an _APNs Signing Key_ for your _Sinch Application_ in the _Sinch Developer Portal_.
- Configure `SINClient` to let Sinch manage push notifications (both client-side and server-side).
- Integrate use of Sinch push APIs with _CallKit_.
- Ensure [APNs environment](https://developer.apple.com/documentation/bundleresources/entitlements/aps-environment) is matching the app entitlements and codesigning.


> ❗️Important
> For iOS apps built using the iOS 13 SDK or later, using _PushKit_ requires you to use _CallKit_ when handling VoIP calls.


## Configure iOS App with Push Notifications Capability

iOS apps must have the proper entitlements to use push notifications. To add these entitlements to your app, enable the _Push Notifications_ capability in your Xcode project. See Apple documentation [here](https://developer.apple.com/documentation/usernotifications/registering_your_app_with_apns?language=objc#overview) for details on this particular step.


## Acquiring a Push Device Token

`SINManagedPush` is a component used to simplify acquiring a push device token and registering the token with a `SINClient`. `SINManagedPush` will make use of _PushKit_ to acquire a push device token, and will automatically register the token with the `SINClient` when a client is created (later in the application life-cycle). `SINManagedPush` should be created as early as possible in the application’s life-cycle.

```objectivec
@interface AppDelegate () <SINManagedPushDelegate>
@property (nonatomic, readwrite, strong) id<SINManagedPush> push;
@end

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)options {

    self.push = [Sinch managedPushWithAPSEnvironment:SINAPSEnvironmentDevelopment];
    self.push.delegate = self;

    [self.push setDesiredPushType:SINPushTypeVoIP];
}
```

> 📘
>
> When creating the `SINManagedPush` instance, the _Apple Push Notification service Environment_ must be specified and it must __match__ how your application is code signed and provisioned. Please see the section [APS Environments and Provisioning](doc:voice-ios-cloud-push-notifications-callkit#apple-push-service-environments-and-provisioning) for details.

> 👍
>
> `SINManagedPush` is a very lightweight component and its lifecycle can be independent of the life-cycle of a `SINClient`. It should be created once, and should not be disposed (that would prevent receiving push notifications via _PushKit_).


## Enabling Push Notifications for `SINClient`

To enable that Sinch manage push notifications for you end-to-end, i.e. both client-side and server-side in terms acting [APNs Provider](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns?language=objc), it is necessary to enable that when configuring a `SINClient`.

```objectivec
id<SINClient> client = [Sinch clientWithApplicationKey:@"<application key>"
                                       environmentHost:@"ocra.api.sinch.com"
                                                userId:@"<user id>"];

[client enableManagedPushNotifications];
```

## Configuring an APNs Authentication Signing Key

To enable Sinch to act as _APNs Provider_ on your behalf, you must provide Sinch with an _APNs Authentication Token Signing Key_. You create this signing key in in your [_Apple Developer Account_](https://developer.apple.com/) and upload the key file to your [Sinch Developer Account](https://portal.sinch.com/#/apps).

1. Create an APNs Key in your [_Apple Developer Account_](https://developer.apple.com/). See the Apple developer documentation on creating the key [here](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/establishing_a_token-based_connection_to_apns).
2. Take the key file (`.p8`) from _step 1)_ and upload it for your _Sinch Application_ in your [Sinch Developer Account](https://portal.sinch.com/#/apps).

>🔒 Your Signing Key is Stored Securely
>
> Sinch will store your signing key in encrypted form using `AES256-GCM` (AES in GCM mode, using 256-bit keys).


__NOTE__: We only support _APNs Token-based authentication_, i.e. we no longer support APNs certificate-based functionality.

## CallKit

### Apple Requirements on Use of VoIP Push Notifications and _CallKit_

Apple introduced stricter requirements for using VoIP push notifications since iOS 13. iOS apps built using the iOS 13 SDK and make use of VoIP push notifications must report each received push notification as an incoming call to CallKit.

When linking against the iOS 13 SDK or later, your implementation **must** report notifications to the CallKit framework by calling the method [`-[CXProvider reportNewIncomingCallWithUUID:update:completion:]`](https://developer.apple.com/documentation/callkit/cxprovider/1930694-reportnewincomingcallwithuuid). Further, it must report this __within the same run-loop__, i.e. before the delegate method invocation scope `-[PKPushRegistryDelegate pushRegistry:didReceiveIncomingPushWithPayload:forType:withCompletionHandler:]` ends. In terms of the Sinch SDK, this means your implementation must report to CallKit in your implementation of  `-[SINManagedPushDelegate managedPush:didReceiveIncomingPushWithPayload:forType:]`.

```objectivec

// (Assuming CallKit CXProvider accessible as self.provider)

- (void)managedPush:(id<SINManagedPush>)managedPush
    didReceiveIncomingPushWithPayload:(NSDictionary *)payload
                              forType:(NSString *)pushType {

  id<SINNotificationResult> notification = [SINManagedPush queryPushNotificationPayload:payload];

  if ([notification isValid] && [notification isCall]) {
    NSUUID *callId = [[NSUUID alloc] initWithUUIDString:notification.callResult.callId];

    CXCallUpdate *callUpdate = [[CXCallUpdate alloc] init];
    callUpdate.remoteHandle = [[CXHandle alloc] initWithType:CXHandleTypeGeneric value:notification.callResult.remoteUserId];

    [self.provider reportNewIncomingCallWithUUID:callId
                                          update:callUpdate
                                          completion:^(NSError *_Nullable error) {
                                            if (error) {
                                              // Hangup call
                                            }
                                          }];
  }
}

// (For a more complete example, see the Sinch sample app SinchCallKit.xcodeproj)
```


When you relay the push notification to a `SINClient`. If you for some reason do not relay the push payload to a Sinch client instance using `-[SINClient relayRemotePushNotification:]`, you __must__ instead invoke `-[SINManagedPush didCompleteProcessingPushPayload:]` so that the Sinch SDK can invoke the _PKPushKit_ completion handler (which is managed by `SINManagedPush`).

> ❗️Report Push Notifications to CallKit
> If a VoIP push notification is not reported to CallKit then iOS will __terminate__ the application. Repeatedly failing to report calls to CallKit may cause the system to stop delivering any more VoIP push notifications to your app. The exact limit before this behaviour kicks in is subject to Apple iOS implementation details and outside the control of the Sinch SDK.
>
> Please also see [Apples Developer documentation on this topic](https://developer.apple.com/documentation/pushkit/pkpushregistrydelegate/2875784-pushregistry).

### Reporting outgoing calls to CallKit

While reporting incoming calls to CallKit is mandatory in order to process incoming VoIP push notifications, the same limitation does not apply to outgoing calls. Nevertheless, reporting outgoing calls to CallKit is still required in scenarios when an outgoing call is established (i.e., callee answers the call) while the caller app is in background, or the caller device is in locked state.

In such scenarios, as a privacy measure the OS will prevent the audio unit to be initialized for recording because the app is not in foreground, unless the outgoing call is reported to CallKit. For this reason the recommendation is that outgoing calls should be reported to CallKit as well.


```objectivec

// (Assuming CallKit CXCallController accessible as self.callController)
- (void)startOutgoingCall:(NSString *)destination {
  NSUUID *callId = [[NSUUID alloc] init];

  CXHandle *handle = [[CXHandle alloc] initWithType:CXHandleTypeGeneric value:destination];
  CXStartCallAction *initiateCallAction =
      [[CXStartCallAction alloc] initWithCallUUID:callId handle:handle];
  CXTransaction *initOutgoingCall = [[CXTransaction alloc] initWithAction:initiateCallAction];

  [self.callController requestTransaction:initOutgoingCall
                           completion:^void(NSError *error) {
                               if (error) {
                                 NSLog(@"%@", error);
                               }
                             }];
}

- (void)provider:(CXProvider *)provider performStartCallAction:(CXStartCallAction *)action {
  id<SINCall> call = [_client.callClient callUserWithId: action.handle.value];
  [action fulfill];
}

// (For a more complete example, see the Sinch sample app SinchCallKit.xcodeproj)
```

### Extracting Call Information From a Push Payload

At the time when your application receives a push notification you will need to extract some key information about the call based on only the push notification payload. You will need to do this to conform with Apple requirements on reporting a VoIP push notification as an incoming call to _CallKit_, but you may also want to extract application-specific headers for the call. Use the method `+[SINManagedPush queryPushNotificationPayload:]` to extract call details from the raw push payload. Note that you can do this immediately and before you have created and started a `SINClient` instance.

__Example__

```objectivec

- (void)managedPush:(id<SINManagedPush>)managedPush
    didReceiveIncomingPushWithPayload:(NSDictionary *)payload
                              forType:(NSString *)pushType {

  id<SINNotificationResult> notification = [SINManagedPush queryPushNotificationPayload:payload];

  if ([notification isValid]) {
    id<SINCallNotificationResult> callNotification = [notification callResult];

    // Use SINCallNotificationResult to extract remote User ID, call headers, etc.
  }

```

Also see reference documentation for [SINManagedPush](reference\html\Classes\SINManagedPush.html) and [SINCallNotificationResult](reference\html\Protocols\SINCallNotificationResult.html).

## Unregister a Push Device Token

If the user of the application logs out or performs a similar action, the push notification device token can be unregistered using the method `-[SINClient unregisterPushNotificationDeviceToken]` to prevent further notifications to be sent to the particular device.

## Push Notification _"Display Name"_

To support showing a user display name in a non-VoIP push notification (i.e. in a regular _Remote Push Notification_), you can configure a display name using [`-[SINManagedPush setDisplayName:]`]. The display name will be available to in the push notification in `aps.alert.loc-args` (when key `aps.alert.loc-key` is `SIN_INCOMING_CALL_DISPLAY_NAME`).

Example of Sinch push notification payload when display name is specified:

```
{
   "aps" : {
      "alert" : {
         "loc-key" : "SIN_INCOMING_CALL_DISPLAY_NAME",
         "loc-args" : [ "John Doe"]
      }
   },
   "sin": "<opaque push payload>"
}
```

Example of Sinch push notification payload when display name is __not__ specified:

```
{
   "aps" : {
      "alert" : {
         "loc-key" : "SIN_INCOMING_CALL"
      }
   },
   "sin": "<opaque push payload>"
}
```

## Apple Push Service Environments and Provisioning

When an iOS application is code signed, the embedded _Provisioning Profile_ will have to match the _Apple Push Notification service Environment_ (also refered to as _APS Environment_) specified in the app [_Entitlements_](https://developer.apple.com/documentation/bundleresources/entitlements/aps-environment?language=objc).

This means how the the app is code signed and what _Provisioning Profile_ is used has an effect on what value should be passed to `+[Sinch managedPushWithAPSEnvironment:]`.

For example, if your application is signed with a _Development_ provisioning profile it will be bound to the APS _Development_ environment. If it’s code signed with a _Distribution_ provisioning profile it will be bound to the APS _Production_ environment.

Typically a _Debug_ build will be code signed with a _Development_ provisioning profile and thus `SINAPSEnvironmentDevelopment` should be used. And typically a _Release_ build will be code signed with a _Distribution_ provisioning profile and thus `SINAPSEnvironmentProduction` should be used. **You are responsible for selecting proper entitlements depending on your build type and signing profile.**

## iOS not Delivering Notifications

Under certain circumstances, iOS will not deliver a notification to your application even if it was received at device/OS level. Note that this also applies to VoIP push notifications. Exact behaviour and limits are subject to iOS internal details, but well known scenarios where notifications will not be delivered are:

* The end user has actively terminated the application. iOS will only start delivering notifications to the application again after the user has actively started the application again.
* Your app has not been reporting VoIP push notifications to _CallKit_. Please see the separate sections above on how to report VoIP push notifications as _CallKit_ calls.

## Relevant Apple Resources

For more details on Apple _PushKit_ and _CallKit_, see following _Apple Developer Documentation_:

* [PushKit](https://developer.apple.com/documentation/pushkit)
* [CallKit](https://developer.apple.com/documentation/callkit)
* [Responding to VoIP Notifications from PushKit](https://developer.apple.com/documentation/pushkit/responding_to_voip_notifications_from_pushkit)

## SINManagedPush and SINClient Interaction

This section covers details on how `SINManagedPush` and `SINClient` interacts together (automatically).

`SINManagedPush` will make use of `PKPushRegistry` to acquire a push device token. If any `SINClient` instances exist, it will register the token via `-[SINClient registerPushNotificationDeviceToken:type:apsEnvironment:]`, which will in turn register the token with the Sinch backend platform. If no instance of `SINClient` exists when `SINManagedPush` initially acquire the token, it will hold on to the token (in-process memory only) and register it with any `SINClient` that is created later during the whole application life-cycle.
