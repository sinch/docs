---
title: Sinch Client
excerpt: >-
  The Sinch SDK is a product that makes adding voice and video calling to mobile
  apps easy. Continue reading this step-by-step guide now.
hidden: false
next:
  pages:
    - voice-android-js-application-authentication
---

The _SinchClient_ is the Sinch SDK entry point. It is used to configure the user’s and device’s capabilities, as well as to provide access to feature functions such as the _callClient_, and _audioElement_.

## Create the _sinchClient_

```javascript
class SinchPhone {
  constructor() {
    this.sinch = Sinch;
    this.sinchApplicationKey = '<application_key>';
    this.apiUrl = 'ocra.api.sinch.com';
    this.callClient = null;
  }
```
* The _Application Key_ is obtained from the [Sinch Developer Dashboard - Apps](https://portal.sinch.com/#/apps). 
* The _User ID_ should uniquely identify the user on the particular device (for the sake of convienience in the sample apps the _getUserId_ is taken from a userID form element)
* (The term _Ocra_ in the hostname `ocra.api.sinch.com` is just the name for the Sinch API that the SDK clients target)

## Start the Sinch Client

Before starting the client, add a [SinchClientListener](reference\com\sinch\android\rtc\SinchClientListener.html):

```javascript
 start = () => {
    this.handleStartClientClick(this.sinchApplicationKey, this.apiUrl);
    this.handleMakeCallClick();
  }

  startSinchClient = (applicationKey, apiUrl) => {
    console.log('Sinch - Starting client');

    const getUserId = this.getUserId();
    const sinch = this.sinch.getSinchClient(applicationKey, getUserId, apiUrl);
    sinch.setEventListener(this);
    sinch.start();
  }

  onClientStarted = async (sinch) => {
    console.log(`Sinch - Client started for ${ sinch.userId }`);

    const callClient = sinch.getCallClient();
    callClient.addEventListener(this);
    this.callClient = callClient;
  }
```

### Authorizing the Client / User

When the _SinchClient_ is started with a given _User ID_ (__userId__ in the following example) it is required to provide an authorization token to register towards the _Sinch backend_. To authorize use the _createJWT_, passing the t _application_id_,_application_secret_ and _user_id_. 

```javascript

 onCredentialsRequired = (sinch, clientRegistration) => {
    console.log('Sinch - Credentials required');

    this.createJWT(this.sinchApplicationKey, this.sinchApplicationSecret, sinch.userId).then((jwt) => {
      clientRegistration.register(jwt);
    });
  }

```
> ⚠
>
> When deploying your application to production, do not embed the Application Secret in the application. The example above is only meant to show how to provide a signed JWT to the _SinchClient_. Implement the required functionality on your backend and fetch signed registration token when required.

## Promises / Asynchronous Sinch calls

_async_ is used for the function to return a promise. _await promise_ is used to return the result from the promise.  __try..catch__ is implemented to handle errors.
