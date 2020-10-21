---
title: Calling API
excerpt: >-
  The calling API exposes calling-related functionality in the Sinch Platform.
  Read more.
next:
  pages:
    - voice-rest-api-callback-api
---

### Methods

#### Configuration Management Methods

    [GET]       /configuration/numbers/
    [POST]      /configuration/numbers/
    [DELETE]    /configuration/numbers/
    [GET]       /configuration/callbacks/applications/{applicationkey}
    [POST]      /configuration/callbacks/applications/{applicationkey}
    [GET]       /calling/query/number/{number}

#### Traffic Management Methods

    [PATCH]     /calls/id/{callId}
    [GET]       /calls/id/{callId}
    [GET]       /conferences/id/{conferenceId}
    [PATCH]     /conferences/id/{conferenceId}/{callId}
    [DELETE]    /conferences/id/{conferenceId}
    [DELETE]    /conferences/id/{conferenceId}/{callId}
    [POST]      /callouts
    [GET]       /calls/id/{callId}
    [PATCH]     /calls/id/{callId}
    [GET]       /recording?from=timestamp&to=timestamp&page=int&pageSize=int
    [GET]       /recording/{key}
    [DELETE]    /recording/{key}

### Errors

    [Error Codes]
        40001 - Illegal parameters
        40002 - Missing parameter
        40003 - Invalid request
        40301 - Invalid authorization scheme for calling the method
        40108 - Invalid credentials
        40400 - Unable to get user
        50000 - Internal error

### Get Numbers

