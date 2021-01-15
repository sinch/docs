---
title: Sinch MMS MO Postbacks
excerpt: >-
  An API that notifies when a customer have replied to an MMS message trough the
  XML API. Read more.
next:
  pages:
    - xml-service-appendix
---
> **Note**    
>
> MO MMS functionality is limited to Verizon, Sprint and T-Mobile initially. MO MMS for AT\&T will be supported shortly after commercial launch.

The MMS MO postback API notifies you that a customer has replied to your message, or interacted to one of your keywords.

## MMS MO Information
To receive MMS MO postback you need to work with your Sinch Account Manager to provision with your account. Once the MMS MO postback is enabled you will start receiving postbacks for each MMS MO received on the MMS MO Keyword or Short Code.

## The MMS MO
This postback notifies you when an MMS MO is received.

|     Variable    |  Description   |
| --------------- | -------------- |
| code            | N401           |
| origin          | MMS\_MO        |
| from            | The phone number, including the country code of the sender |
| to              | The recipient shortcode |
| keyword         | If a keyword was recognized in the first word of the subject or the,first word body of the message and it matched to a MMS Inbox Keyword,campaign that keyword will be passed in this node |
| tracking-id     | A tracking ID, Sinch has assigned this message |
| operator-id     | The operator-id of the sender’s carrier |
| timestamp       | The timestamp that our system received the MMS MO |
| message-subject | The subject sent in the MMS MO |
| content         | Contains the file nodes sent in the MMS MO |
| file            | A series of sub-nodes that contains a single URL to a picture, video, audio or text file sent in the MMS MO within each node. The URL points to the location of the content on our servers. For those developing the back-end handling of the postback URL, you may choose to download/store these content files for whatever purpose you see fit. You may also choose to store the URLs for download at a future time. The file will be removed based on the terms of your contract |

### MMS MO Example

**MMS MO**
```xml
<postback>
    <origin>MMS_MO</origin>
    <code>N401</code>
    <from>14082257140</from>
    <to>28444</to>
    <keyword>all</keyword>
    <tracking-id>MMS_MO_iLnCRrL6</tracking-id>
    <operator-id>31002</operator-id>
    <timestamp>2014-02-03T11:19:49-05:00</timestamp>
    <message-subject></message-subject>
    <content>
        <file>URL of Content Here</file>
        <file>URL of Content Here</file>
        <file>URL of Content Here</file>
    </content>
</postback>
```
