---
title: Sinch Voice iOS SDK
excerpt: >-
  See how the Sinch IOS SDK is evolving and find out about new features and bug
  fixes.
---

<h3>2020/09/23 | SINCH SDK IOS 4.2.6</h3>

- Use CallKit for outbound calls in sample application SinchCallKit.

<h3>2020/05/15 | SINCH SDK IOS 4.2.5</h3>

- Fix ObjectiveC symbol clashes between internal and system
  WebRTC libraries.

- Fix crash in iOS 13 caused by method
  endGeneratingDeviceOrientationNotifications in WebRTC.

<h3>2020/04/15 | SINCH SDK IOS 4.2.4</h3>

- Fix potential crash in -[SINVideoView initWithFrame:name:]

<h3>2020/03/15 | SINCH SDK IOS 4.2.3</h3>

- Fix crash on iOS 10 caused by method -[PKPushRegistryDelegate pushRegistry: didReceiveIncomingPushWithPayload:forType:] not being implemented.

<h3>2020/03/05 | SINCH SDK IOS 4.2.2</h3>

- Fix for multiple Callee scenarios that are dependent on media proxy relay.

<h3>2020/02/15 | SINCH SDK IOS 4.2.1</h3>

- Rebuilt to resolve issues using Deployment Target iOS 10.0.

<h3>2020/02/10 | SINCH SDK IOS 4.2.0</h3>

- Re-add support for legacy push data: -[SINCallDelegate call:shouldSendPushNotifications:] -[SINClient registerPushNotificationData:] -[SINClient unregisterPushNotificationData]

> **Note** These APIs will be removed in the next major version of the Sinch RTC SDK (5.x). Please migrate to use the "Sinch Managed Push Notifications" functionality.

<h3>2019/11/12 | SINCH SDK IOS 4.1.0</h3>

- iOS 13 VoIP push and CallKit compatibility. When linking against
  the iOS 13 SDK or later, your application must report VoIP push
  notifications as an incoming call to
  CallKit. -[SINManagedPushDelegate
  managedPush:didReceiveIncomingPushWithPayload:forType:] will now
  be invoked synchronously on Sinch-internal GCD queue as assigned
  to PKPushRegistry so that an application can report to CallKit
  within the same runloop as mandated by the iOS 13 changes.

- Fix video functionality on iOS 13 (H.264 codec, video rotation)

- Removed support for local notifications
  (UILocalNotification). Since CallKit is effectively mandatory to
  use with PushKit, using local notifications in conjunction with
  VoIP push is no longer applicable since iOS SDK 13. An application
  using the Sinch SDK should use PushKit and CallKit going forward.

- Removed support for canceled call notifications (also known as
  "missed call" notifications). iOS 13 requirements on how a (new)
  call must be reported for each received VoIP push notification has
  severe implications on the previous support for canceled call
  notifications, so severe that it is no longer possible to support.
  (-[SINCallNotificationResult isCallCanceled] is no longer available)

- Removed support for -[SINCallDetails
  applicationStateWhenReceived]. This is no longer supported due to
  that a -[UIApplication applicationState] can only safely be used
  on main UI thread (Main Thread Checker would catch this as
  incorrect usage under certain circumstances).

- IMPORTANT: A VoIP push payload MUST be relayed to -[SINManagedPush
  didCompleteProcessingPushPayload:] unless it is relayed to an
  instance of SINClient. Note that the sample app SinchCallKit
  implements it so that it always invokes this method, which is also
  OK.

