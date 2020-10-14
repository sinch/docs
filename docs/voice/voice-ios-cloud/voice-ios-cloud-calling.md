---
title: Voice Calling
excerpt: >-
  Set up voice calling with the Sinch Voice & Video SDK. The SDK supports four
  types of calls: _App-to-App_ (audio or video), _App-to-Phone_, _App-to-SIP_ and
  _Conference_ calls.
hidden: false
next:
  pages:
    - voice-ios-cloud-video-calling
---

The Sinch SDK supports four types of calls: _App-to-App_ (audio or video), _App-to-Phone_, _App-to-SIP_ and _Conference_ calls. The `SINCallClient` is the entry point for the calling functionality of the Sinch SDK.

Calls are placed using `SINCallClient` and events are received via `SINCallClientDelegate`. The call client (`SINCallClient`) is owned by the _Sinch client_ (`SINClient`) and accessed using `-[SINClient callClient]`.

## Setting Up an _App-to-App_ Call

Use the call client to start the call using the method `callUserWithId:`, passing the user identifier of the callee (the destination user) as an argument.

```objectivec
id<SINCallClient> callClient = [sinchClient callClient];
id<SINCall> call = [callClient callUserWithId:@"<remote user id>"];
call.delegate = self; // Assign delegate to observe call state changes
```

A call object is returned, containing details about the participants in the call, call details such as start time, call state, possible errors, and so on.

Assuming the callee’s device is available and responsive, the delegate method `callDidProgress:` will be called. It notifies the application that the outbound call is progressing. If a progress tone should be played, this is where it should be started. We recommend that you use the available functionality provided by the Sinch SDK to play sounds such as ringtones (`SINAudioController`), see [Playing Ringtones](doc:voice-ios-cloud-playing-ringtones).

When the other party answers, the delegate method `callDidEstablish:` method will be called. Now, the users can start talking. If a progress tone has previously been initiated, it should be stopped now, in the delegate callback method.

