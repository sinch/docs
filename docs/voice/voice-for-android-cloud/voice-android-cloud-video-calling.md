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

Just like audio calls, video calls are placed through the [CallClient](reference/com/sinch/android/rtc/calling/CallClient.html) and events are received using the [CallClientListener](reference/com/sinch/android/rtc/calling/CallClientListener.html) (callback `onIncomingCall()`) and [VideoCallListener](reference/com/sinch/android/rtc/video/VideoCallListener.html) which is a subclass of [CallListener](reference/com/sinch/android/rtc/calling/CallListener.html) for other callbacks like `onCallEstablished()`, `onCallEnded()` etc. The call client is owned by the SinchClient and accessed using [SinchClient.getCallClient()](reference/com/sinch/android/rtc/SinchClient.html#getCallClient--). For a more general introduction to calling with the SinchClient, see [here](doc:voice-android-cloud-calling).

## Showing the Video Streams

Once you have created a [VideoCallListener](reference/com/sinch/android/rtc/video/VideoCallListener.html) and added it to a call using [Call.addCallListener](reference/com/sinch/android/rtc/calling/Call.html#addCallListener-com.sinch.android.rtc.calling.CallListener-), the [onVideoTrackAdded()](reference/com/sinch/android/rtc/video/VideoCallListener.html#onVideoTrackAdded-com.sinch.android.rtc.calling.Call-) method will be called.

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

To pause the local video stream, use the method [Call.pauseVideo()](reference/com/sinch/android/rtc/calling/Call.html#pauseVideo--). To resume the local video stream, use method [Call.resumeVideo()](reference/com/sinch/android/rtc/calling/Call.html#resumeVideo--).

```java
// User pauses the video stream
call.pauseVideo();

// User resumes the video stream
call.resumeVideo();
```

The call listeners will be notified of pause- and resume events via the callback methods [VideoCallListener.onVideoTrackPaused()](reference/com/sinch/android/rtc/video/VideoCallListener.html#onVideoTrackPaused-com.sinch.android.rtc.calling.Call-) and [VideoCallListener.onVideoTrackResumed()](reference/com/sinch/android/rtc/video/VideoCallListener.html#onVideoTrackResumed-com.sinch.android.rtc.calling.Call-). Based on these events it is, for example, appropriate to update the UI with a pause indicator, and subsequently remove such pause indicator.

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

Use [VideoController](reference/com/sinch/android/rtc/video/VideoController) to control various aspects of the local and remote video. Aquire `VideoController` using [SinchClient.getVideoController()](reference/com/sinch/android/rtc/SinchClient.html#getVideoController--). How the remote video stream is fitted into a view can be controller by the [VideoController.setResizeBehaviour()](reference/com/sinch/android/rtc/video/VideoController.html#setResizeBehaviour-com.sinch.android.rtc.video.VideoScalingType-) method with possible arguments `VideoScalingType.ASPECT_FIT`, `VideoScalingType.ASPECT_FILL` and `VideoScalingType.ASPECT_BALANCED`. The local preview will always use `VideoScalingType.ASPECT_FIT`.

## Switching Capturing Device

The capturing device can be switched using [VideoController.setCaptureDevicePosition(int facing)](reference/com/sinch/android/rtc/video/VideoController.html#setCaptureDevicePosition-int-) with possible values `Camera.CameraInfo.CAMERA_FACING_FRONT` and `Camera.CameraInfo.CAMERA_FACING_BACK`. Use [VideoController.toggleCaptureDevicePosition()](reference/com/sinch/android/rtc/video/VideoController.html#toggleCaptureDevicePosition--) to alternate the two.

## Accessing Video Frames of the Remote Streams

The Sinch SDK can provide access to raw video frames via a callback function. This callback can be used to achieve rich functionality such as applying filters, adding stickers to the video frames, or saving the video frame as an image.

Your video frame handler needs to implement [RemoteVideoFrameListener](reference/com/sinch/android/rtc/video/RemoteVideoFrameListener.html) interface by implementing the `onFrame()` callback. Note that this method blocks rendering. In-place modification of provided I420 frame be rendered. 

Example:

```java
import com.sinch.android.rtc.video.VideoFrame;
import com.sinch.android.rtc.video.RemoteVideoFrameListener;

public class YourVideoFrameHandler implements RemoteVideoFrameListener {
    public synchronized void onFrame(String callId, VideoFrame videoFrame) {
    ... // Process videoFrame
    }
}
```

Use [VideoController.setRemoteVideoFrameListener()](reference/com/sinch/android/rtc/video/VideoController.html#setRemoteVideoFrameListener-com.sinch.android.rtc.video.RemoteVideoFrameListener-) to register your video frame handler as the callback to receive video frames.

Example:

```java
YourVideoFrameHandler videoFrameHandler = new YourVideoFrameHandler();
VideoController vc = getSinchServiceInterface().getVideoController();
vc.setVideoFrameListener(videoFrameHandler);
```

## Accessing Video Frames of the Local Streams

Similar to the accessing the remote stream video frames, use [VideoController.setLocalVideoFrameListener()](reference/com/sinch/android/rtc/video/VideoController.html#setLocalVideoFrameListener-com.sinch.android.rtc.video.LocalVideoFrameListener-) to read or modify local video stream frames. You'll have to implement [LocalVideoFrameListener.onFrame()](reference/com/sinch/android/rtc/video/LocalVideoFrameListener.html#onFrame-com.sinch.android.rtc.video.VideoFrame-com.sinch.android.rtc.video.ProcessedVideoFrameListener-) callback.


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
