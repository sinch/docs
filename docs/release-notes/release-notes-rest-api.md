---
title: Sinch REST API
excerpt: >-
  See how the Sinch JavaScript SDK is evolving and find out about new features
  and bug fixes.
---

<h3>Specify language for SMS verification</h3>
Multiple languages are supported for SMS verification.

SMS verification can now take an “Accept-Language” header on the initiate verification request to specify in which language the SMS should be sent.  The value can also be specified/overwritten via the “Verification Request Event” callback.

Check out the verification REST documentation [here](doc:verification-rest-api) to see how.

<h3>Sinch REST API documentation update 20160420</h3>
This release has updated documentation for SMS section.

If you want to get more details about message size please check our documentation: SMS \<sms-classic-sinch\>

<h3>Sinch REST API documentation update 20160407</h3>
This release introduces the dialTimeout option for application signed flashCall verification requests.

For more information please check our documentation here [here](doc:verification-rest-api)

<h3>Sinch REST API documentation update 20160309</h3>
This documentation release introduces:  
1.  The enablePie parameter in the Conference Callout API
2.  The addition of a country Id as a CDR parameter for PSTN termination CDRs

<h3>Sinch REST API documentation update 20160127</h3>
This release introduces the termination of calls through SIP interworking (beta). With this feature, you can now route all your app, web, SIP or PSTN calls to your SIP infrastructure.

For more information please check our documentation:

/docs/voice/ [here](doc:voice-introduction)

