---
title: Push Notifications
excerpt: ''
hidden: false
next:
  pages:
    - voice-android-cloud-user-controller
---

## Receiving Incoming Calls via Push Notifications

To receive incoming calls via Push Notifications the _application instance_ has to be registered on Sinch Backend using [UserController.registerUser()](reference/com/sinch/android/rtc/UserController.html). Push Notifications allow to receive an incoming call even if the phone is locked, or the application is in the background or closed.

Sinch SDK supports both currently available major Push Notification platforms on Android - [Google's Firebase Cloud Messages](doc:voice-android-cloud-push-notifications#google-fcm-push-notifications) (later FCM) and [Huawei Mobile Services Push Notifications](doc:voice-android-cloud-push-notifications#huawei-hms-notifications)  (Huawei Push or HMS Push). 

> 📘
> Your application can be built to support both Push Notification platforms, but each _application instance_ should 
> register itself towards Sinch backend to receive Push Notification using either one way or another.

Registering towards Sinch backend to receive incoming call push notifications via FCM or HMS Push is quite [similar](doc:voice-android-cloud-push-notifications#fcm-vs-hms-push-registration-steps-comparison) and consists of several topics, which are covered below. 
The sections below will describe how to:
- Enable support for _Sinch Managed Push_
- Provision the Application with the Support Code
- Acquire a unique Device Token
- Register this Device Token on Sinch Backend
- Implement _Listening Service_


## Google FCM Push Notifications

### 1. Enable Support for Managed Push

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
> You must catch the `MissingFCMException` if you distribute your app to devices without _Google Play Services_ and using _Firebase Cloud Messages_

> 📘
> Using `setSupportManagedPush(true)` will register a token with the _Firebase Cloud Messaging_ using a _sender ID_ owned by Sinch. It will __not__ unregister FCM tokens associated with your own _sender ID_. This means you can use FCM for your own purpose, filtering them in _onMessageReceived(RemoteMessage remoteMessage)_ method of your FCM Listening Service using Sinch helper API _SinchHelpers.isSinchPushPayload_.

> 📘
> Using FCM requires that the _Google Play Services_ is installed on the device. If you distribute your application through other channels than Google Play, push notifications will not be available on devices that do not have _Google Play Services_. 

### 2. Provision the Application with the Support Code

Provisioning your application with the support code to receive the FCM Push Notifications is easy. You'll need to acquire a configuration file __google-services.json__ from the FCM console, and add it to your project.

You can add Firebase to your app either semi-automatically using Android Studio, or manually [following this step-by-step official guide](https://firebase.google.com/docs/android/setup). In brief, to perform manual setup you first need to register your application in [firebase console](https://console.firebase.google.com/). If your project already uses FCM, the console will prompt you to import it as a new Firebase Cloud Messaging project. Register your application using the console, and download relevant _google-services.json_ into your project’s main folder.

Sample SDK projects _sinch-rtc-sample-push_ and _sinch-rtc-sample-video-push_ will require you to supply your own _google-services.json_ in order to be built. In the absence of this file, gradle will show a relevant error with explanation and relevant links and stop the build. That _google-services.json_ file is the main mean of automatization of support of Firebase services to your app. Android Studio’s plugin `com.google.gms.google-services` parses and adds relevant resources and permissions to your applications manifest automatically.

### 3. Acquire FCM Device Token

This step is performed automatically when you call [UserController.registerUser()](reference/com/sinch/android/rtc/UserController.html) on the next step.

### 4. Register FCM Device Token on Sinch Backend

Create an instance of the _UserController_ using the [UserControllerBuilder](reference/com/sinch/android/rtc/UserControllerBuilder.html) and call [UserController.registerUser()](reference/com/sinch/android/rtc/UserController.html) to acquire and register FCM token on the Sinch Backend, and wait for the callbacks that reports whether registration succeeded. Please see dedicated `UserContoller` documentation [here](doc:voice-android-cloud-user-controller).

```java
UserController userController = Sinch.getUserControllerBuilder()
                .context(getApplicationContext())
                .applicationKey("<application key>")
                .userId("<user id>")
                .environmentHost("ocra.api.sinch.com")
                .build();
userController.registerUser(this, this); // parameters are `UserRegistrationCallback` and `PushTokenRegistrationCallback`
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

### Prerequisites

The following steps 1-5 will guide you through how to enable Huawei Push together with the Sinch SDK in your Android client-side application. To use Huawei Push notifications you must also implement the [Huawei OAuth 2.0 Flow](doc:voice-android-cloud-push-notifications#huawei-oauth-20-flow).

### 1. Enable Support for Managed Push

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
> Using the HMS Push platform requires that the _Huawei Mobile Services_ is installed on the device. The UI prompt to install the _HMS_ will appear automatically the very first time  a unique _HMS device token_ is being acquired. 

### 2. Provision the Application with the Support Code

Please follow the [Huawei Push Kit Devlopment Process](https://developer.huawei.com/consumer/en/doc/HMSCore-Guides-V5/android-dev-process-0000001050263396-V5) to acquire __agconnect-services.json__. Please refer to [How to Integrate HMS Core SDK](https://developer.huawei.com/consumer/en/doc/HMSCore-Guides-V5/android-integrating-sdk-0000001050040084-V5) for the necessary changes in the __gradle__ build files.

### 3. Acquire HMS Device Token

Unlike FCM, Sinch SDK expects the application to acquire the _HMS device token_ __before__ creating _UserController_. A good example how to acquire an _HMS device token_ asynchronously is provided in the `RegisterToHmsTask` class of the `sinch-rtc-sample-hms-push` sample application.

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

Create an instance of the _UserController_ using the [UserControllerBuilder](reference/com/sinch/android/rtc/UserControllerBuilder.html) and sub-builder [HmsPushBuilder](reference/com/sinch/android/rtc/HmsPushBuilder.html), accessible via `UserControllerBuilder.hms()` method. Then call [UserController.registerUser()](reference/com/sinch/android/rtc/UserController.html) to acquire and register FCM token on the Sinch Backend, and wait for the callbacks that reports whether registration succeeded. Please see dedicated `UserContoller` documentation [here](doc:voice-android-cloud-user-controller).

```java
UserController userController = Sinch.getUserControllerBuilder()
                    .context(getApplicationContext())
                    .applicationKey("<application key>")
                    .userId("<user id>")
                    .environmentHost("ocra.api.sinch.com")
                    .hms()
                        .deviceToken(hmsDeviceToken)
                        .applicationId(hmsApplicationId)
                        .done()
                    .build();

userController.registerUser(this, this); // parameters are `UserRegistrationCallback` and `PushTokenRegistrationCallback`
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

As a developer, you will be responsible for implementing the code that receives the push message. 
- For FCM example, please see the sample apps `sinch-rtc-sample-push` and `sinch-rtc-sample-video-push`.
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

If the user of the application logs out or performs a similar action, which implies that this device should not receive incoming calls push notifications any longer, the push notification device token can be unregistered via [UserController.unregisterPushToken()](reference/com/sinch/android/rtc/UserController.html).

> ❗️
>
> If your application assumes frequent change of users (logging in and out), it's imperative to unregister device via using `UserController.unregisterPushToken()` on each log out to guarantee that a new user won't receive incoming calls intended to the previous one.

## GCM to FCM Migration

Sinch SDK moved from deprecated _Google Cloud Messaging_ (GCM) to its most up-to-date and Google-recommended version _Firebase Cloud Messaging_ (FCM), which requires client app to be modified in accordance with the Google’s official [GCM to FCM migration guide](https://developers.google.com/cloud-messaging/android/android-migrate-fcm)

## Permissions Required

You don't need to manually add any permission to the application manifest - all required changes will be added automatically by the __gradle__ depending on the configuration file you provide (__google-service.json__ or __agconnect-services.json__).

## FCM vs HMS Push Registration Steps Comparison

| Step                                                              | FCM Specific            | HMS Specific                | Notes                                                             | 
| ------------------------------------------------------------------|-------------------------|-----------------------------|-------------------------------------------------------------------|
|1. Enable support of the Managed Push in _Sinch Client_|||Steps are identical|
|2. Provision your application with FCM / HMS support code.| Use __gooogle-services.json__| Use __agconnect-services.json__| Acquire files online in FCM/HMS Consoles|
|3. Acquire a unique _device token_ from FCM / MHS| Automatic|[Example](doc:voice-android-cloud-push-notifications#3-acquire-hms-device-token)||
|4. Register the _device token_ on the Sinch Backend| Automatic|Automatic||
|5. Implement _listening service_ | Derive from `FirebaseMessagingService`,| Derive from `HmsMessageService`,| Minor differences in the `RemoteMessage` API|
|| Use `RemoteMessage.getData()`.| Use `RemoteMessage.getDataOfMap()`.| |


## Huawei OAuth 2.0 Flow

Sinch supports Huawei push messages via [Huawei Push Kit](https://developer.huawei.com/consumer/en/doc/development/HMSCore-Guides/service-introduction-0000001050040060) (_HPK_) which is part of [Huawei Mobile Services](https://developer.huawei.com/consumer/en/hms) (_HMS_). The Sinch SDKs and platform can take care of sending push notification messages via _Huawei Push Kit_ on your behalf as part our _Sinch Managed Push Notifications_ functionality. To enable Huawei push messages for Android devices you will need to do two things:

1. Make use of the _Huawei_ (_HMS_) APIs in the Sinch Android SDK [as described above](doc:voice-android-cloud-push-notifications#huawei-hms-notifications).
2. Implement an _OAuth 2.0 Authorization Server_ endpoint that can provide Sinch with _OAuth 2.0_ _access_tokens_ required to send messages via _HPK_ on your behalf.

Sinch will send push notification messages via the [Huawei Push Kit API](https://developer.huawei.com/consumer/en/doc/HMSCore-References-V5/https-send-api-0000001050986197-V5). Huawei Push Kit supports (and requires) an _OAuth 2.0_ _Client Credentials_ flow to authenticate against the _Huawei Push Kit_ server endpoint(s). Your Huawei app in Huawei _AppGallery Connect_ will have an _App ID_ and an _App secret_. These are to be used as OAuth client credentials, i.e. `client_id` and `client_secret` respectively.

Sinch supports Huawei OAuth flow by delegation. You will keep your `client_secret` on your backend, and Sinch will request an OAuth `access_token` via an _Authorization Server_ endpoint that you implement. The overall flow is depicted below:

![Sinch - Huawei OAuth flow](images\20200922-sinch-huawei-hpk-oauth.png)

Key takeaways:

1. When Sinch need an `access_token` required to send a push message to _Huawei Push Kit_ server, it will make an OAuth request using a _Client Credentials_ grant type to your _Authorization Server_. This request will be specifying and `client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer` will as value for `client_assertion` provide a JWT that is symmetrically signed with your _Sinch Application Secret_.
2. Your _Authorization Server_ should validate the JWT provided as `client_assertion` by Sinch and that the signed JWT is signed with your _Sinch Application Secret_.
3. Your _Authorization Server_ should request an `access_token` using the Huawei HMS OAuth Authoriation Server endpoint and your Huawei _App ID_ and _App secret_ as `client_id` and `client_secret`.
4. Your _Authorization Server_ should pass the `access_token` (as received from HMS) in the response back to Sinch.

Details on how to validate the JWT provided by Sinch as `client_assertion` are available in the following sections.

(The use of `client_assertion` and `client_assertion_type=urn:ietf:params:oauth:client-assertion-type:jwt-bearer` is based on [RFC 7523](https://tools.ietf.org/html/rfc7523) and [RFC 7521](https://tools.ietf.org/html/rfc7521#section-4.2) and part of the [OpenID Connect Core 1.0 standard](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication).)

### Validating the `client_assertion` JWT provided by Sinch

As described in the overview, Sinch will make a request to your OAuth _Authorization Server_ endpoint, requesting a _HMS_ `access_token`. The request will be on the following form:

```
POST /sinch/rtc/push/oauth2/v1/huawei-hms/token HTTP 1.1
Host: as.your-domain.com
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials&
scope=https%3A%2F%2Fpush-api.cloud.huawei.com&
client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
client_assertion=<JWT>
```

> 📘
> __Note__: The value of `scope` and `client_assertion_type` in the example above are URL-encoded

The `JWT` will be making use of the standard JWT header parameters `alg` and `kid`, and the standard claims `iss`, `sub`, `iat`, `exp`, `nonce` and `aud`. Before we jump to the details of how to validate this token, here is an example:

```
// JWT Header (example)
{
  "alg": "HS256",
  "kid": "hkdfv1-20200901",
  "sinch:rtc:application_key": "a32e5a8d-f7d8-411c-9645-9038e8dd051d"
}

// JWT Payload (example)
{
  "iss": "//rtc.sinch.com/applications/a32e5a8d-f7d8-411c-9645-9038e8dd051d",
  "sub": "123456789",
  "aud": "https://as.your-domain.com/sinch/rtc/push/oauth2/v1/huawei-hms/token",
  "scope": "https://push-api.cloud.huawei.com",
  "sinch:rtc:application_key": "a32e5a8d-f7d8-411c-9645-9038e8dd051d",
  "iat": 1600780504,
  "exp": 1600784104,
  "nonce": "6b438bda-2d5c-4e8c-92b0-39f20a94b34e"
}
```

* Claim `iss` is on the form `//rtc.sinch.com/applications/<your Sinch Application Key>` (canonical form)
* Claim `sub` is your _HMS App ID_  (as specified via `HmsPushBuilder.applicationId(String)` on the Android client).
* Claim `aud` will be set to the _Authorization Server_ token endpoint you have configured with Sinch. E.g. `https://as.your-domain.com/sinch/rtc/push/oauth2/v1/huawei-hms/token`
* Claim `scope` will be `https://push-api.cloud.huawei.com` (representing the Huawei _Push Kit_ server domain)
* Claim `sinch:rtc:application_key` will contain your _Sinch Application Key_`.
* Claims `iat`, `exp`, `nonce` are standard JWT claims (see [JWT RFC 7519](https://tools.ietf.org/html/rfc7519))


> 📘
> __Note__: Your _Sinch Application Key_ is present both in the JWT header and the JWT payload (as header parameter and claim `sinch:rtc:application_key`). The reason is it allows you to implement validating the JWT signature without accessing the payload, and once you have validated the JWT signature, you can strip away the header and all the data you need for further processing is self contained in the payload.

#### `kid` and Deriving a Signing Key from Sinch Application Secret

The `kid` parameter in the JWT header is on the form `hkdfv1-{DATE}` where `{DATE}` is date of signing in UTC on format `YYYYMMDD`.

When validating the JWT, use a signing key that is derived from your _Sinch Application Secret_ as follows. Given:

- A function `HMAC256(key, message)`.
- A date-formatting function `FormatDate(date, format)`.
- The date of signing as variable `signedAt`. This is to be extracted from the JWT header parameter `kid`.
- _Sinch Application Secret_ as variable `applicationSecret`, holding the secret as a _base64_ encoded string.

, derive the signing key as follows:

```
signingKey = HMAC256(BASE64-DECODE(applicationSecret), UTF8-ENCODE(FormatDate(signedAt, "YYYYMMDD")))
```

(__NOTE__: This is the same key derivation scheme as used for [Token-based User registration](doc:voice-android-cloud-application-authentication))

#### Validating the JWT

Your _Authorization Server_ should validate the JWT in accordance with section [RFC 7523 - Section 3. JWT Format and Processing Requirements](https://tools.ietf.org/html/rfc7523#section-3). Here is a rough outline of the steps necessary:

1. Use JWT header parameter `sinch:rtc:application_key` to lookup your corresponding _Application Key_ and _Application Secret_ (at this point the token is still unvalidated)
2. Derive the signing key (as detailed in the previous section)
3. Validate that the JWT has a valid signature given the signing that you have derived.
4. Validate the JWT payload in terms of `iat`, `exp`, `nonce` etc.
5. Validate that the claim `scope` is `https://push-api.cloud.huawei.com`

> 👍
> Example code is available at [https://github.com/sinch/sinch-rtc-api-auth-examples](https://github.com/sinch/sinch-rtc-api-auth-examples).

### Acquiring an `access_token` from Huawei HMS

After validating the JWT client assertion, your _Authorization Server_ should in turn request an `access_token` from the Huawei HMS OAuth endpoint, using your Huawei _App ID_ and _App secret_ as `client_id` and `client_secret`. The access token received from Huawei should then be included in the response back to Sinch. See [Huawei documentation](https://developer.huawei.com/consumer/en/doc/HMSCore-Guides/open-platform-oauth-0000001050123437-V5#EN-US_TOPIC_0000001050123437__section12493191334711) for how to implement requesting an OAuth _access token_ using Huawei HMS.

Example response to Sinch:

```
HTTP/1.1 200 OK
Content-Type: application/json;charset=utf-8
{
  "access_token": "<access token acquired from Huawei>",
  "expires_in": 3600,
  "token_type": "Bearer"
}
```

Sinch will then be able to use this `access_token` to send push messages to your end-user devices until the token expires, upon which Sinch will issue a new token request to your _Authorization Server_.

__NOTE__: You will receive your _HMS App ID_ in JWT claim `sub` and you can use that to for a given request map it to your corresponding _HMS App_. You will also be able to access your _Sinch Application Key_ as the JWT claim `sinch:rtc:application_key` if you need that as input at this stage.

### Rejecting the `access_token` grant request

If your _Authorization Server_ rejects the access token request from Sinch, it should respond with a HTTP response that is compliant with the [OAuth 2.0 standard](https://www.oauth.com/oauth2-servers/access-tokens/access-token-response/). Example:

```
HTTP/1.1 400 Bad Request
Content-Type: application/json;charset=utf-8
{
  "error": "unauthorized_client",
  "error_description": "Your helpful error description"
}
```

Please see [https://www.oauth.com/oauth2-servers/access-tokens/access-token-response/](https://www.oauth.com/oauth2-servers/access-tokens/access-token-response/) for details on how to formulate conformant OAuth error responses.


### More Resources on Huawei Push and OAuth

* Examples how to validate a client assertion JWT at [https://github.com/sinch/sinch-rtc-api-auth-examples](https://github.com/sinch/sinch-rtc-api-auth-examples).
* [Huawei HMS OAuth Client Credentials Flow](https://developer.huawei.com/consumer/en/doc/HMSCore-Guides/open-platform-oauth-0000001050123437-V5#EN-US_TOPIC_0000001050123437__section12493191334711)
* [OAuth 2.0 Client Credentials](https://www.oauth.com/oauth2-servers/access-tokens/client-credentials/)
* [OAuth `client_secret_jwt`](https://openid.net/specs/openid-connect-core-1_0.html#ClientAuthentication)
* [RFC 7523 - JSON Web Token (JWT) Profile for OAuth 2.0 Client Authentication and Authorization Grants](https://tools.ietf.org/html/rfc7523)
* [RFC 7521 - Assertion Framework for OAuth 2.0 Client Authentication and Authorization Grants](https://tools.ietf.org/html/rfc7521#section-4.2)
