---
title: Verification API
excerpt: >-
  Verify users with SMS, flash calls (missed calls) or a regular call. hThis
  document serves as a user guide and documentation on how to use the Sinch
  Verification REST APIs. Find out more information about our API today.
next:
  pages:
    - verification-rest-callback-api
    - verification-rest-call-detail-records
---
```text
URI:  https://verificationapi-v1.sinch.com/verification/[version]

    Version - v1
```

The Sinch Verification API is used for verify mobile phone numbers. It is consumed by the Sinch Verification SDK, but it can also be used by any backend or client directly.

```text
    [POST] /verifications
    [PUT] /verifications/{type}/{endpoint}
```

The Sinch service uses three different verification methods:

1.  By sending an SMS message with a PIN code
2.  By placing a flashcall (missed call) and detecting the incoming calling number (CLI)
3.  By placing a PSTN call to the user's phone and playing an announcement, asking the user to press a particular digit to verify the phone number (only iOS)

## Verification Request

**\[POST\] /verifications/** - Start a verification

This method is used by the mobile and web Verification SDKs to start a verification. It can also be used to request a verification from your backend, by making an [Application signed request](doc:using-rest#application-signed-request). It is a POST request which specifies the phone number that should be verified and the verification method.

### Authorization

Authorization level can be specified in the Sinch dashboard, under your app settings. By default it is set to "Deny". These schemes are allowed:

  - [Public authorization](doc:using-rest#public-resources)
  - [Application signed request](doc:using-rest#application-signed-request)
  - [Instance signed requests](doc:using-rest#instance-signed-request)

### Request

    [RequestBody]
        VerificationRequest

    [VerificationRequest]
        identity - identity
        string - method
        string? - reference
        string? - custom
        object? - metadata
        FlashCallOptions? - flashCallOptions
    [FlashCallOptions]
        int? - dialTimeout

**identity** indicates type of endpoint that will be verified and specifies the particular endpoint. The support endpoint type currently is only "number".

**method** indicates the verification method. Possible values are:

  - flashCall
  - sms
  - callout

**reference** can be used to pass your own reference in the request for tracking purposes.

**custom** can be used to pass custom data in the request.

**FlashCallOptions** is an optional object for flashCall verifications. It allows you to specify dial time out parameter for flashCall. DialTimeout should be specified in seconds and must be between 5 and 120. FlashCallOptions object can be specified optionally, and only if the verification request was triggered from your backend (no SDK client) through an [Application signed request](doc:using-rest#application-signed-request).

By default you do not need to specify what dial time out to use. Sinch will optimize dial time out for your flashCall.

*Example* - FlashCall

```text
[POST] https://verificationapi-v1.sinch.com/verification/v1/verifications
```

```json
{
    "identity": { "type":"number", "endpoint":"+46700000000" },
    "method": "flashCall"
}
```

*Example* - FlashCall specifying CLI (no SDK scenario)

```text
[POST] https://verificationapi-v1.sinch.com/verification/v1/verifications
```

```json
Request
{
    "identity": { "type":"number", "endpoint":"+46700000000" },
    "method": "flashCall",
    "flashCallOptions":{ "dialTimeout":10 }
}
```

*Example* - Sms

```text
[POST] https://verificationapi-v1.sinch.com/verification/v1/verifications
```

```json
{
    "identity": { "type":"number", "endpoint":"+46700000000" },
    "method": "sms"
}
```

*Example* - Callout

```text
[POST] https://verificationapi-v1.sinch.com/verification/v1/verifications
```

```json
{
    "identity": { "type":"number", "endpoint":"+46700000000" },
    "method": "callout"
}
```

### Response

    [RequestBody]
        VerificationResponse

    [VerificationResponse]
        string - id
        SmsVerificationData? - sms
        FlashCallVerificationData? - flashCall
        CalloutVerificationData? - callout

    [SmsVerificationData]
        string - template

    [FlashCallVerificationData]
        string - cliFilter
        int - interceptionTimeout

    [CalloutVerificationData]
        int - startPollingAfter
        int - stopPollingAfter
        int - pollingInterval

The response from the Sinch Verification service differs, depending on the verification method.

In case of a flashcall request, the response contains the following parameters:

**cliFilter** is the filter that should be applied for incoming calls to intercept the flashcall.

**interceptionTimeout** indicates the amount of seconds that the SDK client will be waiting for the flashcall.

*Example* - FlashCall

```json
{
    "id":"1234567876",
    "flashCall":{"cliFilter":"(.*)70123(.*)","interceptionTimeout":120}
}
```

In case of an SMS verification, the response contains the **template** of the SMS to be expected and intercepted.

*Example* - Sms

```json
{
    "id": "1234567890",
    "sms": { "template": "Your verfication code is {{CODE}}" }
}
```

In case of a callout verification (voice call), the response contains information for the client to poll for the verification result.

*Example* - Callout

```json
{
    "id": "1234567890",
    "callout":{
        "startPollingAfter":3,
        "stopPollingAfter":30,
        "pollingInterval":3
        }
}
```

## Report Verification

**\[PUT\] /verifications/{type}/{endpoint}** - Report back on a started verification

After the SMS or flashcall is received (and intercepted, in case of Android), the client needs to report to the Sinch Verification service either the PIN (for SMS) or CLI (for flashCall).

### Authorization

Authorization level can be specified in the Sinch dashboard, under your app settings. By default it is set to "Deny". If you are using this API to report a verification from your backend, you should use an [Application signed request](doc:using-rest#application-signed-request).

### Request

    [RequestBody]
        VerficationReportRequest

    [VerficationReportRequest]
        string - method
        SmsVerificationReportData? - sms
        FlashCallVerificationReportData? - flashCall

    [SmsVerificationReportData]
        string - code
        string? - cli

    [FlashCallVerificationReportData]
        string - cli

**method** indicates the verification method. Possible values are:

  - flashCall
  - sms

**code** indicates the PIN code that was inputted by the user in the app

**cli** indicates the caller Id of the flashCall or the sender Id of the SMS.

*Example* - FlashCall

```text
[PUT] https://verificationapi-v1.sinch.com/verification/v1/verifications/number/+46700000000
```

```json
{
    "method": "flashCall",
    "flashCall": { "cli": "+46000000000" }
}
```

*Example* - SMS

```text
[PUT] https://verificationapi-v1.sinch.com/verification/v1/verifications/number/+46700000000
```

```json
{
    "method": "sms",
    "sms": { "code": "123" }
}
```

The Sinch Verification backend will then validate the reported PIN or CLI and respond with success or failure.

### Response

    [ResponseBody]
        VerificationResult

    [VerificationResult]
        string - id
        string - method
        string - status
        string? - reason
        string? - reference
        bool? - source

**id** is the verification id.

**method** shows the verification method. It can take the values:

  - "flashcall"
  - "sms"
  - "callout"

**status** shows the current status of the verification request. It can take the values:

  - "PENDING": the verification is ongoing
  - "SUCCESSFUL": the verification was successful
  - "FAIL": the verification attempt was made, but the number was not verified
  - "DENIED": the verification attempt was denied by Sinch or your backend
  - "ABORTED": the verification attempt was aborted by requesting a new verification
  - "ERROR": the verification could not be completed due to a network error or the number being unreachable.

**reason** shows the reason why a verification has FAILED, was DENIED or was ABORTED. It can take the values:

  - “Fraud”
  - "Not enough credit"
  - "Blocked"
  - "Denied by callback"
  - "Invalid callback"
  - "Internal error"
  - "Destination denied"
  - "Network error or number unreachable"
  - "Failed pending"
  - "SMS delivery failure"
  - "Invalid CLI"
  - "Invalid code"
  - "Expired"
  - "Hung up without entering valid code"

**reference** shows the reference Id that was passed (optionally) together with the verification request.

**source** This is free text that the client is sending, but it is used to show if the call/SMS was intercepted or not. Typical values can be:

  - “intercepted”
  - "manual"

*Example* - Response

```json
{
    "id": "1234567890",
    "method": "sms",
    "status": "SUCCESSFUL",
    "source": "intercept"
}
```

## Query by ID

**\[GET\] /verifications/id/{id}** - Queries the verification result by sending the verification id

With this query you can get the result of a verification.

### Authorization

This is a protected resource and requires an [Application signed request](doc:using-rest#application-signed-request).

### Request

    [GET] /verifications/id/{id}

### Response

    [ResponseBody]
        VerificationResult

    [VerificationResult]
        string - id
        string - method
        string - status
        string? - reason
        string? - reference
        string? - source

**id** is the verification id.

**method** shows the verification method. It can take the values:

>   - "flashcall"
>   - "sms"
>   - "callout"

**status** shows the current status of the verification request. It can take the values:

>   - "PENDING": the verification is ongoing
>   - "SUCCESSFUL": the verification was successful
>   - "FAIL": the verification attempt was made, but the number was not verified
>   - "DENIED": the verification attempt was denied by Sinch or your backend
>   - "ABORTED": the verification attempt was aborted by requesting a new verification
>   - "ERROR": the verification could not be completed due to a network error or the number being unreachable.

**reason** shows the reason why a verification has FAILED, was DENIED or was ABORTED. It can take the values:

>   - “Fraud”
>   - "Not enough credit"
>   - "Blocked"
>   - "Denied by callback"
>   - "Invalid callback"
>   - "Internal error"
>   - "Destination denied"
>   - "Network error or number unreachable"
>   - "Failed pending"
>   - "SMS delivery failure"
>   - "Invalid CLI"
>   - "Invalid code"
>   - "Expired"
>   - "Hung up without entering valid code"

**reference** shows the reference Id that was passed (optionally) together with the verification request.

**source** This is free text that the client is sending, but it is used to show if the call/SMS was intercepted or not. Typical values can be:

>   - “intercepted”
>   - "manual"

*Example* - Query by Id

#### Request

    [GET] https://verificationapi-v1.sinch.com/verification/v1/verifications/id/1234567890

#### Response

```json
{
    "id": "1234567890",
    "method": "sms",
    "status": "SUCCESSFUL",
    "source": "intercept"
}
```

## Query Verification by Reference

**\[GET\] /verifications/reference/{reference}** - Queries the verification result by sending the custom reference

With this query you can get the result of a verification by sending the reference.

### Authorization

This is a protected resource and requires an [application signed request](doc:using-rest#application-signed-request).

### Request

    [GET] /verifications/reference/{reference}

### Response

    [ResponseBody]
        VerificationResult

    [VerificationResult]
        string - id
        string - method
        string - status
        string? - reason
        string? - reference
        string? - source

**id** is the verification id.

**method** shows the verification method. It can take the values:

>   - "flashcall"
>   - "sms"
>   - "callout"

**status** shows the current status of the verification request. It can take the values:

>   - "PENDING": the verification is ongoing
>   - "SUCCESSFUL": the verification was successful
>   - "FAIL": the verification attempt was made, but the number was not verified
>   - "DENIED": the verification attempt was denied by Sinch or your backend
>   - "ABORTED": the verification attempt was aborted by requesting a new verification
>   - "ERROR": the verification could not be completed due to a network error or the number being unreachable.

**reason** shows the reason why a verification has FAILED, was DENIED or was ABORTED. It can take the values:

>   - “Fraud”
>   - "Not enough credit"
>   - "Blocked"
>   - "Denied by callback"
>   - "Invalid callback"
>   - "Internal error"
>   - "Destination denied"
>   - "Network error or number unreachable"
>   - "Failed pending"
>   - "SMS delivery failure"
>   - "Invalid CLI"
>   - "Invalid code"
>   - "Expired"
>   - "Hung up without entering valid code"

**reference** shows the reference Id that was passed (optionally) together with the verification request.

**source** This is free text that the client is sending, but it is used to show if the call/SMS was intercepted or not. Typical values can be:

>   - “intercepted”
>   - "manual"

*Example* - Query by reference

#### Request

    [GET] https://verificationapi-v1.sinch.com/verification/v1/verifications/reference/abc_123

#### Response
```json
{
    "id": "1234567123",
    "method": "sms",
    "status": "SUCCESSFUL",
    "source": "manual",
    "reference": "abc_123"
}
```
## Query by Endpoint

**\[GET\] /verifications/{method}/{type}/{endpoint}** - Queries the verification result by sending the number to be verified.

### Authorization

The following endpoint is [public authorized](doc:using-rest#public-resources), so there is no need to use any signing for this request. This is so that the client can poll for the status of their callout verification without first being authorized.

### Request

    [GET] /verifications/{method}/{type}/{endpoint}

### Response

    [ResponseBody]
        VerificationResult

    [VerificationResult]
        string - id
        string - method
        string - status
        string? - reason
        string? - reference
        string? - source

**id** is the verification id.

**method** shows the verification method. It can take the values:

>   - "flashcall"
>   - "sms"
>   - "callout"

**status** shows the current status of the verification request. It can take the values:

>   - "PENDING": the verification is ongoing
>   - "SUCCESSFUL": the verification was successful
>   - "FAIL": the verification attempt was made, but the number was not verified
>   - "DENIED": the verification attempt was denied by Sinch or your backend
>   - "ABORTED": the verification attempt was aborted by requesting a new verification
>   - "ERROR": the verification could not be completed due to a network error or the number being unreachable.

**reason** shows the reason why a verification has FAILED, was DENIED or was ABORTED. It can take the values:

>   - “Fraud”
>   - "Not enough credit"
>   - "Blocked"
>   - "Denied by callback"
>   - "Invalid callback"
>   - "Internal error"
>   - "Destination denied"
>   - "Network error or number unreachable"
>   - "Failed pending"
>   - "SMS delivery failure"
>   - "Invalid CLI"
>   - "Invalid code"
>   - "Expired"
>   - "Hung up without entering valid code"

**reference** shows the reference Id that was passed (optionally) together with the verification request.

**source** This is free text that the client is sending, but it is used to show if the call/SMS was intercepted or not. Typical values can be:

>   - “intercepted”
>   - "manual"

*Example* - Query by endpoint

#### Request

    [GET] https://verificationapi-v1.sinch.com/verification/v1/verifications/callout/number/+46700000000

#### Response

```json
{
    "id": "1234567890",
    "method": "callout",
    "status": "SUCCESSFUL",
    "reference": "abc_123"
}
```

---
**Important**

This endpoint can only be used for a limited time after the verification request.

---

## Set SMS language

It is possible to specify the content language for SMS verification. The desired language is set in the initiate verification request 'Accept-Language' header. Setting this header is optional. If not included the application's default SMS content language is used, which by default is en-US. If an SMS template for the desired language is not found it is defaulted to 'en-US' unless a different default language has been defined for the requesting application.
 More information on the 'Accept-Language' header can be found from [IETF](https://tools.ietf.org/html/rfc3282) and the [HTTP/1.1 documentation](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4).

---
**Note**

The content language specified in the API request or in the callback can be overridden by carrier provider specific templates, due to complience and legal requirements, such as [US shortcode requirements (pdf)](https://www.wmcglobal.com/storage/us_resources/ctia-short-code-monitoring-handbook-current-Short-Code-Monitoring-Handbook-v1.7.pdf).

---

*Example* - SMS verification specifying content language

    Headers:
    ...
    Accept-Language: es-ES
    ...

    [POST] https://verificationapi-v1.sinch.com/verification/v1/verifications

```json    
{
    "identity": { "type":"number", "endpoint":"+46700000001" },
    "method": "sms"
}
```

The actual language that was selected is returned in the 'Content-Language' HTTP content header. As outlined above, this may differ from the requested language due to either that language not having SMS content defined, or it being overridden by provider requirements.

*Example* - SMS verification response with selected content language

#### Response

    Headers:
    ...
    Content-Length: 103
    Content-Language: es-ES
    Content-Type: application/json; charset=utf-8
    Expires: -1
    ...

```json
{
    "id":"1087388",
    "sms":
        {
            "template":"Tu código de verificación es {{CODE}}.",
            "interceptionTimeout":120
        }
}
```
