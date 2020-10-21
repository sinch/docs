---
title: The migration process
excerpt: >-
  The migration process
---

In order to migrate from version 1 to 2.\*.\* of the SDK one needs to change how Verification objects are created:

```java
val verification =
  SinchVerification.createSmsVerification(config, phoneNumberInE164, listener)

//to

val verification = SmsVerificationMethod.Builder()
  .config(/* Method config instance */)
  .initiationListener(/* Initiation listener instance */)
  .verificationListener(/* Verification listener instance */)
  .build()
```
See [The verification process](doc:verification-android-the-verification-process) section for more details.

The process is a little more complex then simply copy-paste as the SDK architecture has changed. The main differences are as follows:

1. Each verification is shipped in different module, allowing your app to declare permissions required only by the verification methods that are actually used in your code.
2. There are no `createVerification` methods of global SDK object. Each method has its own builder instance following [Fluent Builder Pattern](https://dzone.com/articles/fluent-builder-pattern). There is also a `verification-all` module that can be used to simplify creation of different verification method objects with similar configuration parameters.
3. `Config` object concept passed to each verification has been splitted to GlobalConfiguration (one for entire SDK) and method specific configuration instances.
4. Single `VerificationListener` holding callbacks connected with entire verification flow was divided into method specific `InitiationListener` notifying about the verification request result and the `VerificationListener` which is now responsible only for callbacks connected with second phase of verification (validating the code). Passing both listeners to builder is optional (however recommended).
5. Some helper utility classes such as `PhoneNumberFormattingTextWatcher` were removed as starting from API level 21, there are methods with exact same functionality already built-in Android SDK.
