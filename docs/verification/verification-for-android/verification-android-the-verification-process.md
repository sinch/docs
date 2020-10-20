---
title: The verification process
excerpt: >-
  The verification process in the Android Verification SDK. It is performed in
  two steps: requesting a verification code and verifying the received code.
  This can be done through Sms, flashcall, callout and seamlessly.
next:
  pages:
    - verification-android-all
---
Verification of a phone number is performed in two steps: requesting a verification code and verifying the received code.

Depending on chosen verification method, the SDK can automatically verify any matching code (assuming all the required permissions were provided (see [Permissions](#permissions))) or wait for manual code input.

 - SMS Verification - Received text message will be matched against the expected template. If it matches, the code will be extracted and sent to Sinch backend.
 - Flashcall Verification - Any incoming phone call number will be checked if it matches expected mask. If it does, the full number will be sent to the backend thus passing the verification process. If the process finishes successfully, the call will be hung up automatically.
 - Callout Verification - No automatic code extraction is possible. SDK expects the code to be passed manually. User can obtain the code by answering the incoming phone call, during which text-to-speech software will speak it out loud.
 - Seamless Verification - Completely automatic method. Two steps of verification mentioned earlier are unified into one. Sinch uses telephony provider infrastructure to verify the phone number without any user interaction. Because of that, for this method to work, the mobile data should be enabled (in case both WiFi and mobile data are on, SDK will try to force usage of the latter for API calls).

During the process of every verification, SDK will callback first to the `InitiationListener` (with the result of the verification request) and then to the `VerificationListener` (result of specific code verification), see [InitiationListener](#initiation-listener) and [VerificationListener](#verification-listener) for more information.

If the permissions needed for automatic verification are not provided, method does not provide automatic verification or for some other reason automatic code extraction fails the `verify` method on the `Verification` object should be invoked with a user-supplied verification code to verify the phone number.

> **Note**    
>
> The SDK will abort verification process and trigger the `onVerificationFailed` passing `CodeInterceptionTimeoutException` after a certain time, typically around 20 seconds. However, in case of flashcall the SDK will continue to listen for calls for an additional time after the verification has failed, in order to intercept and report any possible late calls.

## Permissions

The `android.permission.INTERNET` permission is required for the Verification SDK to work.
To handle flash call verification automatically `android.permission.READ_CALL_LOG` is needed.
In case of seamless method SDK needs `android.permission.CHANGE_NETWORK_STATE` to be able to automatically switch to mobile data for API calls connected with the verification process.
Additionally, SDK collects phone metadata information connected with sim cards or phone software version, which are then used to handle early verification rejection rules. For this module to be fully functional `android.permission.ACCESS_NETWORK_STATE` and  `android.permission.READ_PHONE_STATE` should be granted. These permissions however are not essential.

### Permissions handling on API levels 23 or later.

If your application targets API 23 or later the permissions have to be requested at runtime, see [Runtime Permissions](https://developer.android.com/training/permissions/requesting.html). It is recommended to ask the user for corresponding permissions just before initiating verification. If the verification SDK fails to intercept the code automatically due to missing permissions, the `onVerificationFailed` callback will be executed with an instance of `CodeInterceptionException`. In this case it is still possible to proceed with verification by asking the user to enter the code manually.

## Creating a Verification

To initiate a verification process a `Verification` object needs to be created. Depending on chosen verification method this can be accomplished by calling method specific builder, passing configuration object instance and optionally initiation and verification listeners.

```java
val verification = SmsVerificationMethod.Builder() //Use FlashcallVerificationMethod for flashcalls, CalloutVerificationMethod for callout etc.
  .config(/* Method config instance */)
  .initiationListener(/* Initiation listener instance */)
  .verificationListener(/* Verification listener instance */)
  .build()
```

[Fluent Builder Pattern](https://dzone.com/articles/fluent-builder-pattern) is used for every object creation so the process should be self explanatory and straightforward.

### Verification configuration objects

Every verification method requires a specific configuration object to be passed during the building process. Similarly to creating a verification this is done by calling config specific builder and passing the arguments requested by the [Fluent Builder Pattern](https://dzone.com/articles/fluent-builder-pattern).

```java
val configuration = SmsVerificationConfig.Builder()
  .globalConfig(/* Global config instance */)
  .number(/* Number to be verified */)
  // Other optional arguments
  .build()
```

### Global Configuration
Contrary to configurations specified for each verification request, for SDK to work, a single, global verification object needs to be created once and then passed to each verification method configuration. This object is simply a holder for the Android `Context` instance and `AuthorizationMethod` used to authorize the application against Sinch API (currently only `AppKeyAuthorizationMethod` is supported).
```java
SinchGlobalConfig.Builder()
  .applicationContext(this)
  .authorizationMethod(AppKeyAuthorizationMethod(/*Application Key*/))
  .build()
```

> **WARNING: Important**    
>
> It is important to pass `ApplicationContext` to the global config builder, as the object might outlive the activity or fragment it is instantiated from.

### Automatic code extraction from SMS

Due to newly introduced [restrictions for accessing SMS logs](https://support.google.com/googleplay/android-developer/answer/9047303?hl=en), automatic code extraction now relies on Google Play Services API. To enable it in your project:

1.  Calculate application hash from your release key: ``keytool -exportcert -alias KEY_ALIAS -keystore MY_KEY.jks | xxd -p | tr -d "[:space:]" | echo -n MY.APPLICATION.PACKAGE `cat` | sha256sum | tr -d "[:space:]-" | xxd -r -p | base64 | cut -c1-11``
Example result: `XtNB5qNssI/`

2.  Include generated application hash in SmsVerificationConfig:
```java
val smsMethodConfiguration = SmsVerificationConfig.Builder()
  .globalConfig(/* Global config instance */)
  .number(/* Number to be verified */)
  .appHash(/* generated hash */)
  // Other optional arguments
  .build()
```

That’s it\! When this configuration is applied, SDK will be able to automatically parse incoming verification messages across all Android versions without the need of declaring SMS permissions in app’s manifest.

### Verification Language
Although it possible to specify the language that will be used during the verification process, currently only sms method supports this feature. In order to to that, a list of `VerificationLanguage` objects should be passed to the configuration builder.

```java
val topPriorityLanguage = VerificationLanguage(language = "pl", region = "PL", weight = 1)
val secondaryLanguage = VerificationLanguage(language = "es", region = "ES", weight = 0.33)
val smsMethodConfiguration = SmsVerificationConfig.Builder()
  .globalConfig(/* Global config instance */)
  .number(/* Number to be verified */)
  .appHash(/* generated hash */)
  .acceptedLanguages(listOf(topPriorityLanguage, secondaryLanguage))
  // Other optional arguments
  .build()
```

> **Note**    
>
> The content language specified can be overridden by carrier provider specific templates, due to compliance and legal requirements, such as [US shortcode requirements (pdf)](https://www.wmcglobal.com/storage/us_resources/ctia-short-code-monitoring-handbook-current-Short-Code-Monitoring-Handbook-v1.7.pdf).


See [IETF](https://tools.ietf.org/html/rfc3282) language tags to find all possible values that can be passed as region and language parameters of `VerificationLanguage` constructor. The weight argument should be within range 0 - 1 (lowest to highest priority). To find out what language was actually selected check `contentLanguage` property of `SmsInitiationResponseData` passed to `onInitiated` callback of the initiation listener.

### Initiating the verification process

After creating a verification method object instance simply call it's `initiate` method to start the verification process.


## Initiation listener

The `InitiationListener` provides callbacks during the initiation phase of the verification process (requesting the verification code). If initiation is successful, the `onInitiated` callback is triggered passing the method specific initiation data (such as selected verification language, sms template, incoming call mask or verification id).
If initiated method supports automatic code interception SDK starts the process automatically. Otherwise it waits for manual code input via the `verify` method.
If initiation fails, the `onInitializationFailed()` callback runs and the exception describing the error is passed.

## Verification listener

The `VerificationListener` provides callbacks for the second stage of the verification process - checking if passed code is correct. If it is the `onVerified` callback is called. If it is not or some other error occurs `onVerificationFailed` callback runs and the exception describing the error is passed.


## Phone numbers

> **WARNING: Important**    
>
> The phone number should be specified according to the E.164 number formatting (<http://en.wikipedia.org/wiki/E.164>) recommendation and should be prefixed with a <span class="title-ref">+</span>. See the section [Phone numbers](doc:verification-android-phone-numbers) for details.

## Validate code manually

To complete the verification of the phone number manually, the verification code should be passed to `verify` method. Depending on the method the code is:
1. For SMS verification - number received in the sms message.
2. For Flashcall - full phone number of the caller.
3. For Callout - digits spoken by text-to-speech software after answering the call.

Example:
```java
verification.verify(code);
```


The method `verify` may be invoked multiple times. For example, if the verification listener is invoked with an incorrect code exception, the application may hint to the user that the code was incorrect, let the user adjust it, and call `verify` again on the same verification instance.

## Early reject

If Sinch knows that verification is most likely to fail, an application can be configured to catch this condition and provide means to fallback fast to other verification methods. In this case the verification listener `onInitializationFailed` callback will be executed with an instance of `ApiCallException`. To enable this feature contact us at <dev@sinch.com>.

## Pass data to your backend

For each call to `Verification.initiate()`, the Sinch backend can perform a callback to the application backend to allow or disallow verification being initiated. By using the optional parameter `custom` on method configuration objects, any unique identifier can be passed from the application to the application backend. The data will be passed as a string. If there is a need for a more complex datatype, it needs to be stringified or encoded before being sent.
