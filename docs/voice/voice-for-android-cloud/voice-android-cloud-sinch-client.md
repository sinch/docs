---
title: Sinch Client
excerpt: >-
  The Sinch SDK is a product that makes adding voice and video calling to mobile
  apps easy. Continue reading this step-by-step guide now.
hidden: false
next:
  pages:
    - voice-android-cloud-application-authentication
---

The _SinchClient_ is the Sinch SDK entry point. It is used to configure the user’s and device’s capabilities, as well as to provide access to feature classes such as the _CallClient_, _AudioController_ and _VideoController_.

## Create the _SinchClient_

Set up the Sinch client, using [SinchClientBuilder](https://sinch.github.io/docs/voice/voice-for-android-cloud/reference/com/sinch/android/rtc/SinchClientBuilder.html):

```java
// Instantiate a SinchClient using the SinchClientBuilder.
android.content.Context context = this.getApplicationContext();
SinchClient sinchClient = Sinch.getSinchClientBuilder().context(context)
                                                       .applicationKey("<application key>")
                                                       .environmentHost("ocra.api.sinch.com")
                                                       .userId("<user id>")
                                                       .build();
```

* The _Application Key_ is obtained from the [Sinch Developer Dashboard - Apps](https://portal.sinch.com/#/apps). 
* The _User ID_ should uniquely identify the user on the particular device.
* (The term _Ocra_ in the hostname `ocra.api.sinch.com` is just the name for the Sinch API that the SDK clients target)

## Specify Capabilities

The [SinchClient](reference\com\sinch\android\rtc\SinchClient.html) can be configured to enable or disable certain functionality. To enable support for _push notifications_, use the method [SinchClient.setSupportManagedPush(true)](reference/com/sinch/android/rtc/SinchClient.html) for additional steps that are required to fully implement support for push notifications.

The following example shows how to set up the client with voice calling enabled.

```java
// Specify the client capabilities.
sinchClient.setSupportManagedPush(true);
```

> 📘
>
> If the application is meant to only make outbound calls but not receive incoming calls, the client will be ready to make calls after calling the start method and receiving [SinchClientListener.onClientStarted(...)](reference/com/sinch/android/rtc/SinchClientListener.html) callback.

> 📘
>
> If the application is meant to receive incoming calls while not running in foreground, [Push Notifications](doc:voice-android-cloud-push-notifications) are required.

## Start the Sinch Client

Before starting the client, add a [SinchClientListener](reference\com\sinch\android\rtc\SinchClientListener.html):

```java
sinchClient.addSinchClientListener(new SinchClientListener() {

    public void onClientStarted(SinchClient client) { }
    public void onClientFailed(SinchClient client, SinchError error) { }
    public void onRegistrationCredentialsRequired(SinchClient client, ClientRegistration registrationCallback) { 
      // You have to implement this method, it can't be no-op. 
    }
    public void onLogMessage(int level, String area, String message) { }
});

sinchClient.start();
```

When starting the client (`sinchClient.start()`) the client will ask for a token via [SinchClientListener.onRegistrationCredentialsRequired()](reference\com\sinch\android\rtc\SinchClientListener.html).
See section [Authentication & Authorization](doc:voice-android-cloud-application-authentication) for the details.


> 📘
>
> All listener callbacks emitted from the Sinch SDK are invoked on the same thread that the call to `SinchClientBuilder.build` is made on. If the invoking thread is _not_ the main-thread, it needs to have an associated `Looper`.

### Authorizing the Client / User

When the _SinchClient_ is started with a given _User ID_ it is required to provide an authorization token to register towards the _Sinch backend_. To authorize a client, implement [SinchClientListener.onRegistrationCredentialsRequired()](reference/com/sinch/android/rtc/SinchClientListener.html) that is cryptographically signed with the _Application Secret_. 

The sample applications included in the Sinch SDK includes a class `JWT` that describes how to create the _JWT_ and sign it with the _Application Secret_.

```java
class MySinchClientListener implements SinchClientListener {

        @Override
        public void onRegistrationCredentialsRequired(SinchClient client,
                                                      ClientRegistration registrationCallback) {
            String jwt = JWT.create("<application key>", "<application secret>", client.getLocalUserId());
            
            registrationCallback.register(jwt);
        }
}
```

See section [Authentication & Authorization](doc:voice-android-cloud-application-authentication) for the details.

> ⚠
>
> When deploying your application to production, do not embed the Application Secret in the application. The example above is only meant to show how to provide a signed JWT to the _SinchClient_. Implement the required functionality on your backend and fetch signed registration token when required.

## Registering the Client / User via UserController API

You can also register a user towards the _Sinch backend_ via [UserController API](doc:voice-android-cloud-user-controller). This lightweight component provides a way to register the user without starting the _SinchClient_. You can also register push token for [Managed Push](doc:voice-android-cloud-push-notifications) to receive incoming calls even when the application is closed/in background. The _UserController_ uses the very same authentication scheme as the _SinchClient_ based on the signed JWT registration token that you provide in response to _onRegistrationCredentialsRequired()_ method of [UserRegistrationCallback](reference\com\sinch\android\rtc\UserRegistrationCallback.html). The _UserController_ provides better control over the registration process than the _SinchClient_ by providing callbacks for each step of the registration.

## Lifecycle Management of a _SinchClient_-Instance

We recommend that you initiate the _SinchClient_, start it, but not terminate it, during the lifetime of the running application. That also implies that the _SinchClient_-instance should be _retained_ by the application code. It is best to keep the client instance alive and started unless there are reasons specific to your application. 

The _SinchClient_ can of course be completely stopped and also disposed. 

> 📘
>
> Stopping / disposing of _SinchClient_ won't affect receiving incoming calls if the user was previously registered towards the _Sinch backend_ via [UserController API](doc:voice-android-cloud-user-controller). Upon receiving _incoming call_ push notification instantiate and forward the push payload to the new _SinchClient_ instance. 

When the app is done using the `SinchClient`, it can be stopped and disposed using [SinchClient.terminateGracefully()](reference/com/sinch/android/rtc/SinchClient.html) is considered invalid.

Example of how to completely dispose the `SinchClient`:

```java
sinchClient.terminateGracefully();
sinchClient = null;
```