Also see [Handling Incoming Calls](doc:voice-ios-cloud-calling#handling-incoming-calls).

## Setting Up an _App-to-Phone_ Call

An _App-to-Phone_ call is a call that is made to a phone on the regular telephone network. Setting up an _App-to-Phone_ call is not much different from setting up an _App-to-App_ call.

Initiate a call to a phone number by calling the method `-[SINCallClient callPhoneNumber:]`. The phone number should be specified according to the [E.164\_ number formatting standard](https://en.wikipedia.org/wiki/E.164) and should be prefixed with a ‘+’.

**Example**: To call the US phone number _415 555 0101_, the phone number should be specified as `+14155550101`. The `‘+’` is the required prefix and the US country code `‘1’` prepended to the local subscriber number.

_App-to-Phone_ calls can be tested by calling the following test number: _+46000000000_. When placing a call to this number, you will hear a voice prompt stating that the call has been connected, and shortly after that the call will automatically be ended.

> ⚠ Credits Required
>
> Placing an _App-to-Phone_ call requires that your Sinch account has sufficient credits; Top up credits on your [Account](https://portal.sinch.com/#/account) page. Credits are used each time an _App-to-Phone_ call is placed and your account balance is updated after each call.

> ⚠ Testing DTMF
>
> In _App-to-Phone_ scenario, it is possible to issue DTMF sequences using Sinch SDK. Please note that if the receiving end of the call is an iOS device, you might have to disable _VoLTE_ ("Voice over LTE") option in the settings of the phone at the receiving end of the call in order to be able to hear DTMF tones.

## Setting Up an _App-to-SIP_ Call

An _App-to-SIP_ call is a call that is made to a [SIP](https://en.wikipedia.org/wiki/Session_Initiation_Protocol) server. Use the method `-[SINCallClient callSIP:]` or `-[SINCallClient callSIP:headers:]` to initiate a _SIP_ call. The destination _SIP_ identity follows the form of email addresses (<user@domain>), for example <alice@sip.your-sip-provider.com>.

```objectivec
id<SINClient> sinchClient = ...; // Sinch client setup since before.
id<SINCallClient> callClient = [sinchClient callClient];
id<SINCall> call = [callClient callSIP:@"<SIP identity>"];
```

When custom _SIP_ headers are passed as using `-[SINCallClient callSIP:headers:]`, the headers should be prefixed with ‘x-’.

## Setting Up a _Conference_ Call

A _Conference_ call can be made to connect a user to a conference room where multiple users can be connected at the same time.

```objectivec
id<SINCallClient> callClient = [sinchClient callClient];
id<SINCall> call = [callClient callConferenceWithId:@"<conference id>"];
```

It is also possible to connect users to a conference call via the [Sinch REST API](doc:voice-rest-api-calling-api#text-to-speech-callout).

> ❗️Note
> The identifier for a conference room may not be longer than 64 characters.

## Handling Incoming Calls

To act on the incoming calls, implement the protocol `SINCallClientDelegate` and assign a delegate to the call client. The call client delegate is notified of new incoming calls via the delegate method `didReceiveIncomingCall:`.

To get events related to the call, assign a call delegate. The call object contains details about participants, start time, potential error codes, and error messages. See `-[SINCall details]` and [SINCallDetails](reference\html\Protocols\SINCallDetails.html).

If you using _VoIP Push Notifications_ and [CallKit](https://developer.apple.com/documentation/callkit), use `didReceiveIncomingCall:` primarily to associate the `SINCall` with the _CallKit_-call. E.g. this may be implemented by keeping a mapping between _CallKit_ calls and `SINCall`. Example:

```objectivec
- (void)client:(id<SINCallClient>)client didReceiveIncomingCall:(id<SINCall>)call {
    // Assign delegate
    call.delegate = self;

    // Store in a NSDictionary for later, to be able to act on CXCallAction (CallKit action).
    [self.calls setObject:call forKey: [[NSUUID alloc] initWithUUIDString:call.callId]];
}
```

> 👍 See our _CallKit_ sample apps
>
> In the _Sinch SDK_ download, there is a sample app `SinchCallKit.xcodeproj` with a detailed example on how to associate `SINCall` with _CallKit_ calls.

If you are **not** using _VoIP Push Notifications_ and [CallKit](https://developer.apple.com/documentation/callkit), then use `didReceiveIncomingCall:` to assign a delegate, and present a call UI. If the application is in foreground, you may also want to [play a ringtone](doc:voice-ios-cloud-playing-ringtones).

```objectivec
- (void)client:(id<SINCallClient>)client didReceiveIncomingCall:(id<SINCall>)call {
    // Assign delegate
    call.delegate = self;

    // Play ringtone

    // Present UI for call (UIViewController / segue)
}
```

### Incoming Video Call

A video call will just like a voice-only call trigger the delegate method `didReceiveIncomingCall:`. To determine whether an incoming call contains a video stream, the property `SINCallDetails.isVideoOffered` can be used. Also see [Video Calling](doc:voice-ios-cloud-video-calling) for details on how to add video views.

### Answering an Incoming Call

To answer a call, use the method `-[SINCall answer]`. If a ringtone was previously played, it should be stopped now.

User presses the answer button:

```objectivec
// User answers the call
[call answer];

// Stop playing ringing tone (unless using CallKit)
```

Now, the clients on both ends will establish the connection. When the call is established and the voice streams are running in both directions, the `callDidEstablish:` delegate method is called.

### Declining an Incoming Call

If the call should not be answered, use the method `-[SINCall hangup]` to decline the call. The caller will be notified that the incoming call was denied (via `-[SINCallDelegate callDidEnd:]`). If a ringtone was previously played, it should be stopped now.

User presses the hangup button:

```objectivec
// User declines the call
[call hangup];

// Stop playing ringing tone
```

## Disconnecting a Call

When the user wants to disconnect an ongoing call, use the method `-[SINCall hangup]`. Either user taking part in a call can disconnect it.

Hanging up a call:

```objectivec
[call hangup];
```

When either party disconnects a call, the application is notified using the call delegate method `-[SINCallDelegate callDidEnd:]`. This allows the user interface to be updated, an alert tone to be played, or similar actions to occur.

A call can be disconnected before it has been completely established.

Hanging up a connecting call:

```objectivec
// Starting a call
id<SINCall> call = [client callUserWithId:@"<remote user id>"];

// User changed his/her mind, let's hangup.
[call hangup];
```

Handling a call that ends:

```objectivec
// SINCallDelegate implementation

- (void)callDidEnd:(id<SINCall>) call {
  // Update user interface, e.g. hide the call screen.
```

## Request User Permission for Using the Microphone

Recording audio always requires explicit permission from the user. Your app must provide a description for its use of the microphone in terms of the _Info.plist_ key [NSMicrophoneUsageDescription](https://developer.apple.com/documentation/bundleresources/information_property_list/nsmicrophoneusagedescription).

See Apple documentation on [`+[AVCaptureDevice requestAccessForMediaType:completionHandler:]`](https://developer.apple.com/documentation/avfoundation/avcapturedevice/1624584-requestaccessformediatype) for details on how to request user permission.

> 📘
> Unless the application has explicitly requested permission to use the microphone, the user is shown a dialog the first time the microphone is activated. This may not provide the best user experience, so we advice to consider explicitly requesting microphone permission in advance.
