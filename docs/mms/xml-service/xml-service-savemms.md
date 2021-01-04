---
title: saveMMS
excerpt: >-
  Store MMS template from XML. These templates can be saved for later use. The
  MMS may contain slides with different types. Read more.
next:
  pages:
    - xml-service-sendsavedmms
---
## Overview

This API stores an MMS from XML. The MMS may contain slides embedded with text, and video, audio, images, PDF, iCal, vCard, Passbook files. Once the MMS is saved, it can be utilized by other functions through the mms-id returned. Note, while elements may be given in any order, the slides are created in the order given in the API call.

### Request Example

**saveMMS Request**
```xml
<request>
    <action>saveMMS</action>
    <api-key>qTFkykO9JTfahCOqJ0V2Wf5Cg1t8iWlZ</api-key>
    <message-subject>The subject</message-subject>
    <name>FuelTheFire</name>
    <slide>
        <image><url>http://www.yoursite.com/images/1.jpg</url></image>
        <audio><url>http://www.yoursite.com/audio/1.mp3</url></audio>
        <message-text>Here is some text</message-text>
        <duration>5</duration>
    </slide>
    <slide>
        <message-text>My Contact</message-text>
        <vcard><url>http://www.yoursite.com/2.vcf</url></vcard>
        <duration>10</duration>
    </slide>
</request>
```


  - **Request Parameters:**  
    Mandatory: action, api\_key, subject, name, slide

    Optional: image, audio, video, url, text, duration, vcard, ical,
    pdf, passbook

  - **Response Parameters:**  
    status, mmsId, errorCode, errorInfo

  - **Related Error Codes:**  
    E223, E224, E225, E226, E227, E228, E229, E230, E301, E302, E303,
    E304, E331, E332, E333, E334, E335, E336, E337, E338, E341, E351,
    E352, E353, E355, E356, E357, E358

### Response Examples

**saveMMS Success Response**
```xml
<response>
    <status>Success</status>
    <mms-id>35674</mms-id>
</response>
```


**saveMMS Failure Response**
```xml
<response>
    <status>Failure</status>
    <error-code>E111</error-code>
    <error-info>Invalid shortcode</error-info>
</response>
```


## Postback Notifications
When an MMS is saved, the system will generate a Postback notification and unlock MMS for further use. If an MMS contain audio/video, Postback will be sent when the encoding of the MMS audio/video is finished, otherwise Postback notification will be sent instantly. Below is an example of Postback notification when an MMS is saved successfully:

**saveMMS Postback notification**
```xml
<POSTBACK>
    <ORIGIN>MMS_MT</ORIGIN>
    <CODE>N003</CODE>
    <MMSID>35674</MMSID>
</POSTBACK>
```


**saveMMS error Postback Notification**
```xml
<POSTBACK>
    <ORIGIN>MMS_MT</ORIGIN>
    <CODE>E002</CODE>
    <MMSID>35674</MMSID>
    <AUDIONAME>http://www.yoursite.com/audio/1.mp3</AUDIONAME>
</POSTBACK>
```


## Special Considerations for saveMMS

  - MMS message-subject is required. The message-subject parameter should not contain emoji/Unicode characters as this will cause messages to get rejected by the carrier's MMSC.
  - The API SHALL reformat the content when necessary so that it can be delivered to the end users handset in the best possible way.
  - Delivery success takes precedence over audio and video content quality and occasionally the picture quality will be reduced to fit handset message size requirements.
  - Video SHALL be reduced in quality to fit delivery limitations and if it still does not fit it will be delivered as XHTML/SMS.
  - Each request MUST contain at least one slide which MAY contain text and/or image and/or video and/or audio and/or objects (vcard/ical/pdf/passbook).
  - The API SHALL support up to 80 characters in the MMS subject.
  - The API SHALL support up to 8 slides for each MMS submission.
  - The API SHALL NOT support multiple files of the same MIME type on the same slide.
  - Slides with image SHALL NOT support video but SHALL support audio.
  - Slides with audio SHALL NOT support video. Slides with video SHALL only support text.
  - Slides with text SHALL support up to 5000 characters in any slide.
  - Slide with vcard/ical/pdf/passbook object SHALL NOT support media type audio/video/image and vice-versa.
  - All slides MAY contain a duration for playback.
  - Default slide duration is 10 seconds.
  - Slide duration will be overwritten with the audio/video file duration after encoding is completed.
  - URLs provided MUST contain the full path to the mime files.
  - Slide Duration SHOULD NOT exceed 30 seconds.
  - MMS containing audio/video can be used only when audio/video encoding is completed.
  - After submission you will not be given a successful acknowledgement of audio/video encoding when a message is submitted
  - The HTTP status of audio/video encoding after it has been completed will be sent to your Postback URL.
  - Supported Media: TEXT/PLAIN, GIF/JPG/PNG, MP3/WAV, 3GP, MP4, MPEG, MPG, AVI, WMV.
  - There is a maximum source file size for each supported source file submitted.
  - You can find out what the current maximum is by visiting your API settings.
  - MMS messages are delivered in B64 encoding To estimate the final size of Base64-encoded binary data multiply the filesize by 1.37 times the original data size + 814 bytes (for headers).
