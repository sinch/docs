---
title: iOS Push Notifications Using SinchService
excerpt: >-
  At Sinch, we’ve been fortunate not only to provide an intuitive API, but also
  contribute to the open-source community around it. Part of that initiative is
  the SinchService for iOS. In this tutorial, we’ll take a look at the
  SinchService, discuss some best practices, and explain how to use it.
---
At Sinch, we’ve been fortunate not only to provide an intuitive API, but also contribute to the open-source community around it. Part of that initiative is the SinchService for iOS. With it, we’ve abstracted a lot of the heavy lifting and common functionality so you can get started with Sinch as fast as possible.

In this tutorial, we’ll take a look at the SinchService, discuss some best practices, and explain how to use it. This builds on Christian’s [How to Use iOS Managed Push tutorial](doc:ios-managed-push-tutorial).
![pushImage-small.png](images\c0c6fa4-pushImage-small.png)

## Getting started

To get started with this tutorial, be sure to download the starter project [here](https://sinch.readme.io/page/downloads). The SDK comes packaged with a few sample projects, but the one we are interested in is called “SinchCallingPushWithService”. Note that if you already have a project using the Sinch SDK, you can use that as well.

### CocoaPods

Next, install the SinchService via [CocoaPods](https://cocoapods.org/). If you are unfamiliar with CocoaPods, don’t worry; you can set up everything by following these steps:

>   - **If you’ve never installed CocoaPods**, open terminal and first update RubyGems to ensure you have the latest version using this command: `sudo gem update --system`.
>   - Next, install CocoaPods: `sudo gem install cocoapods`.
>   - Now, navigate to your project’s folder using the `cd` command.
>   - We’ve included a pod file for you already, so just run `pod install` and you’ll be all set.

You can also add more libraries to the pod file as well. Once you’ve navigated to where the podfile is, use this command: `open -a Xcode Podfile`.

#### Push notification certificates

No matter if you are using the sample app or one of your own, you’ll need to have push notifications enabled. If you haven’t done this before, you’ll want to follow the [guide created by Apple](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1).

Note that if you don’t include certificates for VoIP services or user Pushkit, the legacy push notifications will still function just fine.

Once you’ve generated the certificates, upload them to your Sinch dashboard for the application you’re working with. All three push notifications certificate types are supported:
![ssKeys.PNG](images\8d7ed75-ssKeys.PNG)

At this point, you’re ready to start using Sinch and push notifications. Let’s take a look at some of the things SinchService can do for us.

## SinchService: Why use it?

At its core, SinchService acts as a supplement to the Sinch API. If you are familiar with our API already, it specifically bolsters both **SinchRTC** and **SINClient**. With it, you can set up all the events for calls, use the notification center to listen for those events anywhere you need, handle local or remote notifications, and even implement messaging.

For example, the `SINClient` is the main entry point to interact with our API. When your app leverages several of our features, you may have a class that conforms to several protocols.

Say your app is using our API to enable calling and push notifications. If you implement your client on the AppDelegate, as we have in several code samples, your implementation file may look something like this:

```objectivec
@interface AppDelegate () <SINClientDelegate, SINCallClientDelegate, SINManagedPushDelegate>
```

While that certainly works, SinchService makes this more concise. There are essentially three main responsibilities here, and right now they are going to be spread across the AppDelegate. In addition, to properly support push notifications, an instance of `SINManagedPush` is necessary as well:

```objectivec
@property (nonatomic, readwrite, strong) id<SINManagedPush> push;
```

SinchService can take care of a lot of this setup for you. Since it houses most of the Sinch API functionality, we can rely on one focused object to to handle important events and do the necessary setup. As you’ll see, SinchService has a several helpful properties that can help with these tasks, such as the previously mentioned `SINManagePush`.

## Initializing SinchService

Getting SinchService up and running is easy. Its initializer looks very similar to the `SINClient` but differs in that we’ll want to do some configuration upfront. Once we’ve set our configuration options, we’ll call the initializer to get our instance of SinchService ready to go:

```objectivec
id config = [[SinchService configWithApplicationKey:@"<YOUR_APP_KEY>" applicationSecret:@"<YOUR_APP_SECRET>"      environmentHost:@"clientapi.sinch.com"]        pushNotificationsWithEnvironment:SINAPSEnvironmentDevelopment];

id<SINService> sinch = [SinchService serviceWithConfig:config];
```

At this point, you’ve already offloaded the work `SINClient` would normally do over to the SinchService instance. Using the `SINAPSEnvironmentDevelopment` value we are making sure we supply device token to the Apple Push Notification Service Sandbox Environment. You are responsible for specifying correct APNSEnvironment depending on your build type and distribution. Environment must always match signing identity.

## SinchService delegate

The SinchService instance should have a delegate assigned to it. One helpful delegate method to implement is `-(void)service:(id<SINService>)service didFailWithError:(NSError *)error`. This method will be invoked if something goes wrong when getting the SinchService going or communicating with the API.

Once you set its delegate, you are able to do all the things you can with the `SINClient` object. If we refer to our example before, you’ll see there is one less protocol to conform to:

```objectivec
@interface AppDelegate () <SINServiceDelegate, SINCallClientDelegate>
```

This makes your code easier to follow and more concise. You no longer need the SINManagedPush protocol or the `SINManagedPush` object. SinchService houses all of these together.

## Calling, push notifications, and more

What makes SinchService so great is that it can handle any of Sinch’s core functions. In iOS, that means making and receiving calls, sending and receiving push notifications, messaging, and more. You’ll still use the same protocols you are used to if you’ve worked with our API before.

If you haven’t though, don’t worry. I’ll provide a brief overview of those functions in action using SinchService. Let’s take a look at making calls to start things off.

### Handling call events

Two important things you’ll want to know about when dealing with calls using Sinch are the **SINCall** object and **SINCallDelegate**. In addition, there are two delegate methods you can implement in the `SINCallClientDelegate` protocol to know when a call finished or if a notification was received for one.

SinchService can help you take care of all of this. In fact, it already has a `SINCallClient` property that you can use to handle your calls. The call client is your point of entry for any call based functionality inside the Sinch SDK.

Let’s review the basics of calling with SinchService. First, we’ll talk about `SINCall`. This is primarily responsible for controlling the flow of the calls. With it, you can do the following actions:

>   - Answer calls
>   - Decline calls
>   - Hang up a call

The methods for completing these actions are named appropriately (i.e. answer and dismiss). Typically, you’ll use the `dismiss` method to either hang up a call or decline an incoming call.

To make the call, you’ll need to invoke `callWithId:` from a `SINCallClient` object. To initiate a call with the SinchService, it might look something like this:

```objectivec
[sinchServiceInstance.callClient callUserWithId:id];
```

Calling this method will also return a `SINCall` instance, which will be needed to call the methods specified above for making, rejecting, and ending calls. If your refer to the demo project, you can see this inside the `MainViewController.m` file.

We also have some helpful delegate methods that can be implemented to take action during the lifetime of a call. Updating your user interface during a call makes the session feel natural and understandable to the user. You can facilitate this by calling some of the provided methods.

For example, the user will need to know when a call has started, ended, or is ringing. These delegate methods will let you know when any of these actions has occurred:

>   - `(void)callDidProgress:(id<SINCall>)call`
>   - `(void)callDidEstablish:(id<SINCall>)call`
>   - `(void)callDidEnd:(id<SINCall>)call`

You could even create a simple timer so that the user will know how long the call has progressed. Using the `-(void)callDidEstablish:(id<SINCall>)call` function, simply start the timer and update the user interface. We have a working sample of this in the demo app that can be found in the `CallViewController`.

SinchService even has access to an instance of `SINAudioController` via its `audioController` property. The `SINAudioController` provides methods for controlling audio-related tasks like enabling the speaker, muting the microphone, and playing sound files. This is the perfect candidate for playing any dialing, calling, or hang up sounds.

To see a more in-depth example of handling calls with Sinch, be sure to take a look at our SinchCalling project. It can be found inside the samples folder that’s included in the Sinch API download. Calling is a core feature of our API, and remember, getting started is as easy as calling `callUserWithId:`\!

### Handling remote notifications

The `SINManagedPush` object is used to simplify the process of working with push notifications. To work with notifications, you’ll first need to acquire a push notification token and then register it with the Sinch client. Using SinchService, we can do all of that.

Keep in mind that we’ll want to create an instance of `SINManagedPush` as early as possible in the application lifecycle. Normally, that code would look something like this inside `application:DidFinishLaunchingWithOptions`:

```objectivec
self.push = [Sinch managedPushWithAPSEnvironment:SINAPSEnvironmentDevelopment];
self.push.delegate = self;
[self.push setDesiredPushTypeAutomatically];
[self.push registerUserNotificationSettings];
```

The `SINManagedPush` does quite a bit of work for us. It simplifies the iOS SDK API differences between iOS 7 and iOS 8, as well as differences for regular remote push notifications and VoIP push notifications. So, four lines of code is not much of a burden.

Even so, SinchService makes this even easier. When you initialize SinchService, we saw earlier that you do so with an initial configuration. To refresh your memory, it looked like this:

```objectivec
id config = [[SinchService configWithApplicationKey:@"<YOUR_APP_KEY>" applicationSecret:@"<YOUR_APP_SECRET>"                                 environmentHost:@"clientapi.sinch.com"]        pushNotificationsWithEnvironment:SINAPSEnvironmentDevelopment];
```

By using SinchService , the `SINManagedPush` setup is done and out of the way. All that we need to do is give it the appropriate environment. Since the Sinch API now provides push notification services, you do not need to worry about spinning up a server to handle them. Even better, you can use SinchService to quickly register them.

First, you’ll need to specify that Sinch will be acting as the push notification server. When the user’s device successfully registers for push notifications, you can call this convenience method from SinchService to do just that:

```objectivec
- (void)application:(UIApplication *)application
    didRegisterForRemoteNotificationsWithDeviceToken:(NSData *)deviceToken {
  [sinchService.push application:application didRegisterForRemoteNotificationsWithDeviceToken:deviceToken];
}
```

You’ll also want to be able to handle incoming remote notifications. SinchService can do this for you as well:

```objectivec
- (void)application:(UIApplication *)application didReceiveRemoteNotification:(NSDictionary *)userInfo {
  [sinchService.push application:application didReceiveRemoteNotification:userInfo];
}
```

Now, you can interact with `SINManagedPush` easily since it lives as a property on your SinchService instance. You can manage incoming notifications, receiving a token, and more using SinchService. Remember, SinchService can handle remote, local, and VoIP notifications.

## Wrapping up

Hopefully you’ll get some good use out of SinchService. It’s been tuned to make your experience with our API as seamless as possible. It abstracts a lot of what `SINClient` does and makes working with `SINManagedPush` easier as well. Whether it’s calling, messaging, or notifications you are looking to implement, SinchService can help with any of them.

We hope you continue to build amazing apps with Sinch and feel free to reach out to us if you have any questions.

