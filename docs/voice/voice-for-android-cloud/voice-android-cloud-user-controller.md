---
title: User Controller
excerpt: ''
hidden: false
next:
  pages:
    - voice-android-cloud-miscellaneous
---

## Push Token Registration via _UserController_ API

> 📘
>
> [UserController](reference/com/sinch/android/rtc/UserController.html) provides a way, independently from the `SinchClient` lifecycle, to register a user for incoming calls via push notifications. You can also use it to un-register push token if receiving of incoming calls is no longer desirable (e.g. on logout, or changing users).

Sinch SDK supports both currently available major Push Notification platforms on Android - [Google's Firebase Cloud Messages](doc:voice-android-cloud-push-notifications#google-fcm-push-notifications) (later `FCM Push`) and [Huawei Mobile Services Push Notifications](doc:voice-android-cloud-push-notifications#huawei-hms-notifications)  (later `Huawei Push` or `HMS Push`). 

`UserController` provides a way explicitly register push token and get assurance that the push token is indeed registered, e.g.:

- The application is designed to receive calls only, and thus must register push token with the Sinch backend on the very first start, while it's desirable to terminate `SinchClient` as soon as the registration concludes (e.g. to free resources). In this situation, the application should be notified by a specific callback on the registration result.
- The application detects that FCM/HMS Push token is invalidated and should be refreshed and re-registered with Sinch backend. Here, if `SinchClient` is running, it would take care of re-registering of the push token itself, otherwise the application is responsible for re-registering.

Both situations should be handled using the [UserController](reference\com\sinch\android\rtc\UserController.html), which can be used independently from the _SinchClient_ (i.e., it does not require creating and starting the _SinchClient_).

### Create UserController for the Registration for FCM Push

```java
public UserController getUserController(String userId) {
     return Sinch.getUserControllerBuilder()
                 .context(getApplicationContext())
                 .applicationKey("<application key>")
                 .userId("<user id>")
                 .environmentHost("ocra.api.sinch.com")
                 .build();
}
```

### Create UserController for the Registration for HMS Push

```java
public UserController getUserController(String userId) {
     return Sinch.getUserControllerBuilder()
                 .context(getApplicationContext())
                 .applicationKey("<application key>")
                 .userId("<user id>")
                 .environmentHost("ocra.api.sinch.com")
                 .hms()
                    .deviceToken(hmsDeviceToken)
                    .applicationId(hmsApplicationId)
                    .done()
                 .build();
}
```
> 📘
>
> - Your application can be built to support both Push Notification platforms, but each _application instance_ should 
> register itself towards Sinch backend to receive Push Notification using either one way or another.
> - Please observe the only difference between FCM and HMS Push registrations is the use of the [hms()](reference/com/sinch/android/rtc/HmsPushBuilder.html) sub-builder.

The former situation is showcased in _LoginActivity.java_ in _sinch-rtc-sample-push_ and _sinch-rtc-sample-video-push_ sample applications. The activity implements [UserRegistrationCallback](reference\com\sinch\android\rtc\UserRegistrationCallback.html) and [PushTokenRegistrationCallback](reference\com\sinch\android\rtc\PushTokenRegistrationCallback.html) interfaces:

```java
public class LoginActivity extends BaseActivity implements SinchService.StartFailedListener, PushTokenRegistrationCallback, UserRegistrationCallback {

    private void loginClicked() {
        ...
        UserController userController = Sinch.getUserControllerBuilder()
                                             .context(getApplicationContext())
                                             .applicationKey("<application key>")
                                             .userId("<user id>")
                                             .environmentHost("ocra.api.sinch.com")
                                             .build();
        userController.registerUser(this, this);
    }

    @Override
    public void onUserRegistrationFailed(SinchError sinchError) {
        dismissSpinner();
        Toast.makeText(this, "Registration failed!", Toast.LENGTH_LONG).show();
    }

    @Override
    public void onUserRegistered() {
        // Instance is registered, but we'll wait for another callback, assuring that the push token is
        // registered as well, meaning we can receive incoming calls.
    }

    @Override
    public void tokenRegistrationFailed(SinchError sinchError) {
        dismissSpinner();
        Toast.makeText(this, "Push token registration failed - incoming calls can't be received!", Toast.LENGTH_LONG).show();
    }

    @Override
    public void tokenRegistered() {
        dismissSpinner();
        startClientAndOpenPlaceCallActivity();
    }

    @Override
    public void onCredentialsRequired(ClientRegistration clientRegistration) {
        // NOTE: This implementation just emulates what should be an async procedure, with JWT.create() being
        // run on your backend.
        clientRegistration.register(JWT.create(<application key>, <application secret>, mUserId));
    }
}
```

User registration is a two-step process, where the first the step is registering _user_ (after which you can make outgoing calls using the _SinchClient_), and the second is registering the push token for receiving incoming calls via FCM/HMS Push notifications. Each step has correspondent _success_ and _failure_ callbacks, where you are mostly interested in the _tokenRegistered_, After receiving it, you can terminate / close the application and be sure that incoming calls will be received.

![Token-based User Registration](images\20200221-user_and_push_registration.png)

> ❗️
>
> User Controller requires signed registration token the same way as it is required by the SinchClient for the first time. Provide it via [ClientRegistration.register()](reference\com\sinch\android\rtc\ClientRegistration.html) callback in response to _onCredentialsRequired()_.
> See more information about authentication [here](doc:voice-android-cloud-application-authentication).

When both conditions:
- SinchClient is started (optional, see the note below)
- push token is registered
are met, the authentication process has finished and e.g. UI can advance.

> 📘
>
> You don't need to start _SinchClient_ if you only want to receive calls.

> 📘
>
> It is safe to close application right after receiving _tokenRegistered()_ callback - you'll keep receiving incoming calls unless you _force stop_ the application or unregister the push token using _UserController_.

### Push Token Unregistration via _UserController_ API

When you want to _logout_ and stop receiving incoming calls via push, unregister the push token using _UserController_:

```java
    UserController userController = getUserController(<user id>);
    userController.unregisterPushToken();
```
