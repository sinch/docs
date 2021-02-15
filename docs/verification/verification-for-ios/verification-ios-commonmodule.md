---
title: Using common verification configuration for multiple methods
excerpt: Using verification parameters (number, reference etc.) for different verification methods.
next:
  pages:
    - verification-ios-miscellaneous
---

If your application allows the user to authenticate via various verification methods, it might be more readable to initiate the verification using `VerificationInitData` and `CommonVerificationInitializationParameters`.

## VerificationInitData

`VerificationInitData` is a structure that holds all the parameters that can be passed to a verification configuration object and additionally is a method used to verify the phone number.

```swift
private var initData: VerificationInitData {
    return VerificationInitData(
        usedMethod: VerificationMethodType.sms,
        number: "+123456789",
        custom: "ExampleCustom",
        reference: nil,
        honoursEarlyReject: true,
        acceptedLanguages: [])
}
```

> **NOTE:** In case of iOS there are no method specific parameters but the method specific builders have been implemented for future development purposes and same SDK design as in Android.


## CommonVerificationInitializationParameters

Contrary to `VerificationInitData` that holds parameters of configuration objects, `CommonVerificationInitializationParameters` collects `Verification` instance properties and is designed to work together with `VerificationInitData`:

```swift
private var commonConfig: CommonVerificationInitializationParameters {
    return CommonVerificationInitializationParameters(
        globalConfig: self.globalConfig,
        verificationInitData: self.initData,
        initalizationListener: self,
        verificationListener: self
    )
}
```

## Sample

Download the [sample application](https://github.com/sinch/verification-samples/tree/master/iOS-Verification-SDK) and decide which `Verification` object creation suits your needs best.