<h3>Sinch REST API documentation update 20160113</h3>
This release introduces the ability to create interactive IVRs in your voice application. For information on how to do that, check out the [runMenu](doc:voice-rest-api-onprem-callback-api#runmenu) action in the Callback API documentation section. Input from the user during an IVR menu can be sent to your backend through a new callback event, the Prompt Input Event \<PIE\> (PIE).

We have also released a number of APIs to manage your numbers and your callback URLs for Voice. More information can be found here \<calling-api\>.

<h3>Sinch REST API documentation update 20151124</h3>
With this release, the Conference Callout API supports the parameter enableACE, which allows the ACE event to be sent towards the developer’s backend.

For more information please check the documentation [callouts](doc:voice-rest-api-onprem-calling-api#conference-and-text-to-speech-callouts).

<h3>Sinch REST API documentation update 20151105</h3>
This release introduces greater granularity in the Verification results and reasons why verification requests did not succeed.

You can find more information on the CDR files and relevant documentation \<verification-cdr\>.

<h3>Sinch REST API documentation update 20151027</h3>
What’s new:  
-   Updated the Verification REST API “Query Verification by ID”
-   Added instructions on how to record a conference.

<h3>Sinch REST API documentation update 20150714</h3>
This release introduces the conference call functionality. You can find more information on how to connect calls to a conference through REST APIs here:

/docs/voice/rest/\#conferencecalls \<confttscallouts\>

<h3>Sinch REST API documentation updat 20150626</h3>
Added documentation on how to administer voice and SMS numbers through REST APIs. It can be found here:

/number-administration-documentation/ \<numberadministration\>

<h3>Sinch REST API documentation update 20150618</h3>
Improved explanations on restrictions for the SMS API. Also fixed a problem with a character that was ommited in the Authorization signature creation during the last documentation update.

<h3>Sinch REST API documentation update 20150521</h3>
The Sinch documentation has been restructured and is now split into products.

**Verification:**

We are glad to announce the introduction of our Verification product, which comes packaged with a complete set of SDKs and REST APIs. Complete documentation can be found here:

/docs/verification/ \<verificationdocumentation\>

And the REST APIs in particular:

/docs/verification/rest/ [here](doc:verification-rest-api)

The standalone Flashcall verification API will continue to be supported normally. This API is not part of the new Verification service and will not work with the new SDKs. The documentation can be found here:

/flash-verification-version-1/

<h3>Sinch REST API documentation update 20150520</h3>
The SMS Messaging API is now restricted to only send to your verified phone number, for Sandbox apps. To send SMS to all phone numbers, you will need a Production app.

<h3>Sinch REST API documentation update 20150506</h3>
Documentation update:  
-   Fixed an error in the example code for the ICE callback event response
-   Added examples for the SVAML Instructions and Actions

<h3>Sinch REST API documentation update 20150428</h3>
With this release, the following restrictions are applied to the Sinch SMS API for new partners:
- In order to use the Sinch SMS API, you need to have a verified phone number. In order to verify your phone number, login to the Sinch dashboard, click on “Quickstart” and follow the instructions.
- The default CLI used for oubound SMS is now set to the phone number that you have verified in the Sinch dashboard.

<h3>Sinch REST API documentation update 20150401</h3>
This release introduces the support for PSTN origination and text-to-speech.

With PSTN origination, you can now route calls from the PSTN network to any application or any fixed or mobile number. Routing and control can be managed by your backend. For more information, on how to use PSTN origination, please check the documentation [here](doc:voice-rest-api-onprem-calling-api).

Text to speech, abbreviated as TTS, is a form of speech synthesis that converts text into spoken voice output. With this functionality, an application can instruct the Sinch calling service to read out a particular text message in a voice call. For more information please check the documentation [here](doc:voice-rest-api-onprem-calling-api#text-to-speech).

<h3>Sinch REST API documentation update 20150324</h3>
Changed the verification status that is returned when querying the status of a flashcall verification and the respective result in the flashcall verification CDRs. They now are set to “N/A” because the Sinch verification service has very limited visibility on the result of a verification request. This field will be used again when the Sinch Verification SDK is used.

<h3>Sinch REST API documentation update 20150211</h3>
With this release, Sinch is introducing a new way of verifying phone numbers: Flash-calling. With the Sinch API, a developer can place a flash-call (“missed call”) to a phone number of a user, and check in the user’s app if the phone was received with a particular caller number. For more information please check our documentation [here](doc:verification-rest-api).

<h3>Sinch REST API documentation update 20150120</h3>
This release adds support for some new parameters in the Calling Callback events ICE and DiCE.

> 1.  In the ICE event, a new parameter “userRate” is added. It shows the current rate of the particular call, if the call is of “PSTN” type.
> 2.  In the DiCE event, there are 2 new parameters: “userRate”, which shows the rate of the particular call, and “debit” which shows how much was charged for the call.

<h3>Sinch REST API Documentation update 20141217</h3>
This release introduces the support of sending SMS in a wide range of countries, as well as receiving inbound SMS. Also some bug fixes and minor updates in the documentation.

<h3>Sinch REST API Documentation update 20141112</h3>
This release introduces these new features and changes:

> 1. [Incoming Call Event (ICE)](doc:voice-rest-api-onprem-callback-api#incoming-call-event-callback-ice) - The Incoming Call Event is now also supported to connect App-App calls. A new action is supported for connecting app-app calls, called ConnectMXP.
>
> 2. [authorization](doc:verification-rest-callback-api#authorization) - Signing requests with “Session” scheme is no longer supported.
>
> 3. [calldetailrecords](doc:verification-rest-call-detail-records) - The CDR format for app-app calls is now also specified.

<h3>Sinch REST API Documentation update 20141030</h3>
A new parameter is provided in the Incoming Call Event, Answered Call Event and Disconnect Call Event callbacks RequestBody, as well as in the Notification Callback. The new parameter is called “user” and the type is “string”. It provides the user Id of the caller, as it is passed from the SDK client.

<h3>Sinch REST API Documentation update 20141024</h3>
Calling API documentation update:

> - Included the CANCEL reason code in the response of the DiCE event, the getCallResult API and in the CDRs. The CANCEL reason is returned when the caller initiated the call and cancelled it.
> - Added a custom field in Answered Call event and Disconnect Call event. It is used to pass custom headers in these events.
