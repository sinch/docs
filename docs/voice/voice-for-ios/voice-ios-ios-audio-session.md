---
title: iOS Audio Session
excerpt: >-
  Details on how the Sinch SDK make use of iOS _Audio Session_.
hidden: false
next:
  pages:
    - voice-ios-cloud-miscellaneous
---

If the application plays audio that does not originate from the Sinch SDK, certain guidelines should be followed. Additional information on _Audio Session_-related topics is available in the [Audio Session Programming Guide](https://developer.apple.com/library/archive/documentation/Audio/Conceptual/AudioSessionProgrammingGuide/Introduction/Introduction.html).

## Audio Session

When a call is established, the Sinch client activates the shared audio session ([AVAudioSession](https://developer.apple.com/library/archive/documentation/Audio/Conceptual/AudioSessionProgrammingGuide/Introduction/Introduction.html)). When the call is disconnected, the Sinch client deactivates the shared audio session. If the application wants to play audio in any other context, it needs to reactivate the shared audio session. This applies after each finished call.

## Audio Session Categories

When a call is established the audio session category will be set to `PlayAndRecord`. 

The Sinch SDK applies the audio session category mode [`AVAudioSessionModeVoiceChat`](https://developer.apple.com/documentation/avfoundation/avaudiosessionmodevoicechat?language=objc) for improved voice quality.

## Audio Session Interruptions

When a user is a  Sinch SDK call, someone might call users using the PSTN network, thus interrupting the application and make iOS play the regular native ringtone. If the native phone call ends within 30 seconds, the application will start running again. If not, the Sinch SDK call will be terminated.

## SINAudioController

Applications that prefer to not use `SINAudioController` for playing sounds like progress tone are free to do so, but they should follow the guidelines for [Audio Sessions](#audio-session) and [Audio Session Categories](#audio-session-categories) above.
