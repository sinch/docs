---
title: Video Calling
excerpt: >-
  Set up video calls with the Sinch iOS Voice & Video SDK.
hidden: false
next:
  pages:
    - voice-ios-push-notifications-callkit
---

## Setting Up a Video Call

Just like audio calls, video calls are placed through the `SINCallClient` and events are received using the `SINCallClientDelegate` and `SINCallDelegate`. For a more general introduction to calling and `SINCallClient`, see [Calling](doc:voice-ios-calling). 

Before you start, ensure your application is [requesting user permission for using the video camera](doc:voice-ios-video-calling#request-user-permission-for-using-the-camera).

## Showing the Video Streams

The following examples for showing video streams will be based on the assumption of a view controller having the following properties:

```objectivec
@interface MyViewController : UIViewController

@property (weak, nonatomic) IBOutlet UIView *remoteVideoView;
@property (weak, nonatomic) IBOutlet UIView *localVideoView;

@end
```

### Showing a Preview of the Local Video Stream

The locally captured stream is rendered into the view provided by [`-[SINVideoController localView]`](reference\html\Protocols\SINVideoController.html) when it is attached to the application UI view hierarchy.

```objectivec
- (void)viewDidLoad {
  [super viewDidLoad];

  id<SINVideoController> videoController = ... // get video controller from SINClient.

  [self.localVideoView addSubview:[videoController localView]];
}
```

### Showing Remote Video Streams

Once the remote video stream is available, the delegate method `-[SINCall callDidAddVideoTrack:]` will be called and you can use that to attach the Sinch video controller view ([`-[SINVideoController remoteView]`](reference\html\Protocols\SINVideoController.html)) to your application view hierarchy so that the stream is rendered.


```objectivec
- (void)callDidAddVideoTrack:(id<SINCall>)call {
  id<SINVideoController> videoController = ... // get video controller from SINClient.

  // Add the video views to your view hierarchy
  [self.remoteVideoView addSubview:[videoController remoteView]];
}
```

(The remote stream will automatically be rendered into the view provided by `-[SINVideoController remoteView]`.)

### Pausing and Resuming a Video Stream

To pause the local video stream, use the method `-[SINCall pauseVideo]`. To resume the local video stream, use the method `-[SINCall resumeVideo]`.

```objectivec
// Pause the video stream.
[call pauseVideo];

// Resume the video stream.
[call resumeVideo];
```

The call delegate will be notified of pause- and resume events via the delegate callback methods `-[SINCallDelegate callDidPauseVideoTrack:]` and `-[SINCallDelegate callDidResumeVideoTrack:]`. It is for example appropriate to based on these events update the UI with a pause indicator, and subsequently remove such pause indicator.

### Video Content Fitting and Aspect Ratio

How the rendered video stream is fitted into a view can be controlled by the regular property `UIView.contentMode`. I.e. assigning `contentMode` on a view returned by `-[SINVideoController remoteView]` or `-[SINVideoController localView]` will affect how the video content is laid out. Note though that only `UIViewContentModeScaleAspectFit` and `UIViewContentModeScaleAspectFill` will be respected.

**Example**

```objectivec
id<SINVideoController> videoController = ... // get video controller from SINClient.

[videoController remoteView].contentMode = UIViewContentModeScaleAspectFill;
```

### Full Screen Mode

The Sinch SDK provides helper functions to transition a video view into fullscreen mode. These are provided as Objective-C category methods for the `UIView` class and are defined in `SINUIView+Fullscreen.h` (`SINUIViewFullscreenAdditions`).

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

### Camera Selection (Front/Back)

Select the front or back camera using `-[SINVideoController captureDevicePosition:]`.

__Example__

```objectivec
- (IBAction)onUserSelectedBackCamera:(id)sender {
  id<SINVideoController> videoController = ... // get video controller from SINClient.

  videoController.captureDevicePosition = AVCaptureDevicePositionBack;
}

- (IBAction)onUserSelectedFrontCamera:(id)sender {
  id<SINVideoController> videoController = ... // get video controller from SINClient.

  videoController.captureDevicePosition = AVCaptureDevicePositionFront;
}
```

### Accessing Raw Video Frames from Remote Streams

The Sinch SDK provides access to the raw video frames of the remote video streams. This allows you to process the video frames with your own implementation to achieve rich functionalities, e.g. applying filters, adding stickers to the video frames, or saving a frame as an image.

Perform custom video frame processing by implementing `SINRemoteVideoFrameCallback` / `SINLocalVideoFrameCallback` and register it using `-[SINVideoController setRemoteVideoFrameCallback:]` and `-[SINVideoController setLocalVideoFrameCallback:]`. The callback handler will provide the frame in the form of a [`CVPixelBufferRef`](https://developer.apple.com/documentation/corevideo/cvpixelbuffer?language=objc), and a completion handler block that you __must__ invoke, passing the processed output frame (also as a `CVPixelBufferRef`) as argument. The implementation of the frame callback hander __must__ retain (and release) the buffer using [CVPixelBufferRetain](https://developer.apple.com/documentation/corevideo/1563590-cvpixelbufferretain?language=objc) and [CVPixelBufferRelease](https://developer.apple.com/documentation/corevideo/1563589-cvpixelbufferrelease?language=objc).

**Example**

```objectivec
// Implementation of -[SINVideoFrameCallback onFrame:completionHandler:]
- (void)onFrame:(CVPixelBufferRef)inputPixelBuffer
    completionHandler:(void (^)(CVPixelBufferRef))completionHandler {
  CVPixelBufferRetain(inputPixelBuffer);
  
  // Use GCD dispatch to process frame async.
  dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_HIGH, 0), ^{
    CVPixelBufferRef outputPixelBuffer = ... // Perform actual processing.
    completionHandler(outputPixelBuffer);
    CVPixelBufferRelease(outputPixelBuffer); // Release your intermediate processing output buffer.
    CVPixelBufferRelease(inputPixelBuffer); // Release the original input frame buffer.
  });
}
```

__NOTE__: It is recommended to perform frame processing asynchronously using [GCD](https://developer.apple.com/documentation/dispatch?language=objc), using a dedicated queue and tune the queue priority to your use case. If you are processing each and every frame (e.g. applying a filter), it is recommended to use a high priority queue. If you are only processing some frames, e.g. saving snapshot frames based on user action, then it may be more appropriate to use a low priority background queue.

### Converting a Video Frame to `UIImage`

The Sinch SDK provides the helper function `SINUIImageFromPixelBuffer(CVPixelBufferRef)` to convert `CVPixelBufferRef` to `UIImage*`.

```objectivec
#import "SINVideoController.h" // To use SINUIImageFromVideoFrame()

id<SINVideoFrame> videoFrame = ... // Get SINVideoFrame from onFrame: callback
UIImage *image = SINUIImageFromVideoFrame(videoFrame);
```

__IMPORTANT__: The helper function will __not__ release the frame buffer (i.e. you must still call `CVPixelBufferRelease` after using this helper function.)


## Request User Permission for Using the Camera

Recording video always requires explicit permission from the user. Your app must provide a description for its use of video camera in terms of the _Info.plist_ key [NSCameraUsageDescription](https://developer.apple.com/documentation/bundleresources/information_property_list/nscamerausagedescription).

See the Apple documentation on [`+[AVCaptureDevice requestAccessForMediaType:completionHandler:]`](https://developer.apple.com/documentation/avfoundation/avcapturedevice/1624584-requestaccessformediatype) for details on how to request user permission.
