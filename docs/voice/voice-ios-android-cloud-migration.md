---
title: Sinch Voice and Video SDK Migration Guide
excerpt: >-
  Follow this migration guide to start using the latest Sinch mobile Voice and Video SDKs.
hidden: true
---

## Intended Audience

This migration guide provides an overview of adaptions required to migrate from the Sinch legacy platform to the Sinch cloud-native platform. We assume that you're familiar with key concepts, operations, and glossary of the Sinch Voice and Video SDK. Here we provide a comprehensive list of the _changes_ required both in the application _and_ on your backend to get started with the new cloud-based Sinch mobile SDKs.

Otherwise, if you had never used Sinch Voice and Video SDK before, please refer to the respective platform documentation:

- [Sinch Android SDK 4.x](doc:voice-for-android-cloud)
- [Sinch iOS SDK 5.x](doc:voice-ios-cloud)

## Client / User Registration Changes

When you initiate the Sinch _client_ you have to provide _user identity_ and also authorize the _client_ to register for the given _user identity_ by providing a registration token. In the legacy Sinch SDKs, user authentication and authorization were based on credentials provided in a way of an opaque token that was based on the _SHA1_ digest of Sinch _Application Secret_. There was also a way to provide _Application Secret_ directly to the _SinchClient_.

In the new SDK, to authorize the registration of a user, the application must provide a registration token to the client (`SINClient`/`SinchClient`) or `UserController`. This token should be in the form of a [JSON Web Token (JWT)](https://jwt.io/) signed with a signing key derived from the _Application Secret_. The recommended way to implement this authentication scheme is that the _Application Secret_ should be kept securely on your server-side backend, and the signed token should be created and signed on your server, then passed via a secure channel to the application and Sinch client running on a device.

Thus, registration is made both more simple and secure:

- _Application Secret_ must never be stored in the application, and there is no way to provide _Application Secret_ to the _SinchClient_ that could encourage that, instead:
- Authentication and Authorization is granted by JWT tokens.

Both [iOS](doc:voice-ios-cloud-auth) and [Android](doc:voice-android-cloud-application-authentication) documents provide the same coverage on how to create and sign these JWT registration token, including links to the server-side sample code.

> ❗️ Server-side changes required
> Adapting to the User registration and authorization changes will require changes to your server-side implementation.

## Android and UserController

There is a new way to register _User_ on Android: _UserController API_. [UserController](voice-for-android-cloud/reference/com/sinch/android/rtc/UserController.html) provides a way independently from the `SinchClient` lifecycle, to register a _User_ for incoming calls via push notifications. You can also use it to unregister push token if receiving of incoming calls is no longer desirable (e.g. on logout, or changing users).

_UserController_ provides two types of callbacks:

- Callbacks for basic _User_ registration with the Sinch backend.
- Callbacks for _push device token_ registration with the Sinch backend.

The action flow diagram of the _User_ registration via _UserController_ is provided below. _UserController_'s callbacks are highlighted in pale blue.

![Registering User via UserController](voice-for-android-cloud\images\20201006-usercontroller-callbacks.pu.png)

> ❗️
>
> Though the use of _UserController_ is **optional** for the FCM Push Notification case, since the _SinchClient_ can
> register itself to receive FCM Push, it is **mandatory** for the Huawei Push Notifications, since the _push device
> token_ have to be acquired by the _Application_ before constructing _UserController_.
> Thus, it is **highly recommended** to use _UserController_ in both cases.

## iOS APNs Signing Keys

New Sinch iOS SDK only support APNs Token-based authentication, i.e. we no longer support APNs certificate-based functionality. Therefore, to enable Sinch to act as APNs Provider on your behalf, you must provide Sinch with an APNs Authentication Token Signing Key. You create this signing key in your Apple Developer Account and upload the key file to your Sinch Developer Account. Please find more details [here](doc:voice-ios-cloud-push-notifications-callkit#configuring-an-apns-authentication-signing-key).
