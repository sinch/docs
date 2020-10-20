---
title: Using verification-all module for configuring multiple methods at once.
excerpt: Using common verification configuration parameters for different verification methods.
next:
  pages:
    - verification-android-phone-numbers
---

If your application allows the user to authenticate via various verification methods it might be more readable to initiate the verification using `VerificationInitData` and `CommonVerificationInitializationParameters`.

## VerificationInitData

`VerificationInitData` is simply a structure that holds all common parameters that can be passed to a verification configuration object and additionally method used to verify the phone number.

```kotlin
private val initData: VerificationInitData
    get() =
        VerificationInitData(
            usedMethod = buttonToMethodMap[methodToggle.checkedButtonId],
            number = phoneInput.editText?.text.toString(),
            custom = customInput.editText?.text.toString(),
            reference = referenceInput.editText?.text.toString(),
            honourEarlyReject = honoursEarlyCheckbox.isChecked,
            acceptedLanguages = acceptedLanguagesInput?.editText?.text.toString().toLocaleList()
        )
```

> **NOTE**
>
> In case of Android the only method specific parameter for now is the application hash needed by the sms method. Methods specific builders have been implemented mostly for future development purposes and for usage with single verification methods.

## CommonVerificationInitializationParameters

Contrary to `VerificationInitData` that holds parameters of configuration objects `CommonVerificationInitializationParameters` collects `Verification` instance properties and is designed to work together with `VerificationInitData`:

```kotlin
val verification = BasicVerificationMethodBuilder.createVerification(
            commonVerificationInitializationParameters = CommonVerificationInitializationParameters(
                globalConfig = app.globalConfig,
                verificationInitData = initData,
                initiationListener = initListener,
                verificationListener = this
            ),
            ///...
```

## Sample

Download the [sample application](https://github.com/sinch/verification-samples/tree/master/Android-Verification-SDK) and decide which `Verification` object creation suits your needs best. The _Verification Sample_ project uses the verification-all module and _VerificationSmsSample_ uses method specific builder to create sms verification instances when no other methods are needed.
