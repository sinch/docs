---
title: Improving verification performance with a great UI
excerpt: >-
  Improve verification performance with great UI. Here is a UI that will capture
  correct input from the user.
next:
  pages:
    - verification-ios-commonmodule
---
For better verification performance, it should be straightforward and easy for users to enter their phone numbers. To simplify this step and build a UI that accurately captures the correct input from the users, the Sinch SDK provides some utility APIs.

## PhoneNumberUITextField

Sinch SDK provides special class `PhoneNumberUITextField` that can replace default UITextField for phone number input. It automatically formats partially entered phone number based on `countryIso` property. It also allows you to get phone number user has typed in E164 representation that can be directly passed to `Verification` object.
Example usage:

```swift
  let textField = PhoneNumberUITextField()
  textField.countryIso = "US" //Uses SinchPhoneNumberUtils.defaultCountryIso by default
  //Get E164 phone representation
  let e164PhoneNumber = textField.e164Number
```

## SinchPhoneNumberUtils

If you prefer to use built in UITextField or your custom text field implementation `SinchPhoneNumberUtils` class might be useful for phone number checking and validation purposes. Methods defined there allow you to convert local phone numbers to E164 format (`formatNumberToE164`) or check if phone number is valid for given country code (`isPossiblePhoneNumber`).
