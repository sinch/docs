---
title: Audio handling
excerpt: >-
  The details on how the Sinch Voice /w Video SDK manages audio during calls.
  Read more.
next:
  pages:
    - video-ios-application-authentication
---
If the application plays audio that does not originate from the Sinch SDK, certain guidelines should be followed. Additional information on audio session-related topics is available in the [Audio Session Programming Guide](https://developer.apple.com/library/archive/documentation/Audio/Conceptual/AudioSessionProgrammingGuide/Introduction/Introduction.html).

## Audio sessions
When a call is established, the Sinch client activates the shared audio session. When the call is disconnected, the Sinch client deactivates the shared audio session. If the application wants to play audio in any other context, it needs to reactivate the shared audio session. This applies after each finished call.

When started, the Sinch SDK client sets itself as the audio session delegate. If the application wishes to override this behaviour, the application needs to reset the delegate after the client has started but before any call has been started. (Doing this is not recommended.)

## Audio session categories

When the Sinch client is started, it sets the audio session category to `PlayAndRecord`. The reason the Sinch client only sets the audio category once, is to avoid interfering with what the hosting application may want to do with the audio session.

If the application changes the audio session category, it is responsible for changing the category back to `PlayAndRecord` after the application has performed its audio task so that the category is correctly setup for calls.

The Sinch SDK applies the audio session category mode `AVAudioSessionModeVoiceChat` for improved voice quality. Please see [Apple’s AVAudioSession documentation](https://developer.apple.com/documentation/avfoundation/avaudiosession) for further details.

## Audio session interruptions

When the users are in the midst of a Sinch SDK call, someone might call users using the PSTN network, thus interrupting the application and make iOS play the regular native ringtone. If the native phone call ends within 30 seconds, the application will start running again. If not, the Sinch SDK call will be terminated.

## Playing ringtones

The `SINAudioController` object provides a convenience method `startPlayingSoundFile:loop:` for playing sounds that are related to a call, such as ringtones and busy tones. Details on how to use it can be found in the [Reference](reference\html\Protocols\SINAudioController.html) documentation.

The sound file must be a mono (1 channel), 16-bit, uncompressed (PCM) `.wav` file with a sample rate of 8kHz, 16kHz, or 32kHz.
```objectivec
- (void)callReceivedOnRemoteEnd:(id<SINCall>)call {
    NSString* soundFilePath = [[NSBundle mainBundle] pathForResource:@"progresstone" ofType:@"wav"];
    // get audio controller from SINClient
    id<SINAudioController> audioController = [self.client audioController];
    [audioController startPlayingSoundFile:soundFilePath loop:NO];
}
```


Applications that prefer to use their own code for playing sounds are free to do so, but they should follow the guidelines for [Audio Sessions](#audio-sessions) and [Audio Session Categories](#audio-session-categories) above.
