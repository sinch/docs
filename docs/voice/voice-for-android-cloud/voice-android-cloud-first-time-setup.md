---
title: First Time Setup
excerpt: >-
  Follow this step-by-step guide to set up the Sinch Voice and Video SDK for the
  first time.
hidden: false
next:
  pages:
    - voice-android-cloud-sinch-client
---

## Register an Application

1.  Register a Sinch Developer account [here](https://portal.sinch.com/#/signup).
2.  Setup a new Application using the Dashboard where you can then obtain an _Application Key_ and an _Application Secret_.

## Download - We are now in Closed Beta Phase!

The Sinch Closed BETA SDKs can only be obtained after direct contact with Sinch Voice & Video Team.  
Contact [Sinch support](mailto:support@sinch.com). Please add the phrase _"CLOSED BETA REQUEST"_ on your email subject.

<!-- ## Download

The Sinch SDK can be downloaded [here](https://sinch.readme.io/page/downloads). It contains: the library _aar_, this user guide, reference documentation, and sample apps for calling. -->

## Add the Sinch library

The Sinch SDK library is distributed in [AAR](http://tools.android.com/tech-docs/new-build-system/aar-format) format. To use it in your project either:

> - Copy the **.aar** file to the `libs` folder and edit the build.gradle file to include

```text
repositories {
    flatDir {
        dirs 'libs'
    }
}

dependencies {
    implementation(name:'sinch-android-rtc', version:'+', ext:'aar')
}
```

> - Or using Android Studio choose `File -> New -> New Module -> Import .JAR/.AAR Package` option

## Running ProGuard

If you are using ProGuard, we bundle an example proguard-project.txt file that makes sure that the Sinch SDK will work as expected.

## Permissions

A minimum set of permissions are needed for the app to use the Sinch SDK. These are specified in the `AndroidManifest.xml` file.

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.RECORD_AUDIO" />
<uses-permission android:name="android.permission.MODIFY_AUDIO_SETTINGS" />
<uses-permission android:name="android.permission.READ_PHONE_STATE" />
```

> 📘
>
> By default, the Sinch SDK hangs up any Sinch call if the regular phone app has an active call. This functionality requires the permission READ_PHONE_STATE. However, if this default functionality isn’t wanted, turn it off by calling [SinchClient.getCallClient().setRespectNativeCalls(false)](reference/com/sinch/android/rtc/calling/CallClient.html) and the permission READ_PHONE_STATE is not needed.

> 📘
>
> If you intend to use `Automatic Audio Routing` (see the appropriate section in the [Voice Calling](doc:voice-android-cloud-calling)), you have to provide  `android.Manifest.permission.BLUETOOTH` in the manifest and manually check that this permission is granted by the user.

## Verify Manifest in Runtime

To verify that the manifest has the necessary permissions the [SinchClient.checkManifest()](reference/com/sinch/android/rtc/SinchClient.html)` can also be used to check whether required permissions are granted by the user in runtime. An example of how to dynamically request missing permissions can be found in the sample applications that comes with the SDK.

_Note:_ This method takes into consideration the capabilities which were enabled for the app (for example, calling, respecting native calls, and so on). Call `SinchClient.checkManifest()` after the setup but before the start of the SinchClient.
