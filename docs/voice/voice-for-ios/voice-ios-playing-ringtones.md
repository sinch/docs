---
title: Playing Ringtones
excerpt: >-
  Details on playing audio using `SINAudioController`.
hidden: false
next:
  pages:
    - voice-ios-cloud-ios-audio-session
---

## Playing Ringtones

`SINAudioController` provides functionality for playing sounds that are related to a call, such as ringtones and busy tones. The relevant methods are `startPlayingSoundFile:loop:` and `stopPlayingSoundFile`. 

The sound file must be a mono (1 channel), 16-bit, uncompressed (PCM) `.wav` file with a sample rate of 8kHz, 16kHz, or 32kHz.

Example of playing a file when the call has reached progress state:

```objectivec
- (void)callDidProgress:(id<SINCall>)call {
    NSString* soundFilePath = [[NSBundle mainBundle] pathForResource:@"my_progress_tone" ofType:@"wav"];
    id<SINAudioController> audioController = [self.sinchClient audioController];
    [audioController startPlayingSoundFile:soundFilePath loop:NO];
}
```

Stop the sound when the call is established and/or when the call ends.

```
- (void)callDidEstablish:(id<SINCall>)call {
  id<SINAudioController> audioController = [self.sinchClient audioController];
  [[self audioController] stopPlayingSoundFile];
}

- (void)callDidEnd:(id<SINCall>)call {
  id<SINAudioController> audioController = [self.sinchClient audioController];
  [[self audioController] stopPlayingSoundFile];
}
```

Further details on `SINAudioController` and how to use it can be found in the [Reference](reference\html\Protocols\SINAudioController.html) documentation.

Applications that prefer to use their own code for playing sounds are free to do so, but they should follow the guidelines for use of the shared iOS [Audio Session](doc:voice-ios-cloud-ios-audio-session)
