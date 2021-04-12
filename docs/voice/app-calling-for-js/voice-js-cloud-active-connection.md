---
title: Active Connection
excerpt: ''
hidden: false
next:
  pages:
    - voice-android-cloud-user-controller
---

If push notifications are not desired, the alternative is to use _active connection_. Invoke [SinchClient.startListeningOnActiveConnection()](reference/com/sinch/android/rtc/SinchClient.html).

> ❗️
>
> Listening on the active connection in a background service is not guaranteed started from Android 9. Android OS can stop background services at any moment. Use [managed push functionality](doc:voice-android-cloud-push-notifications)) to receive incoming calls from the background or when the application is stopped (but not _force stopped_).
