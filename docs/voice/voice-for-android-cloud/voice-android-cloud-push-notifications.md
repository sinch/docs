---
title: Push Notifications
excerpt: ''
hidden: false
next:
  pages:
    - voice-android-cloud-user-controller
---

## Receiving Incoming Calls via Push Notifications

The application can receive incoming calls only when:

- the application is in the foreground AND listening on an _active connection_ (_deprecated_)
- the application is registered to receive incoming calls via Push Notifications the using [UserController.registerUser()](reference/com/sinch/android/rtc/UserController.html).

The latter option is the only way the application can be notified of an incoming call if the phone is locked, or the application is in the background or closed.

Sinch SDK supports both currently available major Push Notification platforms on Android - [Google's Firebase Cloud Messages](doc:voice-android-cloud-push-notifications#google-fcm-push-notifications) (later FCM) and [Huawei Mobile Services Push Notifications](doc:voice-android-cloud-push-notifications#huawei-hms-notifications)  (Huawei Push or HMS Push). 

> 📘
>
> - Your application can be built to support both Push Notification platforms, but each _application instance_ should 
> register itself towards Sinch backend to receive Push Notification using either one way or another.

Registering towards Sinch backend to receive incoming call push notifications via FCM or HMS Push is quite [similar](doc:voice-android-cloud-push-notifications#fcm-vs-hms-push-registration-steps-comparison) and consists of several topics, which are covered below. 
The sections below will describe how to:
- Enable Support for the Managed Push
- Provision the Application with the Support Code
- Acquire a unique Device Token
- Register this Device Token on Sinch Backend
- Implement _Listening Service_


## Google FCM Push Notifications

### 1. Enable Support for the Managed Push

This step is the same for both FCM and HMS. To enable push notifications, set the following capability before starting the Sinch client:

```java
SinchClient sinchClient = Sinch.getSinchClientBuilder().context(context)
                              ...
                              .build();
...                              
sinchClient.setSupportManagedPush(true);
sinchClient.start();
```
> ❗️
>
> - You must catch the `MissingFCMException` if you distribute your app to devices without _Google Play Services_ and using _Firebase Cloud Messages_
> - Using `setSupportManagedPush(true)` will register a token with the _Firebase Cloud Messaging_ using a Sender ID connected to Sinch, which will _NOT_ unregister your own FCM token, so you _CAN_ use Firebase Cloud Messages for your own purpose filtering them in _onMessageReceived(RemoteMessage remoteMessage)_ method of your FCM Listening Service using Sinch helper API _SinchHelpers.isSinchPushPayload_.

> 📘
>
> - Using the FCM Push platform requires that the _Google Play Services_ is installed on the device. If you distribute your application through other channels than Google Play, push notifications will not be available on devices that do not have _Google Play Services_. 
> - If neither _Firebase Cloud Messaging_ nor _HMS Push_ is viable for the application, please consider deprecated [Active Connection](doc:voice-android-cloud-active-connection).

### 2. Provision the Application with the Support Code

Provisioning your application with the support code to receive the FCM Push Notifications is easy. You'll need to acquire a configuration file __google-services.json__ from the FCM console, add it to your project, and implement __FirebaseMessagingService__.

You can add Firebase to your app either semi-automatically using Android Studio, or manually [following this step-by-step official guide](https://firebase.google.com/docs/android/setup). In brief, to perform manual setup you first need to register your application in [firebase console](https://console.firebase.google.com/). If your project already uses FCM, the console will prompt you to import it as a new Firebase Cloud Messaging project. Register your application using the console, and download relevant _google-services.json_ into your project’s main folder.

Sample SDK projects _sinch-rtc-sample-push_ and _sinch-rtc-sample-video-push_ will require you to supply your own _google-services.json_ in order to be built. In the absence of this file, gradle will show a relevant error with explanation and relevant links and stop the build. That _google-services.json_ file is the main mean of automatization of support of Firebase services to your app. Android Studio’s _‘com.google.gms.google-services’_ plugin parses and adds relevant resources and permissions to your applications manifest automatically.

### 3. Acquire FCM Device Token

This step is performed automatically by the Sinch Client when you call [UserController.registerUser()](reference/com/sinch/android/rtc/UserController.html) on the next step.

### 4. Register FCM Device Token on Sinch Backend

Create an instance of the _UserController_ using the [UserControllerBuilder](reference/com/sinch/android/rtc/UserControllerBuilder.html) and call [UserController.registerUser()](reference/com/sinch/android/rtc/UserController.html) to acquire and register FCM token on the Sinch Backend, and wait for the callbacks that reports whether registration succeeded. See [User Controller](doc:voice-android-cloud-user-controller).

```java
UserController uc = Sinch.getUserControllerBuilder()
                .context(getApplicationContext())
                .applicationKey("<application key>")
                .userId("<user id>")
                .environmentHost("ocra.api.sinch.com")
                .build();
        uc.registerUser(this, this);
```

### 5. Implement _Listening Service_

Implement your _FcmListerningService_ by extending the _FirebaseMessagingService_:
```java
import com.google.firebase.messaging.FirebaseMessagingService;
import com.google.firebase.messaging.RemoteMessage;
...
public class FcmListenerService extends FirebaseMessagingService {

@Override
public void onMessageReceived(RemoteMessage remoteMessage){
    if (SinchHelpers.isSinchPushPayload(remoteMessage.getData())) {
        NotificationResult result = sinchClient.relayRemotePushNotificationPayload(remoteMessage.getData());
        ...
    } else {
        // it's NOT Sinch message - process yourself
    }
  }}
```

## Huawei HMS Notifications

### 1. Enable Support for the Managed Push

This step is the same for both FCM and HMS. To enable push notifications, set the following capability before starting the Sinch client:

```java
SinchClient sinchClient = Sinch.getSinchClientBuilder().context(context)
                              ...
                              .build();
...                              
sinchClient.setSupportManagedPush(true);
sinchClient.start();
```

> 📘
>
> - Using the HMS Push platform requires that the _Huawei Mobile Services_ is installed on the device. The UI prompt to install the _HNS_ will appear automatically the very first time  a unique _HMS device token_ is being acquired. 
> - If neither _Firebase Cloud Messaging_ nor _HMS Push_ is viable for the application, please consider deprecated [Active Connection](doc:voice-android-cloud-active-connection).

### 2. Provision the Application with the Support Code

Please follow the [Huawei Push Kit Devlopment Process](https://developer.huawei.com/consumer/en/doc/HMSCore-Guides-V5/android-dev-process-0000001050263396-V5) to acquire __agconnect-services.json__. Please refer to [How to Integrate HMS Core SDK](https://developer.huawei.com/consumer/en/doc/HMSCore-Guides-V5/android-integrating-sdk-0000001050040084-V5) for the necessary changes in the __gradle__ build files.

### 3. Acquire HMS Device Token

Unlike FCM, there is no way Sinch SDK can acquire a unique _HMS device token_. So, it should be acquired by the application __before__ creating _UserController_. A good example how to acquire an _HMS device token_ asynchronously is provided in the `RegisterToHmsTask` class of the `sinch-rtc-sample-hms-push` sample application.

The task extends `AsyncTask` and returns both known beforehand `mHmsApplicationId` and unique `mHmsDeviceToken`, which Huawei recommends to re-acquire on each application start. To read the  HMS Application ID and acquire HMS Device token use following methods: 

```java
    String hmsApplicationId = AGConnectServicesConfig.fromContext(context).getString("client/app_id");
    String hmsDeviceToken = HmsInstanceId.getInstance(context).getToken(appId, "HCM");
```

> ❗️
>
> To make this operation succeed, your application needs to be signed, and its signature fingerprint registered in the `Huawei AGConnect` console. To learn more, please follow this [link](https://developer.huawei.com/consumer/en/doc/HMSCore-Guides-V5/android-config-agc-0000001050170137-V5#EN-US_TOPIC_0000001050170137__section193351110105114).

> 📘
>
> Checklist for Obtaining the HMS Device Token
> - Application is registered on Huawei AGConnect Console (IMPORTANT: package name should match)
> - The AGConnect Console's application _project_ has PushKit enabled
> - The application is __signed__
> - Fingerprint of the signature is registered in the AGConnect Console 
> - HMS is installed on the device (User will get UI Prompt automatically)
> - Device is connected to the Internet

### 4. Register HMS Device Token on Sinch Backend

Create an instance of the _UserController_ using the [UserControllerBuilder](reference/com/sinch/android/rtc/UserControllerBuilder.html) and sub-builder [HmsPushBuilder](reference/com/sinch/android/rtc/HmsPushBuilder.html), accessible via `UserControllerBuilder.hms()` method. Then call [UserController.registerUser()](reference/com/sinch/android/rtc/UserController.html) to acquire and register FCM token on the Sinch Backend, and wait for the callbacks that reports whether registration succeeded. See [User Controller](doc:voice-android-cloud-user-controller):

```java
UserController uc = Sinch.getUserControllerBuilder()
                    .context(getApplicationContext())
                    .applicationKey("<application key>")
                    .userId("<user id>")
                    .environmentHost("ocra.api.sinch.com")
                    .hms()
                        .deviceToken(hmsDeviceToken)
                        .applicationId(hmsApplicationId)
                        .done()
                    .build();

        uc.registerUser(this, this);
```        

### 5. Implement _Listening Service_

Implement your _HmsListerningService_ by extending the _HmsMessageService_:
```java
import com.huawei.hms.push.HmsMessageService;
import com.huawei.hms.push.RemoteMessage;
...
public class HmsListenerService extends HmsMessageService {

@Override
public void onMessageReceived(RemoteMessage remoteMessage){
    if (SinchHelpers.isSinchPushPayload(remoteMessage.getDataOfMap())) {
        NotificationResult result = sinchClient.relayRemotePushNotificationPayload(remoteMessage.getDataOfMap());
        ...
    } else {
        // it's NOT Sinch message - process yourself
    }
  }}
```

## Sample Applications

As a developer, you will be responsible for implementing the code that receives the FCM push message. 
- For FCM example, please see the sample apps `sinch-rtc-sample-push` and `sinch-rtc-sample-video-push` which are bundled with the SDK.
- For HMS example, please see the sample app `sinch-rtc-sample-hms-push`.

The following sections cover how to support receiving calls and messages via push notifications.

## Receive and Forward Push Notifications to a Sinch Client

Once you have received the `RemoteMessage` in your _listening service_, and forwarded it to the Sinch client using the method [SinchClient.relayRemotePushNotificationPayload](reference/com/sinch/android/rtc/SinchClient.html), you'll get a [NotificationResult](reference/com/sinch/android/rtc/NotificationResult.html), which can be inspected to further confirm that the push is valid and associated with an incoming call using [NotificationResult.isCall()](reference/com/sinch/android/rtc/NotificationResult.html).

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
  // For HMS use remoteMessage.getDataOfMap() API instead:
  // NotificationResult result = sinchClient.relayRemotePushNotificationPayload(remoteMessage.getDataOfMap());
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
  // For HMS use remoteMessage.getDataOfMap() API instead:
  // NotificationResult result = sinchClient.relayRemotePushNotificationPayload(remoteMessage.getDataOfMap());
  if (result.isCall()) {
    CallNotificationResult callResult = result.getCallResult();
    Map<String, String> customHeaders = callResult.getHeaders());
    // analyse headers, decide whether to process message/call and start Sinch client or ignore
    ...
  }
}
```

## Unregister a Device

If the user of the application logs out or performs a similar action, which implies that this device should not receive an incoming calls push notifications any longer, the push notification device token can be unregistered via [UserController.unregisterPushToken()](reference/com/sinch/android/rtc/UserController.html).

> ❗️
>
> If your application assumes frequent change of users (logging in and out), it's imperative to unregister device via using `UserController.unregisterPushToken()` on each log out to guarantee that a new user won't receive incoming calls intended to the previous one.

## GCM to FCM Migration

Sinch SDK moved from deprecated _Google Cloud Messaging_ (GCM) to its most up-to-date and Google-recommended version _Firebase Cloud Messaging_ (FCM), which requires client app to be modified in accordance with the Google’s official [GCM to FCM migration guide](https://developers.google.com/cloud-messaging/android/android-migrate-fcm)

## Permissions Required

You don't need to manually add any permission to the application manifest - all required changes will be added automatically by the gradle depending on the configuration file you provide (__google-service.json__ or __agconnect-services.json__).

## FCM Vs HMS Push Registration Steps Comparison

| Step                                                              | FCM Specific            | HMS Specific                | Notes                                                             | 
| ------------------------------------------------------------------|-------------------------|-----------------------------|-------------------------------------------------------------------|
|1. Enable support of the Managed Push in _Sinch Client_|||Steps are identical|
|2. Provision your application with FCM / HMS support code.| Use __gooogle-services.json__| Use __agconnect-services.json__| Acquire files online in FCM/HMS Consoles|
|3. Acquire a unique _device token_ from FCM / MHS| automatic|[example](doc:voice-android-cloud-push-notifications#3-acquire-hms-device-token)||
|4. Register the _device token_ on the Sinch Backend| automatic|automatic||
|5. Implement _listening service_ | derive from `FirebaseMessagingService`| derive from `HmsMessageService`| Minor differences in the `RemoteMessage` API|
|| use `RemoteMessage.getData()`| use `RemoteMessage.getDataOfMap()`| |