---
title: Voice Calling
excerpt: >-
  Voice calling with Android Voice and Video SDK. Set up calling between
  applications, from application to phone, app to SIP and conference calls. Get
  more information here.
hidden: false
next:
  pages:
    - voice-android-cloud-video-calling
---

The Sinch SDK supports four types of calls: _app-to-app (audio or video)_, _app-to-phone_, _app-to-sip_ and _conference_ calls. The _CallClient_ is the entry point for the calling functionality of the Sinch SDK.

Calls are placed through the _CallClient_ and events are received using the _CallClientListener_. The call client is owned by the SinchClient and accessed using `sinchClient.getCallClient()`.

## Set Up an _App-to-App_ Call

Use the CallClient to start the call (the `callUser` method). Pass the user identifier of the callee (the user receiving the call) to the call method, so that Sinch services can connect the call to the callee.

```java
CallClient callClient = sinchClient.getCallClient();
Call call = callClient.callUser("<remote user id>");
// Or for video call: Call call = callClient.callUserVideo("<remote user id>");
call.addCallListener(...);
```

A call object is returned, containing details about the participants in the call, call details such as start time, call state, possible errors, and so on.

Assuming the callee’s device is available, the method `onCallProgressing` is called on the `CallListener`. It notifies the application that the outgoing call is progressing. If a progress tone should be played, this is where it should be started.

When the other party answers, the `onCallEstablished` method is called. Now, the users can start talking. If a progress tone was previously played, it should be stopped now.

## Set Up an _App-to-Phone_ Call

