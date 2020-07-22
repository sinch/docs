---
title: Push Notifications and CallKit
excerpt: >-
  Using VoIP push notifications and _CallKit_ with the Sinch SDK.
hidden: false
next:
  pages:
    - voice-ios-playing-ringtones
---

Use the Sinch SDK toghether with Apple _VoIP_ push notifications and [CallKit](https://developer.apple.com/documentation/callkit) to provide the best possible end user experience. _VoIP_ push notifications are a special type of push notifications that Apple support as part of _Apple Push Notification service_ (_APNs_) which enables fast and high-priority notifications. [CallKit](https://developer.apple.com/documentation/callkit) is an iOS framework that lets you integrate the Sinch VoIP calling functionality with a iOS native system look and feel.

To fully enable VoIP push notifications in your application, the following steps are required, and this document will guide you through these in more detail:

- Configure your iOS app to use VoIP push notifications and _CallKit_.
- Create and upload an _APNs VoIP Services Certificate_ for your _Sinch Application_ in the _Sinch Developer Portal_.
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

    self.push = [Sinch managedPushWithAPSEnvironment:SINAPSEnvironmentAutomatic];
    self.push.delegate = self;

    [self.push setDesiredPushType:SINPushTypeVoIP];
}
```

> 📘
>
> When creating the `SINManagedPush` instance, the _Apple Push Notification service Environment_ must be specified and it must __match__ how your application is code signed and provisioned. Please see the section [APS Environments and Provisioning](doc:voice-ios-push-notifications-callkit#apple-push-service-environments-and-provisioning) for details.

> 👍
>
> `SINManagedPush` is a very lightweight component and its lifecycle can be independent of the life-cycle of a `SINClient`. It should be created once, and should not be disposed (that would prevent receiving push notifications via _PushKit_).


## Enabling Push Notifications for `SINClient`

To enable that Sinch manage push notifications for you end-to-end, i.e. both client-side and server-side in terms acting [APNs Provider](https://developer.apple.com/documentation/usernotifications/setting_up_a_remote_notification_server/sending_notification_requests_to_apns?language=objc), it is necessary to enable that when configuring a `SINClient`.

```objectivec
id<SINClient> client = [Sinch clientWithApplicationKey:@"<application key>"
                                       environmentHost:@"clientapi.sinch.com"
                                                userId:@"<user id>"];

[client enableManagedPushNotifications];
```

## Configuring Apple Push Notification Certificates

Sending and receiving push notifications via Sinch requires you to create Apple APNs push certificates and upload them to the Sinch dashboard.

### Generating Apple Push Certificates

Apple Push Certificates are generated from the [Apple Developer Member Center](https://developer.apple.com/account/resources/certificates/list) which requires a valid _Apple ID_ to login. If you do not have this information, find out who manages the Apple Developer Program for your organization.

1. Go to [Create a New Certificate](https://developer.apple.com/account/resources/certificates/add) in the _Apple Developer_ web portal. 
2. Under _Services_, select type _VoIP Services Certificate_
3. Continue the steps as instructed by the Apple Developer guide, and download the certificate file, it will typically be named `voip_services.cer`.

#### Exporting the `.cer` certificate file as a `.p12` bundle

1. Open the `.cer` file and it will be opened in the _macOS_ _Keychain Access_ application. 
2. Fold out the drop-down for the selected certificate and you should see the associated private key.
3. Select both the certificate and the private key, right click and select _Export 2 items..._.
5. Select destination path, and click _Save_. You will be prompted for a passphrase. Enter a strong passphrase and press _OK_.
6. You should now have a `.p12` file ready to be uploaded to the Sinch dashboard.

### Setting up your Apple Push Certificates with Sinch

> - Login to the [Dashboard](https://portal.sinch.com/#/dashboard)
> - Open the _Apps_ tab, click on the name of _Application_ you want to configure, then select _Push Configuration_ (top-left in the tabs menu)
> - Drag and drop the certificates associated to your application
> - If your certificate has a password, enter it and hit Enter

Your push notification certificates are now uploaded and ready for use.

Certificates configured with Sinch can be replaced or renewed by uploading new ones. New certificates will automatically replace the previous ones for their respective type (_Development_, _Production_ and _VoIP Services_).

## CallKit

### Apple Requirements on Use of VoIP Push Notifications and _CallKit_

Apple introduced stricter requirements for using VoIP push notifications since iOS 13. iOS apps built using the iOS 13 SDK and make use of VoIP push notifications must report each received push notification as an incoming call to CallKit.

When linking against the iOS 13 SDK or later, your implementation **must** report notifications to the CallKit framework by calling the method [`-[CXProvider reportNewIncomingCallWithUUID:update:completion:]`](https://developer.apple.com/documentation/callkit/cxprovider/1930694-reportnewincomingcallwithuuid). Further, it must report this __within the same run-loop__, i.e. before the delegate method invocation scope `-[PKPushRegistryDelegate pushRegistry:didReceiveIncomingPushWithPayload:forType:withCompletionHandler:]` ends. In terms of the Sinch SDK, this means your implementation must report to CallKit in your implementation of  `-[SINManagedPushDelegate managedPush:didReceiveIncomingPushWithPayload:forType:]`.

```objectivec