**[GET] /configuration/numbers/**

Get information about your numbers. It returns a list of numbers that you own, as well as their capability (voice or SMS). For the ones that are assigned to an app, it returns the application key of the app.

_Example_

    [GET] https://callingapi.sinch.com/v1/configuration/numbers/

### Authorization

This is a protected resource and requires an application signed request \<applicationsignedrequest\> or basic auth \<basicauthorization\>.

### Response

    [ResponseBody]
        NumberItem[] numbers

    [NumberItem]
        string - number
        string - applicationkey
        string - capability

**number** The phone number or list of numbers that you own in E.164 format.

**applicationkey** indicates the application where the number(s) are assigned. If the number is not assigned to an app, no application key is returned.

**capability** the capability of the number. It can take these values:

> - voice
> - sms

_Example_

    {
      "numbers": [
        {
          "number": "+19160001112222",
          "capability": "voice"
        },
        {
          "number": "+14151112223333",
          "applicationkey": "11983f76-12c8-1111-9515-4785c7b49ca8",
          "capability": "voice"
        }
      ]
    }

### Errors

    50000 - Internal error

## Update Numbers

**[POST] /configuration/numbers/**

Assign a number or a list of numbers to an application.

### Request

    [RequestBody]
        string[] - numbers
        string - applicationkey
        string - capability

**number** The phone number or list of numbers in E.164 format.

**applicationkey** indicates the application where the number(s) will be assigned. If empty, the application key that is used to sign the request will be used.

**capability** indicates the DID capability that needs to be assigned to the chosen application. Valid values are “voice” and “sms”. Please note that the DID needs to support the selected capability.

_Example_

    [POST] https://callingapi.sinch.com/v1/configuration/numbers/

    {
        "numbers":["+14151112223333"],
        "applicationkey":"11983f76-12c8-1111-9515-4785c7b67ca8"
    }

### Authorization

This is a protected resource and requires an application signedrequest \<applicationsignedrequest\> or basic auth \<basicauthorization\>.

### Response

    [ResponseBody]
        Empty

### Errors

    50000 - Unable to update number
    40001 - Unable to update numbers
    40003 - Invalid Application Key

## Un-assign Number

**[DELETE] /configuration/numbers/**

Un-assign a number from an application.

### Request

    [RequestBody]
        string - number
        string - applicationKey
        string - capability

**number** The phone number in E.164 format.

**applicationKey** indicates the application from which the number will be un-assigned. If empty, the application key that is used to sign the request will be used.

**capability** indicates the DID capability. Valid values are “voice” and “sms”. Please note that the DID needs to support the selected capability.

_Example_

    [DELETE] https://callingapi.sinch.com/v1/configuration/numbers/

    {
        "number":"+14151112223333",
        "applicationKey":"11983f76-12c8-1111-9515-4785c7b67ca8",
        "capability": "voice"
    }

### Authorization

This is a protected resource and requires an application signed request \<applicationsignedrequest\> or basic auth \<basicauthorization\>.

### Response

    [ResponseBody]
        Empty

### Errors

    50000 - Unable to update number
    40001 - Unable to update numbers
    40003 - Invalid Application Key

## Get Callbacks

**[GET] /configuration/callbacks/applications/{applicationkey}**

Get callback URLs.

### Request

_Example_

    [GET] https://callingapi.sinch.com/v1/configuration/callbacks/applications/94983f76-1161-6655-9515-4785c7b67dd8

### Authorization

This is a protected resource and requires an application signed request \<applicationsignedrequest\> or basic auth \<basicauthorization\>.

### Response

    [ResponseBody]
        UrlItem url

    [UrlItem]
        string - primary
        string - fallback

**primary** is your primary callback URL.

**fallback** is your fallback callback URL. It is used only if Sinch platform gets a timeout or error from your primary callback URL.

_Example_

    {
        "url" : {
                    "primary" : "http://primary.com",
                    "fallback" : "http://fallback.com"
                }
    }

### Errors

    50000 - Unable to get configuration
    40003 - Invalid Application Key

## Update Callbacks

**[POST] /configuration/callbacks/applications/{applicationkey}**

Update callback URLs.

### Request

    [RequestBody]
        UrlItem url

    [UrlItem]
        string - primary
        string - fallback

}

**primary** is your primary callback URL.

**fallback** is your fallback callback URL. It is used only if Sinch platform gets a timeout or error from your primary callback URL.

_Example_

    [POST] https://callingapi.sinch.com/v1/configuration/callbacks/applications/94983f76-1161-6655-9515-4785c7b67ca8

    {
        "url" : {
                    "primary" : "http://primary.com",
                    "fallback" : "http://fallback.com"
                }
    }

### Authorization

This is a protected resource and requires an application signed request \<applicationsignedrequest\> or basic auth \<basicauthorization\>.

### Response

    [ResponseBody]
        Empty

### Errors

    50000 - Unable to update configuration
    40003 - Invalid Application Key

## Query Number

**[GET] /calling/query/number/{number}**

Get information about the requested number.

**number** The phone number you want to query.

_Example_

    [GET] https://callingapi.sinch.com/v1/calling/query/number/+46(0)73-017-0101

### Authorization

This is a protected resource and requires an application signed request \<applicationsignedrequest\> or basic auth \<basicauthorization\>.

### Response

    [ResponseBody]
        QueryNumberResponse

    [QueryNumberResponse]
        NumberItem - number

    [NumberItem]
        string - countryId
        string - numberType
        string - normalizedNumber
        bool - restricted
        Money - rate

**countryId** ISO 3166-1 formatted country code

**numberType** can be one of the following values:

> - Unknown
> - Fixed
> - Mobile
> - Other

**normalizedNumber** E.164 normalized number.

**rate** is the cost per minute to call the destination number.

_Example_

    {
        "numberItem": {
            "countryId": "SE",
            "numberType": "Mobile",
            "normalizedNumber": "+46730170101",
            "restricted": false,
            "rate": {
                "currencyId": "USD",
                "amount": 0.0368
            }
        }
    }

### Errors

    40001 - Invalid number specified
    50000 - Internal error

## Get Call Result

**[GET] calling/v1/calls/id/{callId}**

Gets information about a specific call.

_Example_

    [GET] https://calling.api.sinch.com/calling/v1/calls/id/4398599d1ba84ef3bde0a82dfb61abed

### Authorization

This is a protected resource and requires an application signed request \<applicationsignedrequest\> or basic auth \<basicauthorization\>.

### Response

    [ResponseBody]
        GetCallResultResponse

    [GetCallResultResponse]
        identity - from
        identity - to
        string - domain
        string - callId
        int - duration
        string - status
        string - result
        string - reason
        time - timestamp
        object - custom
        money - userRate
        money - debit

**from** contains the caller information.

**to** contains the callee information.

**domain** can be either “pstn” for PSTN endpoint or “mxp” for data (app or web) clients.

**callId** is the unique identifier of the call.

**duration** shows the duration of the call in seconds.

**status** contains the status of the call.

    "ONGOING" | "FINAL"

**result** contains the result of a call. It may have one of the following values:

    "N/A" | "ANSWERED" | "BUSY" | "NOANSWER" | "FAILED"

**reason** contains the reason why a call ended. It may have one of the following values:

    "N/A" | "TIMEOUT" | "CALLERHANGUP" | "CALLEEHANGUP" |
    "BLOCKED" | "NOCREDITPARTNER" | "MANAGERHANGUP" |
    "CANCEL" | "GENERALERROR"

### Errors

    40400 - Call not found
    50000 - Internal error

## Get Conference Info

**\[GET\] /conferences/id/{conferenceId}**

Gets information about an ongoing conference

_Example_

```text
[GET] https://calling.api.sinch.com/v1/conferences/id/myConference
```

### Authorization

This is a protected resource and requires an [application signed request](doc:using-rest#application-signed-request) or [basic auth](doc:using-rest#basic-authorization).

### Response

```json
[ResponseBody]
    GetConfrenceInfoResponse

[GetConferenceInfoResponse]
    Participant[] - participants

[Participant]
    string - cli
    string - id
    int - duration
    bool - muted
```

**cli** shows the phone number of the PSTN participant that was connected in the conference, or whatever was passed as CLI for data originated/terminated calls.

**callId** is the callId of the call leg that the participant joined the conference.

**duration** shows the number of seconds that this participant was connected in the conference.

**muted** shows if the participant is muted currently.

_Example_

```json
{
  "participants": [
    {
      "cli": "+46708168731",
      "id": "abcabcabcabca",
      "duration": 14,
      "muted": false
    },
    {
      "cli": "+46708425201",
      "id": "cdecdecdecde",
      "duration": 12,
      "muted": false
    }
  ]
}
```

### Errors

    ``40400`` - Conference not found

## Mute/Unmute Conference Participant

**\[PATCH\] /conferences/id/{conferenceId}/{callId}**

Mutes or unmutes a participant in an ongoing conference

_Example_

```text
[PATCH] https://calling.api.sinch.com/v1/conferences/id/myConference/abcdef01234
```

### Request

```text
[RequestBody]
    ConferenceCommand

[ConferenceCommand]
    string - command
```

**command** can be either “mute” or “unmute”

_Example_

```json
{
  "command": "mute"
}
```

### Authorization

This is a protected resource and requires an [application signed request](doc:using-rest#application-signed-request) or [basic auth](doc:using-rest#basic-authorization).

### Errors

    ``40400`` - Conference not found

## Kick Conference Participant

**\[DELETE\] /conferences/id/{conferenceId}/{callId}**

Kicks a participant from an ongoing conference

### Authorization

This is a protected resource and requires an [application signed request](doc:using-rest#application-signed-request) or [basic auth](doc:using-rest#basic-authorization).

## Kick All Conference Participants

**\[DELETE\] /conferences/id/{conferenceId}** - Kicks all participants from an ongoing conference

### Authorization

This is a protected resource and requires an [application signed request](doc:using-rest#application-signed-request) or [basic auth](doc:using-rest#basic-authorization).

## Conference and Text-To-Speech Callouts

**\[POST\] /callouts**

Requests a call to be initiated from the server

### Request

```text
[RequestBody]
    CalloutRequest

[CalloutRequest]
    string - method
    TtsCalloutRequest - ttsCallout
    ConferenceCalloutRequest - conferenceCallout
    CustomCalloutRequest - customCallout
```

### Authorization

This is a protected resource and requires an [application signed request](doc:using-rest#application-signed-request) or [basic auth](doc:using-rest#basic-authorization).

There are currently three types of callouts that are supported: conference callouts, text-to-speech callouts and custom callouts. The custom callout is the most flexible, but text-to-speech and conferece callouts whereas conference and text-to-speech callouts are more convinient.

### Conference Callout

With conference callout, the server initiates call to a phone number and when the call is answered, it is connected to a conference room. The same API can be used multiple times to connect multiple phone numbers in the same conference room.

#### Request

```json
[ConferenceCalloutRequest]
    string - cli
    Identity - destination
    string - dtmf
    int - maxDuration
    bool - enableAce
    bool - enableDice
    bool - enablePie
    string - custom
    string - locale
    string - conferenceId
    string - greeting
    string - mohClass
```

**cli** is the number that will be displayed as caller

**destination** identifies the endpoint that should be called.

<table>
    <tr><td><b>type</b></td><td>is a parameter inside the “destination” object. It can be “number” for PSTN endpoints, or “username” for data endpoints (app or web clients).<td></tr>
    <tr><td><b>endpoint</b></td><td>is a parameter inside the “destination” object. It is either the number of a PSTN endpoint, or the username for a data endpoint.</td></tr>
</table>

**dtmf** when the destination picks up, this DTMF tones will be played to the callee. Valid characters in the string are "0"-"9", "#" and "w". A "w" will render a 500 ms pause. Example: "ww1234#w#" will render a 1s pause, the DTMF tones "1", "2", "3", "4" and "#" followed by a 0.5s pause and finally the DTMF tone for "#". This can be used if the callout destination for instance require a conference PIN code or an extension to be entered.

**endpoint** is either the number of a PSTN endpoint, or the username for a data endpoint.

**domain** can be either “pstn” for PSTN endpoint or “mxp” for data (app or web) clients.

**custom** can be used to input custom data.

**locale** is specified with a language code according to ISO 639, a dash and a country code according to ISO 3166-1 alpha-2.

Supported languages / locales:

<div class="magic-block-html">
  <div class="marked-table">
    <table>
      <thead>
        <tr class="header">
          <th align="left">Locale</th>
          <th align="left">Language</th>
        </tr>
      </thead>
      <tbody>
        <tr class="odd">
          <td align="left">da-DK</td>
          <td align="left">Danish</td>
        </tr>
        <tr class="even">
          <td align="left">nl-NL</td>
          <td align="left">Dutch</td>
        </tr>
        <tr class="odd">
          <td align="left">en-AU</td>
          <td align="left">English (Australian)</td>
        </tr>
        <tr class="even">
          <td align="left">en-GB</td>
          <td align="left">English (British)</td>
        </tr>
        <tr class="odd">
          <td align="left">en-IN</td>
          <td align="left">English (Indian)</td>
        </tr>
        <tr class="even">
          <td align="left">en-US</td>
          <td align="left">English (US)</td>
        </tr>
        <tr class="odd">
          <td align="left">en-GB-WLS</td>
          <td align="left">English (Welsh)</td>
        </tr>
        <tr class="even">
          <td align="left">fr-FR</td>
          <td align="left">French</td>
        </tr>
        <tr class="odd">
          <td align="left">fr-CA</td>
          <td align="left">French (Canadian)</td>
        </tr>
        <tr class="even">
          <td align="left">de-DE</td>
          <td align="left">German</td>
        </tr>
        <tr class="odd">
          <td align="left">is-IS</td>
          <td align="left">Icelandic</td>
        </tr>
        <tr class="even">
          <td align="left">it-IT</td>
          <td align="left">Italian</td>
        </tr>
        <tr class="odd">
          <td align="left">ja-JP</td>
          <td align="left">Japanese</td>
        </tr>
        <tr class="even">
          <td align="left">ko-KR</td>
          <td align="left">Korean</td>
        </tr>
        <tr class="odd">
          <td align="left">nb-NO</td>
          <td align="left">Norwegian</td>
        </tr>
        <tr class="even">
          <td align="left">pl-PL</td>
          <td align="left">Polish</td>
        </tr>
        <tr class="odd">
          <td align="left">pt-BR</td>
          <td align="left">Portuguese (Brazilian)</td>
        </tr>
        <tr class="even">
          <td align="left">pt-PT</td>
          <td align="left">Portuguese (European)</td>
        </tr>
        <tr class="odd">
          <td align="left">ro-RO</td>
          <td align="left">Romanian</td>
        </tr>
        <tr class="even">
          <td align="left">ru-RU</td>
          <td align="left">Russian</td>
        </tr>
        <tr class="odd">
          <td align="left">es-ES</td>
          <td align="left">Spanish (European)</td>
        </tr>
        <tr class="even">
          <td align="left">es-US</td>
          <td align="left">Spanish (Latin American)</td>
        </tr>
        <tr class="odd">
          <td align="left">sv-SE</td>
          <td align="left">Swedish</td>
        </tr>
        <tr class="even">
          <td align="left">tr-TR</td>
          <td align="left">Turkish</td>
        </tr>
        <tr class="odd">
          <td align="left">cy-GB</td>
          <td align="left">Welsh</td>
        </tr>
      </tbody>
    </table>
  </div>
</div>

**greeting** is the text that will be spoken as a greeting.

**conferenceId** shows the Id of the conference to which the participant will be connected.

If **enableAce** is set to true and the application has a callback URL specified, you will receive an ACE callback when the call is answered. When the callback is received, your platform must respond with a svamlet, containing the “connectconf” action in order to add the call to a conference or create the conference if it’s the first call. If it is set to false, no ACE event will be sent to your backend.

_Example ACE response when_ `enableAce` _set to true_

```json
{
  "instructions": [],
  "action": {
    "name": "connectConf",
    "conferenceId": "myConference"
  }
}
```

If **enableDice** is set to true and the application has a callback URL specified, you will receive a DiCE callback when the call is disconnected. If it is set to false, no DiCE event will be sent to your backend.

If **enablePie** is set to true and the application has a callback URL specified, you will receive a PIE callback after a runMenu action, with the information of the action that the user took. If it is set to false, no PIE event will be sent to your backend.

_Example of conference callout_

```text
[POST] https://calling.api.sinch.com/calling/v1/callouts
```

```json
{
  "method": "conferenceCallout",
  "conferenceCallout": {
    "cli": "46000000000",
    "destination": { "type": "number", "endpoint": "46000000001" },
    "domain": "pstn",
    "custom": "customData",
    "locale": "en-US",
    "greeting": "Welcome to my conference",
    "conferenceId": "my_conference_room_id",
    "enableAce": false,
    "enableDice": false
  }
}
```

### Text-to-speech Callout

With the text-to-speech callout, the server initiates a call to a phone number and plays a synthesized text messages or pre-recorded sound files.

#### Request

```json
[TtsCalloutRequest]
    string - cli
    Identity - destination
    string - dtmf
    string - domain
    string - custom
    string - locale
    string - text
    string - prompts
    bool - enableAce
    bool - enableDice
```

**cli** is the number that will be displayed as caller

**destination** identifies the endpoint that should be called.

<table>
    <tr><td><b>type</b></td><td>is a parameter inside the “destination” object. It can be “number” for PSTN endpoints, or “username” for data endpoints (app or web clients).<td></tr>
    <tr><td><b>endpoint</b></td><td>is a parameter inside the “destination” object. It is either the number of a PSTN endpoint, or the username for a data endpoint.</td></tr>
</table>

**dtmf** when the destination picks up, this DTMF tones will be played to the callee. Valid characters in the string are "0"-"9", "#" and "w". A "w" will render a 500 ms pause. Example: "ww1234#w#" will render a 1s pause, the DTMF tones "1", "2", "3", "4" and "#" followed by a 0.5s pause and finally the DTMF tone for "#". This can be used if the callout destination for instance require a conference PIN code or an extension to be entered.

**domain** can be either “pstn” for PSTN endpoint or “mxp” for data (app or web) clients.

**custom** can be used to input custom data.

If **enableAce** is set to true and the application has a callback URL specified, you will receive an ACE callback when the call is answered. When the callback is received, your platform must respond with a svamlet, typically containing a “playFiles” instruction and a “hangup” action. If it is set to false, no ACE event will be sent to your backend.

_Example ACE response when \`\`enableAce\`\` set to true_

```json
{
  "instructions": [
    {
      "name": "playFiles",
      "ids": ["#tts[hello world]"]
    }
  ],
  "action": {
    "name": "hangup"
  }
}
```

_Example ACE response when \`\`enableAce\`\` set to true_. In this example, the result will be the same as it would with the enableAce set to false - the message specified in the `TtsCalloutRequest` will be played

```json
{
  "instructions": [],
  "action": {
    "name": "continue"
  }
}
```

If **enableDice** is set to true and the application has a callback URL specified, you will receive a DiCE callback when the call is disconnected. If it is set to false, no DiCE event will be sent to your backend.

**locale** specifies the language for the Text-to-speech message. Supported languages:

> \*cmn-CN: Chinese, Mandarin  
> \* da-DK: Danish  
> \* nl-NL: Dutch  
> \* en-AU: English, Australian  
> \* en-GB: English, British  
> \* en-IN: English, Indian  
> \* en-US: English, US  
> \* en-GB-WLS: English, Welsh  
> \* fr-FR: French  
> \* fr-CA: French, Canadian  
> \* hi-IN: Hindi  
> \* de-DE: German  
> \* is-IS: Icelandic  
> \* it-IT: Italian  
> \* ja-JP: Japanese  
> \* ko-KR: Korean  
> \* nb-NO: Norwegian  
> \* pl-PL: Polish  
> \* pt-BR: Portuguese, Brazilian  
> \* pt-PT: Portuguese, European  
> \* ro-RO: Romanian  
> \* ru-RU: Russian  
> \* es-ES: Spanish, European  
> \* es-MX: Spanish, Mexican  
> \* es-US: Spanish, US  
> \* sv-SE: Swedish  
> \* tr-TR: Turkish  
> \* cy-GB: Welsh

**text** is the text that will be spoken in the text-to-speech message. Every applications default maximum characters allowed in text-to-speech is 600 characters. Contact support if you wish this limit to be changed.

**prompts** is an advanced alternative to using “text”. You can then supply a “;”-separated list of prompts. Either prompt can be the name of a pre-recorded file or a text-to-speech string specified as “\#tts\[my text\]”. To upload and use pre-recorded files, you need to contact Sinch for support. Every applications default maximum characters allowed per 'prompts'-command text-to-speech is 600 characters. Contact support if you wish this limit to be changed.

_Example of text-to-speech callout_

```text
[POST] https://calling.api.sinch.com/calling/v1/callouts
```

```json
{
  "method": "ttsCallout",
  "ttsCallout": {
    "cli": "46000000000",
    "destination": { "type": "number", "endpoint": "46000000001" },
    "domain": "pstn",
    "custom": "customData",
    "locale": "en-US",
    "text": "Hello, this is a synthesized message."
  }
}
```

_Example of text-to-speech callout_

```text
[POST] https://calling.api.sinch.com/calling/v1/callouts
```

```json
{
  "method": "ttsCallout",
  "ttsCallout": {
    "cli": "46000000000",
    "destination": { "type": "number", "endpoint": "46000000001" },
    "domain": "pstn",
    "custom": "customData",
    "locale": "en-US",
    "prompts": "#tts[Hello, we will now play a pre-recorded file.];myprerecordedfile;#tts[We hope you liked listening to the file.]",
    "enabledice": true
  }
}
```

### Custom Callout

With the custom callout, the server initiates a call from the servers that can be controlled by specifying how the call should progress at each call event.

#### Request

```json
[CustomCalloutRequest]
    string - cli
    Identity - destination
    string - dtmf
    int - maxDuration
    string - ice
    string - ace
    string - pie
    string - dice
```

A server callout generates a number of events. It all starts with the server placing the call which generates an "Incoming Call Event" - **"ICE"**. As a response to the ICE event, the server expects SVML to instruct it to a connect PSTN destination. When (if) the destination picks up, the "Answered Call Event", **ACE**, is generated. As a response to this, the server expects SVML to instruct it how to service the answered callout - this can be reading a message, connecting to a conference or running an IVR menu.

If a menu is played as a response to the ACE event, the result of the menu input generates a "Prompt Input Event", **PIE**. The reponse to this event can be the same as to an ACE event.

When the call is finally disconnected, the server generates a "Disconnected Call Event", **DiCE**.

**\***ICE**\***

Here, you have a few alternatives

- If you use the parameters like in text-to-speech and conference callouts (see below), you do not need to populate the "ice" field

```json
   string - cli
   Identity - destination
   string - dtmf
   int - maxDuration
```

- Specify a URL in the "ice" field. Sinch will generate a callback event an expects SVML that instructs on how to connect the call. As of now, only the action "connectPstn" is allowed in the reply
- Leave the "ice" field empty (and do not specify destination). If you have a callback URL configured for your application, you will generate a callback event to that URL
- The final option is to specify your SVML inline as a (JSON escaped) string.

**\***ACE, PIE, DiCE**\***

For each of these parameters, if you populate them with a URL. The server will generate the corresponding event and send it to that URL. If you leave them blank, the service will instead use the callback URL configured for your application.
Again, the final option is to specify your SVML inline as a (JSON escaped) string.

## Manage Call

**[Patch] calling/v1/calls/id/{callId}**

This method can be used to manage ongoing, connected calls or to unpark parked calls.

### Request

    [RequestBody]
        SVAML

    [SVAML]
        Instruction[] - instructions
        Action - action

**Important**: This method can only be used for calls that are originating from or terminating to PSTN or SIP networks.

### Authorization

This is a protected resource and requires an [application signed request](doc:using-rest#application-signed-request) or [basic auth](doc:using-rest#basic-authorization).

### Managing Ongoing, Connected Calls

For ongoing, connected calls, you can use this endpoint to play messages and/or optionally hang up the call. There are two instructions available to play messages. The _PlayFiles_ instruction can be used to play an IVR message, while the _Say_ instruction can be used to play a text-to-speech message. The message, if specified, is played only on the caller side. A caller can, for example, hear a message saying the total minutes have expired and that the call will be disconnected.

For more information on playing messages and performing actions on calls see the [Callback API](doc:voice-rest-api-callback-api).

### Managing Parked calls

For a parked call (see [Callback API](doc:voice-rest-api-callback-api) on how to park a call), this endpoint can be used to “unpark” the call. Parked calls can be considered calls where the ICE response has been “postponed”. When unparking a parked call, you simply provide the SVML you normally would have responded with in the ICE response if you had not “postponed” it -see the [Callback API](doc:voice-rest-api-callback-api).

_Example IVR_

    [PATCH] https://calling.api.sinch.com/calling/v1/calls/id/4398599d1ba84ef3bde0a82dfb61abed
    {
      "instructions": [
        {
          "name": "playFiles",
          "ids": [
            "welcome"
          ],
          "locale": "en-US"
        }
      ],
      "action": {
        "name": "hangup"
      }
    }

_Example text-to-speech_

    [PATCH] https://calling.api.sinch.com/calling/v1/calls/id/4398599d1ba84ef3bde0a82dfb61abed
    {
      "instructions": [
        {
          "name": "say",
          "text": "Hello, this is a text to speech message",
          "locale": "en-US"
        }
      ],
      "action": {
        "name": "hangup"
      }
    }

_Example Start Recording_

    [PATCH] https://calling.api.sinch.com/calling/v1/calls/id/4398599d1ba84ef3bde0a82dfb61abed
    {
      "action": {
        "name": "startRecording",
        "recordingOptions": {
          "destinationUrl": "s3://my-s3-bucket",
          "credentials": "xyz\_access\_key:wrt\_secret\_key:eu-central-1",
          "format": "mp3",
          "notificationEvents": true
        }
      }
    }

_Example Stop Recording_

    [PATCH] https://calling.api.sinch.com/calling/v1/calls/id/4398599d1ba84ef3bde0a82dfb61abed
    {
      "action": {
        "name": "stopRecording"
      }
    }

### Authorization

This is a protected resource and requires an application signed request \<applicationsignedrequest\> or basic auth \<basicauthorization\>.

### Errors

    40003 - Invalid request
    40400 - Call not found
    50000 - Internal error

## List Recordings

**[GET] calling/v1/recording?from=timestamp&to=timestamp&page=int&pageSize=int**

Lists the stored recordings in the Sinch Drive.

### Authorization

This is a protected resource and requires an application signed request \<applicationsignedrequest\> or basic auth \<basicauthorization\>.

### Response

    [ResponseBody]
        RecordingFileInfo[]

    [RecordingFileInfo]
        string - key
        string - url
        timestamp - createdOn
        timestamp - expiresOn
        object - headers

**key** is a unique identifier for the file. Use this to download or delete the file from the Sinch Drive.

**url** is a direct URL that can be used to download the file

**createdOn** is the date the file has been uploaded to the Sinch Drive

**expiresOn** is the date the platform will automatically delete the file from the Sinch Drive

**headers** additional information about the file stored in as key/value

### Errors

    40001 - 'pageNumber' must be greater than 0
    40001 - 'pageSize' must be a positive integer
    40001 - 'to' must be greater than 'from'
    50000 - Internal error

## Download Recording

**[GET] calling/v1/recording/{key}**

Retrieve the recording file from the Sinch Drive.

### Authorization

This is a protected resource and requires an application signed request \<applicationsignedrequest\> or basic auth \<basicauthorization\>.

### Response

    Binary data

### Errors

    40002 - No key specified
    40400 - Recording not found
    50000 - Internal error

## Delete Recording

**[DELETE] calling/v1/recording/{key}**

Remove a recording file from the Sinch Drive.

### Authorization

This is a protected resource and requires an application signed request \<applicationsignedrequest\> or basic auth \<basicauthorization\>.

### Errors

    40002 - No key specified
    40400 - Recording not found
    50000 - Internal error
