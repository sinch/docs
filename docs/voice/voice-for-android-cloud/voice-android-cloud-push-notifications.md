---
title: Push Notifications
excerpt: ''
hidden: false
next:
  pages:
    - voice-android-cloud-user-controller
---

## Receiving Incoming Calls via Push Notifications

The application may receive incoming calls only when:

- the application is in the foreground AND listening on active connection.
- the application is registered to receive incoming calls via the Push Notifications via _SinchClient_ capabilities, or, better, using [UserController.registerUser()](reference/com/sinch/android/rtc/UserController.html).

When an application is not running, the user must be notified of an incoming call by a push notification.

By invoking [SinchClient.setSupportManagedPush(true)](reference/com/sinch/android/rtc/SinchClient.html) the Sinch SDK will automatically register to _Firebase Cloud Messaging_ and the Sinch backend will initiate push messages to your application when needed. This feature requires _Google Play Services_ on the device. If you distribute your application through other channels than Google Play, push notifications will not be available on devices that do not have _Google Play Services_.

If using the Sinch backend and _Firebase Cloud Messaging_ is not viable in the application, please see [Active Connection](doc:voice-android-cloud-active-connection).

As a developer, you will be responsible for implementing the code that receives the FCM push message. For an example, please see the sample app `sinch-rtc-sample-push` which is bundled with the SDK.

The following sections cover how to support receiving calls and messages via push notifications.

## FCM Configuration File Required (google-services.json)

You can add Firebase to your app either semi-automatically using Android Studio, or manually [following this step-by-step official guide](https://firebase.google.com/docs/android/setup). In brief, to perform manual setup you first need to register your application in [firebase console](https://console.firebase.google.com/). If your project already uses FCM, the console will prompt you to import it as a new Firebase Cloud Messaging project. Register your application using the console, and download relevant _google-services.json_ into your project’s main folder.

Sample SDK projects _sinch-rtc-sample-push_ and _sinch-rtc-sample-video-push_ will require you to supply your own _google-services.json_ in order to be built. In the absence of this file, gradle will show a relevant error with explanation and relevant links and stop the build. That _google-services.json_ file is the main mean of automatization of support of Firebase services to your app. Android Studio’s _‘com.google.gms.google-services’_ plugin parses and adds relevant resources and permissions to your applications manifest automatically.

## Permissions Required

You don't need to manually add any permission to the application manifest.

## Enable Push Notifications

To enable push notifications, set the following capability before starting the Sinch client:

```java
SinchClient sinchClient = Sinch.getSinchClientBuilder().context(context)
                              ...
                              .build();
...                              
sinchClient.setSupportManagedPush(true);
sinchClient.start();
```

Or, better use [UserController.registerUser()](reference/com/sinch/android/rtc/UserController.html) which provides callbacks with the registration status.

> 📘
>
> - You must catch the `MissingGCMException` if you distribute your app to devices without _Google Play Services_.
> - Using `setSupportManagedPush(true)` will register a token with Firebase Cloud Messaging using a Sender ID connected to Sinch, which will _NOT_ unregister your own token, so you _CAN_ use Firebase Cloud Messages for your own purpose filtering them in _onMessageReceived(RemoteMessage remoteMessage)_ method of your FCM Listening Service using Sinch helper API _SinchHelpers.isSinchPushPayload_.

```java
public class FcmListenerService extends FirebaseMessagingService {

@Override
public void onMessageReceived(RemoteMessage remoteMessage){
    if (SinchHelpers.isSinchPushPayload(remoteMessage.getData())) {
        // it's Sinch message - relay it to SinchClient
    } else {
        // it's NOT Sinch message - process yourself
    }
  }}
```

### Explicit Push Token Registration

There are certain situations where it is either desirable to explicitly register push token and/or get assurance that the push token is indeed registered. These situations should be handled using the [UserController API](doc:voice-android-cloud-user-controller).

## Receive and Forward Push Notifications to a Sinch Client

For more details regarding how to implement receiving a FCM downstream message, please see the [Android developer site for FCM](https://firebase.google.com/docs/cloud-messaging/android/receive).

Once you have received the `RemoteMessage` in your `FirebaseMessagingService`, forward it to the Sinch client using the method [SinchClient.relayRemotePushNotificationPayload](reference/com/sinch/android/rtc/SinchClient.html).

```java
// (Ensure you have created a SinchClient)

if (SinchHelpers.isSinchPushPayload(remoteMessage.getData())) {
    NotificationResult result = sinchClient.relayRemotePushNotificationPayload(remoteMessage.getData());
}
```

The returned [NotificationResult](reference/com/sinch/android/rtc/NotificationResult.html) can be inspected to further confirm that the push is valid and associated with an incoming call using [NotificationResult.isCall()](reference/com/sinch/android/rtc/NotificationResult.html).

If the payload that was forwarded to the Sinch client is a valid Sinch call, the `onIncomingCall` callback will automatically be triggered as for any other call. Notification result can be further examined using [NotificationResult.getCallResult()](reference/com/sinch/android/rtc/NotificationResult.html) object provides details about participants, whether the call timed out and whether the call offers video.

## Send and Receive Custom Headers via Sinch Managed Push

The Sinch SDK supports adding custom headers in push notification messages when initiating a call, so developers do not need to implement their own push mechanism if they only need to deliver small pieces of information along the Sinch managed push between their app instances. The Sinch SDK allows up to _1024_ bytes of custom headers.

Setting custom headers on the sender side when initiating a call:

```java
// setting up custom headers
Map<String,String> headers = new HashMap<>();
headers.put("The first value is ", "@123");
headers.put("Custom value ", "two!");
Call call = callClient.callUser(userId, headers);
```

If custom headers were supplied by call initiator, they can be retrieved from notification result using `getHeaders()` API:

```java
// make sure you have created a SinchClient
if (SinchHelpers.isSinchPushPayload(remoteMessage.getData())) {
  NotificationResult result = sinchClient.relayRemotePushNotificationPayload(remoteMessage.getData());
  if (result.isCall()) {
    CallNotificationResult callResult = result.getCallResult();
    Map<String, String> customHeaders = callResult.getHeaders());
  }
}
```

> 📘
>
> It is possible to retrieve custom headers from the push message using _SinchHelpers.queryPushNotificationPayload_ without starting the client.

```java
// SinchClient is not needed to be created at all!
if (SinchHelpers.isSinchPushPayload(remoteMessage.getData())) {
  NotificationResult result = SinchHelpers.queryPushNotificationPayload(applicationContext, remoteMessage.getData());
  if (result.isCall()) {
    CallNotificationResult callResult = result.getCallResult();
    Map<String, String> customHeaders = callResult.getHeaders());
    // analyse headers, decide whether to process message/call and start Sinch client or ignore
    ...
  }
}
```

## Unregister a Device

If the user of the application logs out or performs a similar action, the push notification device token can be unregistered via [SinchClient.unregisterManagedPush()](reference/com/sinch/android/rtc/SinchClient.html)` will register the device again.

> ❗️
>
> If your application assumes frequent change of users (logging in and out), it's imperative to unregister device via `SinchClient.unregisterManagedPush()` or, better, using `UserController.unregisterPushToken()` on each log out to guarantee that a new user won't receive incoming calls intended to the previous one.

## GCM to FCM Migration

Sinch SDK moved from deprecated _Google Cloud Messaging_ (GCM) to its most up-to-date and Google-recommended version _Firebase Cloud Messaging_ (FCM), which requires client app to be modified in accordance with the Google’s official [GCM to FCM migration guide](https://developers.google.com/cloud-messaging/android/android-migrate-fcm)