// (Assuming CallKit CXProvider accessible as self.provider)

- (void)managedPush:(id<SINManagedPush>)managedPush
    didReceiveIncomingPushWithPayload:(NSDictionary *)payload
                              forType:(NSString *)pushType {

  id<SINNotificationResult> notification = [SINPushHelper queryPushNotificationPayload:payload];

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
> If a VoIP push notification is not reported to CallKit then iOS will __terminate__ the application. Repeatedly failing to report calls to CallKit may cause the system to stop delivering any more VoIP push notifications to your app. The exact limit before this throttling behaviour kicks in is subject to Apple iOS implementation details and outside the control of the Sinch SDK.
>
> Please also see [Apples Developer documentation on this topic](https://developer.apple.com/documentation/pushkit/pkpushregistrydelegate/2875784-pushregistry).

### Extracting Call Information From a Push Payload

At the time when your application receives a push notification you will need to extract some key information about the call based on only the push notification payload. You will need to do this to conform with with Apple requirements on reporting a VoIP push notification as an incoming call to _CallKit_, but you may also want to extract application-specific headers for the call. Use the method `+[SINPushHelper queryPushNotificationPayload:]` to extract call details from the raw push payload. Note that you can do this immediately and before you have created and started a `SINClient` instance.

__Example__

```objectivec

- (void)managedPush:(id<SINManagedPush>)managedPush
    didReceiveIncomingPushWithPayload:(NSDictionary *)payload
                              forType:(NSString *)pushType {

  id<SINNotificationResult> notification = [SINPushHelper queryPushNotificationPayload:payload];

  if ([notification isValid]) {
    id<SINCallNotificationResult> callNotification = [notification callResult];

    // Use SINCallNotificationResult to extract remote User ID, call headers, etc.
  }

```

Also see reference documentation for [SINManagedPush](reference\html\Protocols\SINManagedPush.html) and [SINCallNotificationResult](reference\html\Protocols\SINCallNotificationResult.html).

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

Typically a _Debug_ build will be code signed with a _Development_ provisioning profile and thus `SINAPSEnvironmentDevelopment` should be used. And typically a _Release_ build will be code signed with a _Distribution_ provisioning profile and thus `SINAPSEnvironmentProduction` should be used. Instead of changing this manually for each build, the macro `SINAPSEnvironmentAutomatic` is available which automatically expands to _Development_ for _Debug_ builds and _Production_ for _Release_ builds.

## Relevant Apple Resources

For more details on Apple _PushKit_ and _CallKit_, see following _Apple Developer Documentation_:

* [PushKit](https://developer.apple.com/documentation/pushkit)
* [CallKit](https://developer.apple.com/documentation/callkit)
* [Responding to VoIP Notifications from PushKit](https://developer.apple.com/documentation/pushkit/responding_to_voip_notifications_from_pushkit)

## SINManagedPush and SINClient Interaction

This section covers details on how `SINManagedPush` and `SINClient` interacts toghether (automatically).

`SINManagedPush` will make use of `PKPushRegistry` to acquire a push device token. If any `SINClient` instances exist, it will register the token via `-[SINClient registerPushNotificationDeviceToken:type:apsEnvironment:]`, which will in turn register the token with the Sinch backend platform. If no instance of `SINClient` exists when `SINManagedPush` initially acquire the token, it will hold on to the token (in-process memory only) and register it with any `SINClient` that is created later during the whole application life-cycle.