An _app-to-phone_ call is a call that is made to a phone on the regular telephone network. Setting up an _app-to-phone_ call is not much different than setting up an _app-to-app_ call. Instead of invoking the `callUser` method, invoke the `callPhoneNumber` method on the `CallClient` object. Sufficient funds must be available on the Sinch account and a valid phone number specified for the call to connect successfully. The phone number should be specified according to the E.164 number formatting (<http://en.wikipedia.org/wiki/E.164> ) recommendation and should be prefixed with a ‘+’. E.g. to call the US phone number 415 555 0101, the phone number should be specified as “+14155550101”. The ‘+’ is the required prefix and the US country code ‘1’ prepended to the local subscriber number.

Placing an _app-to-phone_ call requires a developer account with credits. Topping up credits can be done on the Account page. Credits are used each time an _app-to-phone_ call is placed and the balance history is updated after each call.

_App-to-phone_ calls can be tested by calling the following test number: _+46000000000_. When placing a call to this number, you will hear a voice prompt stating that the call has been connected, and shortly after that the call will automatically be ended.

## Set Up an _App-to-sip_ Call

An _app-to-sip_ call is a call that is made to a SIP server. Setting up an _app-to-sip_ call is not much different from setting up an _app-to-app_ call. Instead of invoking the `callUser` method, invoke the `callSip` method on the `CallClient` object. The SIP identity should be in the form “<user@server>”. By convention, when passing custom headers in the SIP call, the headers should be prefixed with “x-”. If the SIP server reported any errors, the `CallDetails` object will provide an error with the `SIP` error type.

## Set Up a _Conference_ Call

A _conference_ call can be made to connect a user to a conference room where multiple users can be connected at the same time. The identifier for a conference room may not be longer than 64 characters.

```java
CallClient callClient = sinchClient.getCallClient();
Call call = callClient.callConference("<conferenceId>");
call.addCallListener(...);
```

It is also possible to connect users to a conference call via the [Sinch REST API](doc:voice-rest-api-onprem-calling-api#conference-and-text-to-speech-callouts).

## Handle Incoming Calls

To answer calls, the application must be notified when the user receives an incoming call.

Add a `CallClientListener` to the `CallClient` to act on the incoming calls. The `CallClientListener` is notified using `onIncomingCall` as calls come in to the device.

```java
CallClient callClient = sinchClient.getCallClient();
callClient.addCallClientListener(...);
```

When the incoming call method is executed, the call can either be connected automatically without any user action, or it can wait for the user to press the answer or the hangup button. If the call is set up to wait for a user response, we recommended that a ringtone is played to notify the user that there is an incoming call.

```java
@Override
public void onIncomingCall(CallClient callClient, Call call) {
    // Start playing ringing tone
    ...

    // Add call listener
    call.addCallListener(...);
}
```

To get events related to the call, add a call listener. The call object contains details about participants, start time, potential error codes, and error messages.

### Incoming video call

When an incoming call is a video call, the `onIncomingCall` callback will be executed, just like for the incoming audio call. The `CallDetails` object provides an `isVideoOffered()` method to check whether the call offers a video track. See the \[Video calling\] section for details on how to add video views.

### Answer incoming call

To answer the call, use the `answer` method on the call to accept it. If a ringtone was previously played, it should be stopped now.

User presses the answer button:

```java
// User answers the call
call.answer();

// Stop playing ringing tone
...
```

Now, the clients on both ends establish the connection. When the call is established and the voice streams are running in both directions, the `onCallEstablished` listener method is called.

### Decline incoming call

If the call should not be answered, use the `hangup` method on the call to decline. The caller is notified that the incoming call was denied. If a ringtone was previously played, it should be stopped now.

User presses the hangup button:

```java
// User does not want to answer
call.hangup();

// Stop playing ringing tone
...
```

## Disconnecting a Call

When the user wants to disconnect an ongoing call, use the `hangup` method. Either user taking part in a call can disconnect it.

Hanging up a call:

```java
call.hangup();
```

When either party disconnects a call, the application is notified using the call listener method `onCallEnded`. This allows the user interface to be updated, an alert tone to be played, or similar actions to occur.

A call can be disconnected before it has been completely established.

Hanging up a connecting call:

```java
// Starts a call asynchronously
Call call = callClient.callUser("<remote user id>");

// User changed his/her mind, let’s hangup
call.hangup();
```

## Volume Control

To make sure that the volume of the call can be modified by the hardware volume controls, `setVolumeControlStream(AudioManager.STREAM_VOICE_CALL)` must be called on the `Activity` where the call is handled. Make sure that `volumeControlStream` is reset to a suitable value when the call has ended.

For example, after creating a call (using `CallClient.callUser`) or when answering a call (using `Call.answer()`) you should call `setVolumeControlStream(AudioManager.STREAM_VOICE_CALL);`.

When the call ends, set the volume control stream back to it’s previous value. For example in your implementation of `CallListener`:

```java
@Override
public void onCallEnded(Call call) {
    setVolumeControlStream(AudioManager.USE_DEFAULT_STREAM_TYPE);
}
```

## Audio Routing

_AudioController_ interface allows you to control audio routing. Aquire _AudioController_ using `SinchClient.getAudioController()` method, and use following methods:

| Method   | Description                                                               |
| :------ | :-------------------------------------------------------------------------------- |
| `mute()`   | Mutes audio input.              |
| `unmute()`   | Unmutes audio input.          |
| `enableSpeaker()`   | Enables speaker mode.  |
| `disableSpeaker()`   | Disables speaker mode.|
| `enableAutomaticAudioRouting()`   | Enables automatic audio (AAR).|
| `disableAutomaticAudioRouting()`   | Disables automatic audio routing (AAR).|

### Automatic audio routing

You can enable automatic audio routing using `AudioController.enableAutomaticAudioRouting()` method.
It enables automatic audio routing between earpiece, speakerphone, wired headset and Bluetooth audio devices.

It takes two parameters: 
* `boolean manageBluetoothAudio` if set to true, allows to automatically reroute audio to Bluetooth headset when available. Fires `MissingPermissionException` if `android.Manifest.permission.BLUETOOTH` is not granted.
* `AudioController.UseSpeakerphone useSpeakerphone` can be set to `AUTO`, `TRUE` or `FALSE`. The `AUTO` mode uses a proximity sensor to operate.

Priorities are following:
* Bluetooth (if available and manageBluetoothAudio == true)
* Wired Headset
* Default audio device if `useSpeakerphone` is `TRUE` or `FALSE`; or Proximity sensor's based decision (speakerphone / earpiece) if `useSpeakerphone` is `AUTO`.

Default audio device (speakerphone/ earpiece) is set using `useSpeakerphone` parameter.