- Remove logging callback from SINClientDelegate.
  Logging callback should now be set via [Sinch setLogCallback:].
  Logging callback will be invoked on a background GCD queue (assume
  that it's invoked on a concurrent GCD queue).

- Removed -[SINManagedPush registerUserNotificationSettings] and -[SINManagedPush setUserNotificationTypes:] (these APIs were tied
  to iOS APIs deprecated since iOS 10).
  Managing authorization for remote (non-VoIP) or local
  notifications is now outside the scope of the Sinch APIs and an
  application should use UserNotifications.framework directly.

- iOS Deployment Target raised to iOS 10.0.

- Removed support for Instant Messaging.

<h3>2019/08/22 | SINCH SDK IOS 3.12.10</h3>

- Assign non-main GCD queue for PKPushRegistry
- Ensure SINManagedPush is safe (thread-safe) to invoke from non-main thread (i.e. from any background GCD queue).

<h3>2019/03/22 | SINCH SDK IOS 3.12.9</h3>

- Updated Sinch iOS SDK to handle missing audio and video on calls originated by Sinch JS SDK. Issue introduced by the latest Chrome and Firefox browsers update.

<h3>2019/02/30 | SINCH SDK IOS 3.12.8</h3>

- Improved audio session management, and push notifications for missed call.
- Improved sample Apps.

<h3>2018/12/03 | SINCH SDK IOS 3.12.7</h3>

- Included custom headers in call cancellation push notifications.

<h3>2018/09/05 | SINCH SDK IOS 3.12.6</h3>

- Fixed default orientation of local preview in landscape mode for video chat.
- Improved CallKit Sample App.

<h3>2018/05/23 | SINCH SDK IOS 3.12.5</h3>

- Packaged Sinch Framework as modular framework, made it easier to be integrated into Swift projects.
- Improved Sample Apps.

<h3>2018/03/08 | SINCH SDK IOS 3.12.4</h3>

- Added support for cancellation of a call via VoIP push notifications.
- Added API to set and retrieve custom headers from Sinch managed push notifications.
- Added NSNotification for the state change of a call.
- Added API to set data protection type of Sinch created files.
- Improved Sample Apps.

<h3>2017/12/20 | SINCH SDK IOS 3.12.3</h3>

- Improved sample apps

<h3>2017/11/22 | SINCH SDK IOS 3.12.2</h3>

- Improved Wifi/Cellular handover for App calling

<h3>2017/10/27 | SINCH SDK IOS 3.12.1</h3>

- Minor bug fixes with regard to CallKit support

<h3>2017/10/20 | SINCH SDK IOS 3.12.0</h3>

- Added support for CallKit integration (voice call only).
- Added support for accessing the raw video frame of local video stream: developers can now for example apply filter on local video stream and send it to remote, or save screenshots of the local video stream.
- Fixed IPv6 compatibility issue.
- Minor fixes in Sample Apps.

<h3>2017/03/10 | SINCH SDK IOS 3.11.0</h3>

- Video Calling General Availability Release:
  - Added support for pausing and resuming video stream.
  - Added support for accessing the raw video frame of remote video stream.
- Resolved potential error \`duplicate symbol
  - OBJC_CLASS\$\_GTMStringEncoding\` when using the Sinch SDK together with other third-party libraries that make use of GTMStringEncoding.

<h3>2017/01/30 | SINCH SDK IOS 3.10.1</h3>

- Bug fixes

<h3>2017/01/24 | SINCH SDK IOS 3.10.0</h3>

- General improvement

<h3>2016/11/14 | SINCH SDK IOS 3.9.8</h3>

- IPv6 compatibility.
- General improvement and minor bug fixes.

<h3>2016/09/30 | SINCH SDK IOS 3.9.7</h3>

- Video calling bug fixes.

<h3>2016/08/04 | Sinch SDK iOS 3.9.6</h3>

- iOS 10 compatibility.
- Deprecated -[SINClient setSupportActiveConnectionInBackground:] for iOS 10. See the new section ‘Deprecated features and APIs’ in User Guide for details on this deprecation.

<h3>2016/08/04 | Sinch SDK iOS 3.9.5</h3>

- Fix -[SINMessage timestamp] to return server-side timestamp for a id passed to -[SINMessageClientDelegate messageSent:recipientId:]
- Dropped support for i386 iOS Simulator. Please use x86_64 iOS Simulator.

<h3>2016/05/11 | Sinch SDK iOS 3.9.4</h3>

- Upgrade BoringSSL to revision c880e42ba1c8032d4cdde2aba0541d8a9d9fa2e9. This revision includes fixes for described in <https://www.openssl.org/news/secadv/20160503.txt>

<h3>2016/03/09 | Sinch SDK iOS 3.9.3</h3>

- Fix linker issues when using the SDK via CocoaPods.

<h3>2016/03/04 | Sinch SDK iOS 3.9.2</h3>

- Support for LLVM bitcode.

<h3>2016/01/27 | Sinch SDK iOS 3.9.1</h3>

- SIP errors reporting.

<h3>2015/09/25 | Sinch SDK iOS 3.7.1</h3>

- Removed armv7s slice in framework/library binary
- Fixed the macro SINAPSEnvironmentAutomatic to consider both DEBUG and NDEBUG. This should make it work with Xcode projects that are created with the default build setting DEBUG=1 defined for the Debug build configuration.
- Updated to comply with iOS 9 App Transport Security defaults. See the following link for details: <https://developer.apple.com/library/prerelease/ios/technotes/App-Transport-Security-Technote/>
- Sample app updates for Xcode 7 and iOS 9.
- Introduced Sinch conference calling.

<h3>2015/07/08 | Sinch SDK iOS 3.5.2</h3>

- Adaptive bitrate support for OPUS codec in app-to-app calls.

<h3>2015/06/01 | Sinch SDK iOS 3.5.1</h3>

- Use UIApplication background task when requesting push device token to increase robustness of aquiring device token when application is launched into background mode (e.g. after device reboot, or after a crash). Note that is only used when using VoIP push notifications (PushKit). (The background task is started upon -[SINManagedPush setDesiredPushTypeAutomatically] or -[SINManagedPush setDesiredPushType:], and it’s ended upon receiving a device token from PKPushRegistry).

<h3>2015/04/10 | Sinch SDK iOS 3.5.0</h3>

- New fully integrated support for remote push notifications:

  - Push notifications sent by the Sinch cloud platform (provided that you upload Apple Push Certificates to the Sinch Dashboard).
  - Support for both regular remote push notifications and VoIP-push notifications (available since iOS 8).
  - New protocol SINManagedPush used as entry point for managing push notifications.
  - New push notification methods on SINClient: -[SINClient enableManagedPushNotifications:] -[SINClient relayRemotePushNotification:] -[SINClient registerPushNotificationDeviceToken:type:apsEnvironment:]
  - New sample apps that shows how to use the new push notification functionality.
  - New NSNotifications available (as a complement to SINClientDelegate): SINClientDidStartNotification SINClientDidFailNotification SINClientWillTerminateNotification
  - relayRemotePushNotification and relayRemotePushNotificationPayload will now correctly start the client when the payload is for an IM.

<h3>2015/02/23 | Sinch SDK iOS 3.4.2</h3>

- Fix so that SINClient does not throw exception if -[UIApplication setKeepAliveTimeout:handler] returns NO. SINClient will now instead emit a log message with severity SINLogSeverityCritical.

<h3>2015/02/09 | Sinch SDK iOS 3.4.1</h3>

- Support for x86_64 architecture (iOS 64-bit Simulator)

<h3>2015/01/29 | Sinch SDK iOS 3.4.0</h3>

- Support for arm64 architecture
- Support for OPUS codec for app-to-app calls

<h3>2014/12/05 | Sinch SDK iOS 3.3.2</h3>

- Added support for -[UILocalNotification category] (iOS 8)

<h3>2014/12/03 | Sinch SDK iOS 3.3.1</h3>

- Improved internal call reporting.

<h3>2014/11/21 | Sinch SDK iOS 3.3.0</h3>

- Added support for call headers for app-to-app calls. See -[SINCall headers] and -[SINCallClient callUserWithId:headers:]

<h3>2014/11/13 | Sinch SDK iOS 3.2.9</h3>

- Fixed a regression in reporting SINCallEndCauseNoAnswer even if the call has been cancelled by the caller. It will now report the expected SINCallEndCauseCanceled in this case.

<h3>2014/10/27 | Sinch SDK iOS 3.2.8</h3>

- Improvements to scenario where caller hangs up a call before certain internal REST API requests has completed, but the requests completes later during the graceful termination period.

<h3>2014/10/24 | Sinch SDK iOS 3.2.7</h3>

- Fixed issue with user notification permission on iOS 8.
- Added new method -[SINClient terminateGracefully]

<h3>2014/10/17 | Sinch SDK iOS 3.2.6</h3>

- Fixed issue causing long call setup time when using certain codecs.

<h3>2014/10/10 | Sinch SDK iOS 3.2.5</h3>

- Additional improvements for scenarios in which the caller cancels a call before the call is answered.

<h3>2014/10/09 | Sinch SDK iOS 3.2.4</h3>

- Improvements of disposal of Sinch SDK internals if a consumer of the Sinch SDK would accidently keep reference counts on a SINMessageClient even after a -[SINClient terminate] has been called.

> **IMPORTANT:** Do note that is not in any way recommended to keep references to e.g. a SINMessageClient or a SINCallClient after the parent SINClient has been terminated.\_

- Fixed issue if caller hung up call quickly and before the callee answer, the callee would not receive the event that the call actually ended.

<h3>2014/10/03 | Sinch SDK iOS 3.2.3</h3>

- Improvements in handling unstable network connection when initiating a call.
- Fixed order of instant messages received as history.

<h3>2014/09/19 | Sinch SDK iOS 3.2.1</h3>

- Improvements in handling unstable network connection when initiating a call.

<h3>2014/09/04 | Sinch SDK iOS 3.2.0</h3>

- Added persistence mechanism. Instant messages are now persisted internally in the SDK and retried automatically for 12 hours before failing.

<h3>2014/08/05 | Sinch SDK iOS 3.1.5</h3>

- Bugfix: Incoming calls will from now on never have state SINCallStateProgressing. Previously, incoming calls could either be in SINCallStateInitiating or SINCallStateProgressing just after -[SINCallClientDelgate client:didReceiveIncomingCall:]. Now, the call will initially be in SINCallStateInitiating, until transitioning to SINCallStateEstablished or SINCallStateEnded.
- Fixed crash when attempting to send DTMF during call setup.
- Internal improvements for Call headers.

<h3>2014/07/11 | Sinch SDK iOS 3.1.3</h3>

- Fixed crash that could occur under bad network conditions
- Internal improvements: Reducing number of signaling messages sent when initiating a voice call.
- Improvements for Swift compatibility: use NS_ENUM when declaring enums, e.g. SINCallState, SINCallEndCause etc.

<h3>2014/06/12 | Sinch SDK iOS 3.1</h3>

- [SINClient stop] has been deprecated and replaced by -[SINClient terminate]. As part of this change, the SINClient is invalidated after -[SINClient terminate] has been called and cannot be restarted.
- Minor bug fixes and improvements

<h3>2014/05/14 | Sinch SDK iOS 3.0</h3>

Public release of the iOS SDK with:

- Improved sample applications and documentation
- Refactored calling and messaging API

<h3>2014/05/02 | Sinch SDK iOS 3.0 BETA</h3>

First public beta version of the Sinch SDK for iOS with support for:

- App to app calling
- App to phone calling
- Instant messaging to single and multiple recipient
