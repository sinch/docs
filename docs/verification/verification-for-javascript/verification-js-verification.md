---
title: Verify phone numbers and users with JavaScript
excerpt: >-
  Learn how to request SMS verification and how to verify a user phone number.
next:
  pages:
    - verification-js-miscellaneous
---
The Sinch JavaScript SDK supports verification of phone numbers via SMS and Callout. Flash calling verification will be supported in a later release.

## SMS Verification

Verification of a phone number is performed in two steps, a verification SMS is requested and a verification code for that particular verification session is sent to the recipient. It's the responsibility of the developer to ask the end-user to provide the verification code from the SMS.

When the verification code is known, the `verify(code, success, fail)` method is used to verify the number.

When the supplied code is correct, the phone number is considered verified and you will receive this information in the response. If you have configured a callback URL for verification, you will also receive confirmation through a callback to your backend.

### Request SMS verification

To initiate an SMS verification, start by creating an SMS verification session. This is done by calling the `createSmsVerification(phoneNumber)` method on your `sinchClient`. This method returns a verification object which can be used to send the verification SMS, and re-send the SMS, per the example below.

```javascript
var sinchClient = new SinchClient({applicationKey: YOUR_APPLICATION_KEY})

var verification = sinchClient.createSmsVerification(PHONE_NUMBER)

verification.initiate(success, fail);
```

The call to `initiate(success, fail)` triggers sending a verification SMS. This method can be called multiple times, in case another SMS should be sent. Callbacks for success and failure should be supplied to inform the user whether the action succeeded. However, delivery is not guaranteed even if success is called, which is why it is recommended to provide the end-user a user interface to re-send the verification SMS.

### Verify SMS code

To verify the phone number, the resulting code from the SMS should be verified using the `verify()` method. This can be done in the following way:

```javascript
var code = codeFromUser;

verification.verify(code, success, fail);
```

The verify method takes two callbacks (beyond the pin-code), which can be used to inform the user of any issues or of success in verifying the phone number. However, important actions should be performed server-side using the callback on successful verification of a phone number.

### Act on SMS callbacks

As shown earlier, both `initiate` and `verify` take two callbacks as arguments for successful result or failure. These callbacks should be used to progress correctly through the flow:

  - UI to enter phone number
      - Success: Progress to enter verification code.
      - Fail: UI to inform user of a problem and/or ask the user to try
        again.
  - UI to enter verification code (or re-send SMS)
      - Success: Confirmation of successful verification.
      - Fail: UI to inform user of verification problem.

### SMS Template

By default, the SMS template used for the Sinch verification SMS has a fixed format. Contact us at <dev@sinch.com> to update its content when your app is ready for production.

## Callout Verification

Verification of a phone number is performed in one step: a PSTN call to the end-user phone is placed and a text-to-speech or recorded voice will instruct the end-user to press a digit.

To initiate a callout verification, start by creating a callout verification session. This is done by calling the `createCalloutVerification(phoneNumber)` method on your `sinchClient`. This method returns a verification object which can be used to initiate the call and act on outcome using callbacks, per the example below.

```javascript
var sinchClient = new SinchClient({applicationKey: YOUR_APPLICATION_KEY})

var verification = sinchClient.createCalloutVerification(PHONE_NUMBER)

verification.initiate(success, fail);
```

The call to `initiate(success, fail)` triggers a PSTN call. This method can be called multiple times. Callbacks for success and failure should be supplied to inform the user whether the verification succeeded.

## Pass data to your backend

For each call to `verification.initiate()`, the Sinch backend can perform a callback to the application backend to allow or disallow an SMS or flashcall being initiated. By using the optional parameter `custom` on `SinchVerification.createFlashCallVerification` and `createSmsVerification`, any unique identifier can be passed from the application to the application backend. The data is passed as a string. If there is a need for a more complex datatype, it needs to be stringified or encoded before being sent.

## Promises

As an alternative to callbacks, the verification session object also supports promises. The execution order is for callbacks to be called first, followed by promises. When using promises, the full flow may look like this:

```javascript
var sinchClient = new SinchClient({applicationKey: YOUR_APPLICATION_KEY})

var verification = sinchClient.createSmsVerification(PHONE_NUMBER)

verification.initiate().then(function(successInfo) {
    // Act on success
    // Display "enter pin" UI
}).fail(function(errorInfo) {
    // Act on error
});

//PIN is retrieved from user
var code = codeFromUser;

verification.verify(code).then(function(successInfo) {
    // Act on success (valid number)
}).fail(function(errorInfo) {
    // Act on error and inform the user / retry
});
```
