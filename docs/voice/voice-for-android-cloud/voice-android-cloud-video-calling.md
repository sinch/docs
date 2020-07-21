---
title: Video Calling
excerpt: >-
  Set up video calls with the Sinch Android Voice with Video SDK. Get more
  information here.
hidden: false
next:
  pages:
    - voice-android-cloud-push-notifications
---

## Setting Up a Video Call

Just like audio calls, video calls are placed through the `CallClient` and events are received using the `CallClientListener`. The call client is owned by the SinchClient and accessed using `sinchClient.getCallClient()`. 
For a more general introduction to calling with the SinchClient, see [here](doc:voice-android-cloud-calling).

## Showing the Video Streams

Once you have created a `VideoCallListener` and added it to a call, the `onVideoTrackAdded()` method will be called.

```java
@Override
public void onVideoTrackAdded(Call call) {
    // Get a reference to your SinchClient, in the samples this is done through the service interface:
    VideoController vc = getSinchServiceInterface().getVideoController();
    View localPreviewView = vc.getLocalView();
    View remoteView = vc.getRemoteView();

    // Add the views to your view hierarchy
    ...
}
```

After the call has ended, don’t forget to remove the views from your view hierarchy again.

```java
@Override
public void onCallEnded(Call call) {
    // Remove Sinch video views from your view hierarchy
}
```

### Pausing and Resuming a Video Stream

To pause the local video stream, use the method `Call.pauseVideo()`. To resume the local video stream, use method `Call.resumeVideo()`.

```java
// User pauses the video stream
call.pauseVideo();

// User resumes the video stream
call.resumeVideo();
```

The call listeners will be notified of pause- and resume events via the callback methods `VideoCallListener.onVideoTrackPaused()` and `VideoCallListener.onVideoTrackResumed()`. Based on these events it is, for example, appropriate to update the UI with a pause indicator, and subsequently remove such pause indicator.

```java
@Override
public void onVideoTrackPaused(Call call) {
     // Implement what to be done when remote user pause video stream.
}

@Override
public void onVideoTrackResumed(Call call) {
     // Implement what to be done when remote user resumes video stream.
}
```

## Video Content Fitting and Aspect Ratio

How the remote video stream is fitted into a view can be controller by the `setResizeBehaviour()` method with possible arguments `VideoScalingType.ASPECT_FIT`, `VideoScalingType.ASPECT_FILL` and `VideoScalingType.ASPECT_BALANCED`. The local preview will always use `VideoScalingType.ASPECT_FIT`.

## Switching Capturing Device

The capturing device can be switched using `videoController.setCaptureDevicePosition(int facing)` with possible values `Camera.CameraInfo.CAMERA_FACING_FRONT` and `Camera.CameraInfo.CAMERA_FACING_BACK`. Use `videoController.toggleCaptureDevicePosition()` to alternate the two.

## Accessing Video Frames of the Remote Streams

The Sinch SDK can provide access to raw video frames via a callback function. This callback can be used to achieve rich functionality such as applying filters, adding stickers to the video frames, or saving the video frame as an image.

Your video frame handler needs to implement `VideoFrameListener` interface by implementing the `onFrame()` callback. Note that it is important to explicitly release the video frame by calling `release()`.

Example:

```java
import com.sinch.android.rtc.video.VideoFrame;
import com.sinch.android.rtc.video.VideoFrameListener;

public class YourVideoFrameHandler implements VideoFrameListener {
    public synchronized void onFrame(String callId, VideoFrame videoFrame) {
    ... // Process videoFrame
    videoFrame.release(); // Release videoFrame
    }
}
```

Use `setVideoFrameListener()` to register your video frame handler as the callback to receive video frames.

Example:

```java
YourVideoFrameHandler videoFrameHandler = new YourVideoFrameHandler();
VideoController vc = getSinchServiceInterface().getVideoController();
vc.setVideoFrameListener(videoFrameHandler);
```

**Converting video frame from I420 to NV21**

The Sinch SDK provides a helper function to convert the default I420 frame to NV21 Frame, which is handy to work with when you need to save it as an image on Android. Use `VideoUtils.I420toNV21Frame(VideoFrame)` for the conversion. Note that this helper does _NOT_ release the original I420 video frame.

Example:

```java
import com.sinch.android.rtc.video.VideoUtils; // To use I420toNV21Frame

VideoFrame videoFrame = ... // Get the video frame from onFrame() callback
VideoFrame nv21Frame = VideoUtils.I420toNV21Frame(videoFrame);

YuvImage image = new YuvImage(nv21Frame.yuvPlanes()[0].array(),
                              ImageFormat.NV21,
                              nv21Frame.width(),
                              nv21Frame.height(),
                              nv21Frame.yuvStrides());
```
