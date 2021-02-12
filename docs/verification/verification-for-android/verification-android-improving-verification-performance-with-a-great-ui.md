---
title: Improving verification performance with a great UI
excerpt: >-

next:
  pages:
    - verification-android-miscellaneous
---
For better verification performance, it should be straightforward and easy for users to enter their phone phone numbers. To simplify this step and build a UI that accurately captures the correct input from the users, both Android (starting from API level 21) and Sinch SDK provide some utility APIs.

## PhoneNumberFormattingTextWatcher

The Android SDK provides a [PhoneNumberFormattingTextWatcher](http://developer.android.com/reference/android/telephony/PhoneNumberFormattingTextWatcher.html). This watcher can be added as a text changed listener to a text field, and will format it automatically if a phone number is entered in local or international format.

## SinchPhoneNumberUtils

Another convenient helper utility class available in Sinch SDK is `SinchPhoneNumberUtils`. The function `isPossiblePhoneNumber` is especially useful to check phone numbers that are typed by the user that seem to be correct. If it's not, the text field could be highlighted with a different color (red).

> **WARNING: Important**    
>
> `isPossiblePhoneNumber` internally uses Android SDK and its [Telephony](https://developer.android.com/reference/android/telephony/package-summary) API. In some rare cases, it has been reported that for some numbers it acted as if the numbers were valid  and the were treated as incorrect. For that reason, you should use these APIs only as a hint and **do not** disable the UI completely even if that function returns _false_.
