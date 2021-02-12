---
title: Sinch Voice and Video SDK first time setup
excerpt: >-
  Follow this step-by-step guide to set up the Sinch Voice and Video SDK for the
  first time.
next:
  pages:
    - voice-ios-sinch-client
---
## Register an Application

> 1.  Register a Sinch Developer account [here](https://portal.sinch.com/#/signup).
> 2.  Setup a new Application using the [Dashboard](https://portal.sinch.com/#/dashboard) where you can then obtain an _Application Key_ and an _Application Secret_.

## Download

The Sinch SDK can be downloaded [here](https://sinch.readme.io/page/downloads). It contains: the library binary, this user guide, reference documentation, and sample apps for calling.

### Sinch as CocoaPod

If you are using [CocoaPods](http://www.cocoapods.org), add the following to your Podfile:

```ruby
target '<your xcode project>' do
	pod 'SinchRTC'
end
```

> 👍
>
> Using the pod allows you to skip the manual steps in the next section.

## Add the _Sinch.framework_

Drag the _Sinch.framework_ bundle from the SDK distribution package folder into the Frameworks section in the Xcode Project Navigator.

The Sinch SDK depends on the following frameworks which must be linked with the application target:

> _libc++.dylib_ (_libc++.tbd_), _libz.tbd_, _Security.framework_, _AVFoundation.framework_, _AudioToolbox.framework_, _VideoToolbox.framework_, _CoreMedia.framework_, _CoreVideo.framework_, _CoreImage.framework_, _GLKit.framework_, _OpenGLES.framework_, _QuartzCore.framework_

## Info.plist

If voice calling functionality will be enabled and used, add the following to your _Info.plist_:

- Required background modes (`UIBackgroundModes`):

  - Application plays audio (`audio`)
  - Application provides Voice over IP services (`voip`)

- Privacy - Microphone Usage Description (`NSMicrophoneUsageDescription`):

  [NSMicrophoneUsageDescription](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW25) describes the reason your app accesses the microphone. When the system prompts the user to allow access, this string is displayed as part of the alert, and it _cannot_ be left empty.

In addition to the keys above, if video calling functionality will be enabled and used, add the following to your _Info.plist_:

- Privacy - Camera Usage Description
  (`NSCameraUsageDescription`):

  [NSCameraUsageDescription](https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW24) describes the reason that your app accesses the camera. When the system prompts the user to allow access, this string is displayed as part of the alert, and it _cannot_ be left empty.
