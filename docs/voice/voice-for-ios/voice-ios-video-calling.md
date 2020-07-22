---
title: Video Calling
excerpt: >-
  Set up video calls with the Sinch iOS Voice and Video SDK. Get more
  information here.
next:
  pages:
    - voice-ios-push-notifications-callkit
---
## Setting up a video call

Just like audio calls, video calls are placed through the `SINCallClient` and events are received using the `SINCallClientDelegate`. The call client is owned by the SinchClient and accessed using `[sinchClient callClient]`. Calling is not enabled by default.
For a more general introduction to calling with the SinchClient, see [Calling](doc:voice-ios-calling).

## Showing the video streams

The following examples for showing video streams will be based on the assumption of a view controller having the following properties:
```objectivec
@interface MyViewController : UIViewController

@property (weak, nonatomic) IBOutlet UIView *remoteVideoView;
@property (weak, nonatomic) IBOutlet UIView *localVideoView;

@end
```


### Showing a preview of the local video stream
```objectivec
- (void)viewDidLoad {
  [super viewDidLoad];

  id<SINVideoController> videoController = ... // get video controller from SINClient.

  [self.localVideoView addSubview:[self.videoController localView]];
}
```


### Showing remote video streams

Once you have created a `SINCallClientDelegate` and added it to a call, the method `callDidAddVideoTrack:` will be called.
```objectivec
- (void)callDidAddVideoTrack:(id<SINCall>)call {
  id<SINVideoController> videoController = ... // get video controller from SINClient.

  // Add the video views to your view hierarchy
  [self.remoteVideoView addSubview:[videoController remoteView]];
}
```


### Pausing video stream

To pause the local video stream, use the `pauseVideo` method on the call.
```objectivec
// Pause the video stream.
[call pauseVideo];
```


### Resuming video stream
To resume the local video stream, use the `resumeVideo` method on the call.
```objectivec
// Resume the video stream.
[call resumeVideo];
```


### Pausing video stream delegates

Once you have created a `SINCallClientDelegate` and added it to a call, the method `callDidPauseVideoTrack:` will be called when the remote user pause the video stream.
```objectivec
- (void)callDidPauseVideoTrack:(id<SINCall>)call {
  // Implement what to be done when remote user pause video stream.
```


### Resuming video stream delegates

Once you have created a `SINCallClientDelegate` and added it to a call, the method `callDidResumeVideoTrack:` will be called when the remote user resumes the video stream.
```objectivec
- (void)callDidResumeVideoTrack:(id<SINCall>)call {
  // Implement what to be done when remote user resumes video stream.
```


### Video content fitting and aspect ratio

How the rendered video stream is fitted into a view can be controlled by the regular `-[UIView contentMode]` property. I.e. assigning `contentMode` on a view returned by `-[SINVideoController remoteView]` or `-[SINVideoController localView]` will affect how the video content is laid out. Note though that only `UIViewContentModeScaleAspectFit` and `UIViewContentModeScaleAspectFill` will be respected.

**Example**
```objectivec
id<SINVideoController> videoController;
videocontroller.remoteView.contentMode = UIViewContentModeScaleAspectFill;
```


### Full screen

The Sinch SDK provides helper functions to transition a video view into fullscreen mode. These are provided as Objective-C category methods for the `UIView` class and are defined in `SINUIView+Fullscreen.h` (*SINUIViewFullscreenAdditions*).

**Example**
```objectivec
- (IBAction)toggleFullscreen:(id)sender {
    id<SINVideoController> videoController = ... // get video controller from SINClient.

    UIView *view = videoController.remoteView;

    if ([view sin_isFullscreen]) {
      view.contentMode = UIViewContentModeScaleAspectFit;
      [view sin_disableFullscreen:YES]; // Pass YES to animate the transition
    } else {
      view.contentMode = UIViewContentModeScaleAspectFill;
      [view sin_enableFullscreen:YES];  // Pass YES to animate the transition
    }
  }
```


### Accessing video frames of the remote streams

The Sinch SDK provides developers a callback to access the video frames of the remote streams. So you can process the video frames with your own implementation to achieve rich functionalities, e.g., applying filters, adding stickers to the video frames, or saving the video frame as an image.

Your video frame handler needs to conform `SINVideoFrameCallback` protocol by implementing the `onFrame:` callback. Note that you need to explicitly release the video frame by calling `[videoFrame releaseFrame]`.

**Example**
```objectivec
// YourVideoFrameHandler.h
// Conform SINVideoFrameCallback protocol
@interface YourVideoFrameHandler : NSObject<SINVideoFrameCallback>
    ... // Handler specific declarations
@end
```

``` sourceCode objectivec
// YourVideoFrameHandler.m
// Implement onFrame: callback
@implementation YourVideoFrameHandler
- (void)onFrame:(id<SINVideoFrame>)videoFrame callId:(NSString *)callId {
  ... // Process videoFrame
  [videoFrame releaseFrame]; // Release videoFrame
}
@end
```


Use `-[SINVideoController setVideoFrameCallback:]` to register your video frame handler as the callback to receive video frames.

```objectivec
YourVideoFrameHandler* videoFrameHandler;
id<SINVideoController> videoController = ... // Get video controller from SINClient.
[videoController setVideoFrameCallback: videoFrameHandler];
```


### Converting video frame to UIImage

The Sinch SDK provides helper functions to convert `id<SINVideoFrame>` to `UIImage*`. It is handy to get the `UIImage` representation of `SINVideoFrame` by calling function `SINUIImageFromVideoFrame()`. Note that the helper function will *NOT* release the video frame.
```objectivec
#import "SINVideoController.h" // To use SINUIImageFromVideoFrame()

id<SINVideoFrame> videoFrame = ... // Get SINVideoFrame from onFrame: callback
UIImage *image = SINUIImageFromVideoFrame(videoFrame);
```
