---
title: First Time Setup
excerpt: >-
  Follow this step-by-step guide to set up the Sinch Voice and Video SDK for the
  first time.
hidden: false
next:
  pages:
    - voice-ios-cloud-sinch-client
---

## Register an Application

> 1.  Register a Sinch Developer account [here](https://portal.sinch.com/#/signup).
> 2.  Setup a new Application using the Dashboard where you can then obtain an _Application Key_ and an _Application Secret_.

## Download - We are now in Closed Beta Phase!

The Sinch Closed BETA SDKs can only be obtained after direct contact with Sinch Voice & Video Team.  
Contact [Sinch support](mailto:support@sinch.com). Please add the phrase _"CLOSED BETA REQUEST"_ on your email subject.

Both _ObjC_ and _Swift_ SDKs are accessible as a part of closed beta program. Once you signup as a Beta customer, you will have access to both versions of our IOS SDKs.

Both SDK's comes with sample apps. Read more about them [here](doc:voice-ios-cloud/voice-ios-cloud-samples)

### Sinch SDK as a CocoaPod

> 📘
>
> CocoaPods version will be available only for Production releases (i.e. not closed beta releases)

## Add the _Sinch.xcframework_ (ObjC version)

Drag the _Sinch.xcframework_ bundle from the SDK distribution package folder into the Frameworks section in the Xcode Project Navigator.

## Add the _SinchRTC.xcframework_ (Swift version)

Drag the _SinchRTC.xcframework_ bundle from the SDK distribution package folder into the Frameworks section in the Xcode Project Navigator.

Both versions of Sinch SDKs depends on the following frameworks which must be linked with the application target:

> _libc++.dylib_ (_libc++.tbd_), _libz.tbd_, _Security.framework_, _AVFoundation.framework_, _AudioToolbox.framework_, _VideoToolbox.framework_, _CoreMedia.framework_, _CoreVideo.framework_, _CoreImage.framework_, _GLKit.framework_, _OpenGLES.framework_, _QuartzCore.framework_, _Metal.framework_

## Info.plist

For voice calling functionality, add the following to your _Info.plist_:

- _Required background modes_ (`UIBackgroundModes`):

  - App plays audio (or streams audio/video using AirPlay) (`audio`)
  - App provides Voice over IP services (`voip`)

- _Privacy - Microphone Usage Description_ (`NSMicrophoneUsageDescription`):

  [NSMicrophoneUsageDescription](https://developer.apple.com/documentation/bundleresources/information_property_list/nsmicrophoneusagedescription) describes the reason your app accesses the microphone. When the system prompts the user to allow access, this string is displayed as part of the alert, and it _cannot_ be left empty.

In addition to the keys above, if video calling functionality will be enabled and used, add the following to your _Info.plist_:

- _Privacy - Camera Usage Description_ (`NSCameraUsageDescription`):

  [NSCameraUsageDescription](https://developer.apple.com/documentation/bundleresources/information_property_list/nscamerausagedescription) describes the reason that your app accesses the camera. When the system prompts the user to allow access, this string is displayed as part of the alert, and it _cannot_ be left empty.
