---
title: sendMMS
excerpt: Send an MMS that has been defined in XML. Read more.
next:
  pages:
    - xml-service-deletemmsid
---
## Overview
Sends an MMS defined in the XML containing slides of embedded with video, audio, images and/or text to a single number in international number format. The sendMMS is a minor extension of the saveMMS function. Note, while elements may be given in any order, the slides are created in the order given in the API call.

While the sendMMS API method allows sending slides containing video elements, it is not recommended as it introduces latency during the call. Therefore, Sinch suggests strongly that you use the saveMMS API call to submit video content and then Sinch MMS service can transcode the contents in advance before you request the sendSavedMMS API call.

If the sendMMS API is used to send the same content to multiple users, at present the Sinch application does not cache this content as such the content will need to be uploaded for each request. This is another reason Sinch suggests that you use combination of saveMMS and sendSavedMMS API calls.

In case of Optimized MMS, there is a mandatory ‘fallback-sms-text’ which is used as the SMS text in the case of MMS is delivered via SMS link. In case of Standard MMS, the ‘fallback-sms-text’ is not required.

MMS "Link Expiration Date’ is used to expire the MMS Link (In the case when MMS fallback to SMS Link). Depending on this expiration date, the content is disabled on this link. By default it expires 365 days from the date the original MMS was created.

**Request Parameters:**  
> Mandatory: action, api\_key, to, from, name, slide, fallback-sms-text, service-id

>    Optional: operatorid, campaignRef, subject, image, audio, video, url, text, duration, vcard, ical, pdf, passbook, message-text

**Response Parameters:**  
 >   status, to, mmsId, trackingId, errorCode, errorInfo

**Related Error Codes:**  
>    All saveMMS Error Codes plus E110, E111, E201, E628

### Request Example

**sendMMS Request**
```xml
<request>
    <action>sendMMS</action>
    <api-key>qTFkykO9JTfahCOqJ0V2Wf5Cg1t8iWlZ</api-key>
    <to>12399471613</to>
    <from>28444</from>
    <operator-id>31062</operator-id>
    <message-subject>The subject</message-subject>
    <service-id>32113</service-id>
    <name>FuelTheFire</name>
    <slide>
        <duration>5</duration>
        <image>
            <url>http://www.yoursite.com/images/1.jpg</url>
        </image>
        <audio>
            <url>http://www.yoursite.com/audio/1.mp3</url>
        </audio>
        <message-text>Here is some text blah blah ...</message-text>
    </slide>
    <slide>
        <message-text>Invitation</message-text>
        <ical><url>http://www.yoursite.com/cal/2.ics</url></ical>
        <duration>10</duration>
    </slide>
</request>
```


### Response examples

**sendMMS Success Response**
```xml
<response>
    <status>Success</status>
    <to>12399471613</to>
    <mms-id>35674</mms-id>
    <tracking-id>MMS_12345</tracking-id>
    <status-details>MMS request accepted and queued for delivery</status-details>
</response>
```


**sendMMS Failure Response**
```xml
<response>
    <status>Failure</status>
    <error-code>E111</error-code>
    <to>12399471613</to>
    <error-info>Invalid shortcode</error-info>
</response>
```
