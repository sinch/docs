---
title: sendSavedMMS
excerpt: >-
  Send stored (MMS) content from a specified account to a mobile number. Read
  more.
next:
  pages:
    - xml-service-sendmms
---
## Overview
This API sends stored content from a specified account using an MMSID to a single mobile number. FROM must be one of the shortcodes allowed for your account. In case the number is from a different country than the FROM shortcode is assigned to the default shortcode for those countries will be used.

### Content Transcoding
Every binary MMS we deliver can be transcoded for the destination handset and every web page we deliver is transcoded for the browsing handset. To transcode a binary MMS we must know what type of handset the user has. We are able to obtain this handset type information from delivery receipts and store the record in a handset cache for later use. We have a database of attributes which we manually match to every new handset in the cache so we can adapt the content during MMS delivery.

Our API allows you to dynamically change the text of each slide by setting up optional CUSTOMTEXT (CUSTOMTEXT must include mandatory fields: value and slide) and MMS Subject by setting CUSTOMSUBJECT.

**Request Parameters:**

- Mandatory: action, api-key, fallback-sms-text, from, mms-id, service-id, to
- Optional: custom-subject, custom-text, data, ddm-force, ddm-message-text, ddm-message-subject, ddm-message-timeout, operator-id

**Response Parameters:**  
>  status, to, from, mmsId, trackingId, errorCode, errorInfo

**Related Error Codes:**  
>  E107, E110, E111, E114, E241, E503, E618, E619, E620, E622, E626, E627, E628, E629, E650

### Request Example

**sendSavedMMS Request**
```xml
<request>
    <action>sendSavedMMS</action>
    <api-key>qTFkykO9JTfahCOqJ0V2Wf5Cg1t8iWlZ</api-key>
    <to>14044790000</to>
    <from>28444</from>
    <service-id>12345</service-id>
    <mms-id>35674</mms-id>
    <custom-text>
        <value>My custom text in first slide</value>
        <slide>1</slide>
    </custom-text>
    <custom-subject>My custom subject</custom-subject>
</request>
```


### Response Examples

**sendSavedMMS Success Response**
```xml
<response>
    <status>Success</status>
    <mms-id>35674</mms-id>
    <tracking-id>TU1TXzU5Nzg3OQ==</tracking-id>
    <to>14044790000</to>
    <from>28444</from>
    <status-details>MMS request accepted and queued for delivery</status-details>
</response>
```


**sendSavedMMS Failure Response**
```xml
<response>
    <status>Failure</status>
    <error-code>E713</error-code>
    <to>14044790000</to>
    <error-info>There is billing problem on your account</error-info>
</response>
```


**Postback Notifications For SendSavedMMS** When the MMS delivery is
processed successfully the system will generate a Postback notification.

## Special Considerations for sendSavedMMS

 - MMS message-subject is required. The message-subject parameter should not contain emoji/Unicode characters as this will cause messages to get rejected by the carrier's MMSC.
