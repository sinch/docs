---
title: Callback API
excerpt: How to use the callbacks in the Sinch Verification REST SDK.
next:
  pages:
    - verification-rest-verification-api
    - verification-rest-call-detail-records
---
## Verification Request Event

This callback event is a POST request to the specified verification callback URL and is triggered when a new verification request is made from the SDK client or the Verification Request API. This callback event is only triggered when a verification callback URL is specified.

### Authorization

You can find more information on callback request signing [here](doc:using-rest#callback-request-signing).

### Request

    [RequestBody]
    {
        string - id
        string - event
        string - method
        identity - identity
        money - price
        string? - reference
        string? - custom
        string[]? - acceptLanguage
    }

*Example*

```json
{
    "id":"1234567890",
    "event": "VerificationRequestEvent",
    "method": "sms",
    "identity": { "type":"number", "endpoint":"+46700000000" },
    "price": { "amount": 10.5, "currencyId":"USD" }
}
```

### Response

    [ResponseBody]
        RequestEventResponse

    [RequestEventResponse]
    {
        string - action
        Sms? - sms
        FlashCall? - flashCall
        Callout? - callout
    }

    [Sms]
        string - code
        string[]? - acceptLanguage

    [FlashCall]
        string? - cli
        int? - dialTimeout

    [Callout]
        string? - locale
        string? - ttsMenu
        string? - wavFile
        string? - pin

**action** allows or denies the verification to be executed. It can take these values:

  - allow
  - deny

*Example* - allow

```json
{
    "action": "allow"
}
```

*Example* - deny

```json
{
    "action": "deny"
}
```

### SMS

**code** contains the SMS PIN that should be used. By default, the Sinch dashboard will automatically generate PIN codes for SMS verification. If you want to set your own PIN, you can specify it in the response to the Verification Request Event:

*Example* - SMS with pre-defined PIN

```json
{
    "action": "allow",
    "sms": { "code": "12345" }
}
```

**acceptLanguage** allows you to set or override if provided in the API request, the SMS verification content language.

**NOTE:** The content language specified in the API request or in the callback can be overridden by carrier provider specific templates, due to compliance and legal requirements, such as [US shortcode requirements (pdf)](https://www.wmcglobal.com/storage/us_resources/ctia-short-code-monitoring-handbook-current-Short-Code-Monitoring-Handbook-v1.7.pdf).

*Example* - Set the SMS language to Spanish (Spain)

```json
{
    "action": "allow",
    "sms":
        {
            "code": "12345" ,
            "acceptLanguage": ["es-ES"]
        }
}
```

### Flashcall

**cli** shows the phone number that will be displayed to the user when the flashcall is received on the user's phone. By default, the Sinch dashboard will select randomly the CLI that will be displayed during a flashcall from a pool of numbers. If you want to set your own CLI, you can specify it in the response to the Verification Request Event:

*Example* - Flashcall with pre-defined CLI

```json
{
    "action": "allow",
    "flashCall": { "cli": "+4445874587" }
}
```

**NOTE:** In order to set your own CLI, you need to **own** the number that you will set.

**dialTimeout** shows the maximum time that a flashcall verification will be active and can be completed. If the phone number has not been verified successfully during this time, then the verification request will fail. By default, the Sinch dashboard will automatically optimize dial time out during a flashcall. If you want to set your own dial time out for the flashcall, you can specify it in the response to the Verification Request Event:

*Example* - Flashcall with pre-defined dial timeout

```json
{
    "action": "allow",
    "flashCall": { "dialTimeout": 10 }
}
```

### Callout

**locale** it is used to indicate the language that should be used for the text-to-speech message. Currently, only "en-US" is supported.

**ttsMenu** is the message that can be played to the user when the phone call is picked up. The default value is: "To verify your phone number, please press {pin}. If you didn’t request this call, please hang up."

**wavFile** is the .wav file that can be played to the user when the phone call is picked up.

**pin** is the digit that the user should press to verify the phone number. The default value is "1".

**Important:** For the callout verification, if no ttsMenu nor wavFile is specified, then the user hears a text-to-speech message saying: "To verify your phone number, please press {pin}. If you didn’t request this call, please hang up."

**NOTE:** For the callout method, you can either select to play a text-to-speech message, or a pre-recorded .wav file. If you want to use the .wav file, please contact Sinch support for instructions on how to supply the file to Sinch.

*Example* - callout

```json
{
    "action": "allow",
    "callout": {
        "locale": "en-US",
         "ttsMenu": "Please press 1 to verify your phone",
         "pin": "1",
         "timeout": 60
    }
}
```

## Verification Result Event

This callback event is a POST request to the specified verification callback URL and triggered when a verification has been completed and the result is known. It is used to report the verification result to the developer's backend application. This callback event is only triggered when the verification callback URL is specified.

### Authorization

You can find more information on callback request signing here
<callbackrequestsigning>`.

### Request

    [RequestBody]
        ResultEvent

    [ResultEvent]
    {
        string - id
        string - event
        string - method
        identity - identity
        string - status
        string? - reason
        string? - reference
        string? - source
        string? - custom
    }

**id** is the verification id.

**event** contains the callback event that was received. It has the value "VerificationResultEvent".

**method** shows the verification method. It can take the values:

>   - "flashcall"
>   - "sms"
>   - "callout"
>   - "seamless"

**identity** contains the endpoint information that is being verified. It contains a "type" object and an "endpoint" object

```json
"identity": { "type":"number", "endpoint":"+46700000000" }
```

**status** shows the current status of the verification request. It can take the values:

>   - "PENDING": the verification is ongoing.
>   - "SUCCESSFUL": the verification was successful.
>   - "FAIL": the verification attempt was made, but the number was not verified.
>   - "DENIED": the verification attempt was denied by Sinch or your backend.
>   - "ABORTED": the verification attempt was aborted by requesting a new verification.
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

**source** free text that the client is sending, but it is used to show if the call/SMS was intercepted or not. Typical values can be:

>   - “intercepted”
>   - "manual"

**custom** displays a custom string that can be passed provided during a verification request.

*Example*

```json
{
    "id":"1234567890",
    "event": "VerificationResultEvent",
    "method": "sms",
    "identity": { "type":"number", "endpoint":"+46700000000" },
    "status": "SUCCESSFUL"
}
```

### Response

*None*
