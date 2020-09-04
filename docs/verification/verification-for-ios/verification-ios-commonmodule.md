---
title: Using common verification configuration for multiple methods
excerpt: Using same verification parameters (number, reference etc.) for different verification methods.
next:
  pages:
    - verification-ios-miscellaneous
---

If your application allows the user to authenticate via various verification methods it might be more readable to initiate the verification using `VerificationInitData` and `CommonVerificationInitializationParameters`.

## VerificationInitData

`VerificationInitData` is simply a structure that holds all the parameters that can be passed to a verification configuration object and additionally method used to verify the phone number.

```swift
private var initData: VerificationInitData {
    return VerificationInitData(
        usedMethod: buttonToMethodMap[selectedMethodButton] ?? .sms,
        number: phoneNumberTextField.e164Number ?? "",
        custom: customField.text?.nilIfEmpty(),
        reference: referenceField.text?.nilIfEmpty(),
        honoursEarlyReject: honoursEarlyRejectField.isOn,
        acceptedLanguages: (try? acceptedLanguagesField.text?.nilIfEmpty()?.toLocaleList()) ?? [])
}
```

> **NOTE**    
>
> In case of iOS there are actually no method specific parameters but the method specific builders have been implemented for future development purposes and same SDK design as in Android.


## CommonVerificationInitializationParameters

Contrary to `VerificationInitData` that holds parameters of configuration objects `CommonVerificationInitializationParameters` collects `Verification` instance properties and is designed to work together with `VerificationInitData`:

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
