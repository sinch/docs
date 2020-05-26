---
title: Callbacks
excerpt: >-
  Get to know the callbacks that has been sent via WhatsApp. Find out more
  information here.
next:
  pages:
    - whatsapp-blacklist-callback
---
A callback is a HTTP POST request with a notification made by the Sinch WhatsApp API to a URI of your choosing. The Sinch WhatsApp API expects the receiving server to respond with a response code within the `2xx Success` range. If no successful response is received then the API will either schedule a retry if the error is expected to be temporary or discard the callback if the error seems permanent. The first initial retry will happen 5 seconds after the first try. The next attempt is after 10 seconds, then after 20 seconds, after 40 seconds, after 80 seconds and so on, doubling on every attempt. The last retry will be at 81920 seconds (or 22 hours 45 minutes) after the initial failed attempt.

A callback from the Sinch WhatsApp API will always have the following structure:

|Name          | Description                       | JSON Type     |
|--------------|-----------------------------------|---------------|
|type          | Will always be `whatsapp`         | String        |
|statuses      | Array of delivery reports         | Array[Object] |
|contacts      | Array of inbound messages contact | Array[Object] |
|notifications | Array of inbound messages         | Array[Object] |

> 📘 Note
>
> A callback from the Sinch WhatsApp API can contain both delivery reports and inbound messages.
>
> Contacts might be placed only for text, contact and location inbound messages.

### Delivery report callback

A delivery report contains the status and state of each message sent through the Sinch WhatsApp API.
The format of a delivery report is as follows:

|Name       | Description                                                           | JSON Type |
|-----------|---------------------------------------------------------------------- |-----------|
|status     | The status of the message, either `success` or `failure`              | String    |
|state      | The state of the message                                              | String    |
|message_id | The message id of the message to which this delivery report belong to | String    |
|details    | Detailed message containing information.                              | String    |
|recipient  | The recipient of the message that this delivery report belong to      | String    |
|timestamp  | ISO-8601 datetime of the status update                                | Object    |

Where the states means:

| State          | Description                                                                   |
| -------------- | ----------------------------------------------------------------------------- |
| `queued`       | Message has been received and queued by the Sinch WhatsApp API                |
| `dispatched`   | Message has been dispatched by Sinch WhatsApp API to WhatsApp servers         |
| `sent`         | Message has been sent by WhatsApp to end-user                                 |
| `delivered`    | Message has been successfully delivered to end-user by WhatsApp               |
| `read`         | Message has been read by the end-user in the WhatsApp application             |
| `deleted`      | Message has been deleted or expired in the application                        |
| `failed`       | Message has failed                                                            |
| `no_opt_in`    | Message rejected by Sinch API as recipient is not registered to have opted in |
| `no_capability`| Message rejected by the Sinch API as the recipient lacks WhatsApp capability  |

#### Sample delivery report callback

```json
{
  "type": "whatsapp",
  "statuses":[
    {
      "status":"success",
      "state":"delivered",
      "message_id":"01DPNXZ0WCF9XD19MH84XD0P62",
      "recipient": "+46732001122",
    }
  ]
}
```

### Inbound message callback

An inbound message or MO (Mobile Originated) is a message sent to one of your bots from a WhatsApp user.
The format is as follows:

**Contacts**

|Name      | Description                                                             | JSON Type |
|----------|------------------------------------------------------------------------ |-----------|
|profile   | Profile details of message sender                                       | Object    |
|wa_id     | WhatsApp account id (phone number)                                      | String    |

**Profile**

|Name      | Description                                                | JSON Type |
|----------|----------------------------------------------------------- |-----------|
|name      | Sender name of message                                     | String    |

**Notifications**

|Name        | Description                                                                 | JSON Type |
|------------|---------------------------------------------------------------------------- |-----------|
|from        | MSISDN of the user sending the message                                      | String    |
|in_group    | Identifier of a group if this message is sent to one of your owned groups   | String    |
|to          | The identifier of the receiving bot                                         | String    |
|replying_to | A context object, present only if the user is replying to a specific thread | Object    |
|message_id  | Generated message id for the inbound message                                | String    |
|message     | Message object describing the inbound message                               | Object    |
|timestamp   | ISO-8601 datetime of the status update                                      | Object    |

**Context**

|Name        | Description                                                                 | JSON Type |
|------------|---------------------------------------------------------------------------- |-----------|
|message_id  | Message Id of the message which is being replied directly to                | String    |
|from        | MSISDN of the user which sent the message with the above message id         | String    |

**Text message**

|Name       | Description                                                           | JSON Type |
|-----------|---------------------------------------------------------------------- |-----------|
|type       | Fixed value `text`                                                    | String    |
|body       | The text of the text message                                          | String    |

##### Sample inbound text message

```json
{
  "type":"whatsapp",
  "contacts":[
    {
      "profile":{
        "name":"John Smith"
      },
      "wa_id":"0732001122"
    }
  ],
  "notifications":[
    {
      "from":"0732001122",
      "to":"sinchbot",
      "message_id":"01DPNXZ0WCF9XD19MH84XD0P62",
      "message":{
        "type":"text",
        "body":"Hello bot I want to know something!"
      }
    }
  ]
}
```

**Location message**

|Name       | Description                                                            | JSON Type |
|-----------|----------------------------------------------------------------------- |-----------|
|type       | Fixed value `location`                                                 | String    |
|latitude   | Latitude of location being sent                                        | Number    |
|longitude  | Longitude of location being sent                                       | Number    |
|address    | Address of the location                                                | String    |
|name       | Name of the location                                                   | String    |
|url        | URL for the website where the user downloaded the location information | String    |

##### Sample inbound location message

```json
{
  "type":"whatsapp",
  "contacts":[
    {
      "profile":{
        "name":"John Smith"
      },
      "wa_id":"0732001122"
    }
  ],
  "notifications":[
    {
      "from":"0732001122",
      "to":"sinchbot",
      "message_id":"01DPNXZ0WCF9XD19MH84XD0P62",
      "message":{
        "type":"location",
        "lat":55.7047,
        "lng":13.191,
        "name":"Sinch Ideon Lund",
        "address":"Scheelevägen 17"
      }
    }
  ]
}
```

**Quick reply button reply message**

|Name       | Description                                                            | JSON Type |
|-----------|----------------------------------------------------------------------- |-----------|
|type       | Fixed value `button`.                                                  | String    |
|index      | The index of the button in the message template (0-2).                 | String    |
|text       | The button text.                                                       | String    |
|payload    | The payload that was sent with the button.                             | String    |

##### Sample inbound quick reply button reply message

```json
{
  "type":"whatsapp",
  "notifications":[
    {
      "from": "0732001122",
      "to": "sinchbot",
      "message_id": "01DPNXZ0WCF9XD19MH84XD0P62",
      "message": {
        "type": "button",
        "index": "2",
        "text": "Option 3",
        "payload": "some_payload"
      },
      "timestamp": "2020-05-07T10:02:10Z",
      "to": "a_bot_id",
      "replying_to": {
        "from": "447537817391",
        "message_id": "01E7Q9AVTRB5A30JD7D9ZN0HTE"
      }
    }
  ]
}
```

**Contacts**

|Name       | Description                                                        | JSON Type     |
|-----------|------------------------------------------------------------------- |---------------|
|type       | Fixed value `contacts`                                             | String        |
|contacts   | Array of contact objects                                           | Array[Object] |

**Contact object**

|Name              | Description                                                 | JSON Type     |
|----------------- |------------------------------------------------------------ |---------------|
|addresses         | Array of contact address(e)                                 | Array[Object] |
|birthday          | Contact's birthday, YYYY-MM-DD formatted string             | String        |
|emails            | Array of contact email address(es)                          | Array[Object] |
|ims               | Array of message contact information                        | Array[String] |
|name              | Contact full name information                               | Object        |
|org               | Contact organization information                            | Object        |
|phones            | Array of contact phone number(s)                            | Array[Object] |
|urls              | Array of contact URL(s)                                     | Array[Object] |
|contact_image_url | The public URL of the contact image                         | String        |

**Contact address**

|Name         | Description                                                        | JSON Type     |
|-------------|--------------------------------------------------------------------|---------------|
|type         | Type of address, `HOME`, `WORK`                                    | String        |
|street       | Street number and address                                          | String        |
|city         | City name                                                          | String        |
|state        | State abbreviation                                                 | String        |
|zip          | ZIP code                                                           | String        |
|country      | Full country name                                                  | String        |
|country_code | Two-letter country abbreviation                                    | String        |

**Contact email**

|Name         | Description                                                        | JSON Type     |
|-------------|--------------------------------------------------------------------|---------------|
|type         | Type of email address, `HOME`, `WORK`                              | String        |
|email        | Email address                                                      | String        |

**Contact IM**

|Name         | Description                                                        | JSON Type     |
|-------------|--------------------------------------------------------------------|---------------|
|service      | Type of service                                                    | String        |
|user_id      | User identifier on service                                         | String        |

**Contact name**

|Name           | Description                                                        | JSON Type     |
|---------------|--------------------------------------------------------------------|---------------|
|formatted_name | Full name as it normally appears                                   | String        |
|first_name     | First name                                                         | String        |
|last_name      | Last name                                                          | String        |
|middle_name    | Middle name                                                        | String        |
|suffix         | Name suffix                                                        | String        |
|prefix         | Name prefix                                                        | String        |

**Contact organization**

|Name           | Description                                                        | JSON Type     |
|---------------|--------------------------------------------------------------------|---------------|
|company        | Name of the contact's company                                      | String        |
|department     | Name of the contact's department                                   | String        |
|title          | Contact's business title                                           | String        |

**Contact phone number**

|Name         | Description                                                           | JSON Type     |
|-------------|-----------------------------------------------------------------------|---------------|
|type         | Type of phone number, `CELL`, `MAIN`, `HOME`, `WORK`, `IPHONE`        | String        |
|phone        | Automatically populated with the WhatsApp phone number of the contact | String        |

**Contact URL**

|Name         | Description                                                           | JSON Type     |
|-------------|-----------------------------------------------------------------------|---------------|
|type         | Type of URL `HOME`,  `WORK`                                           | String        |
|url          | URL                                                                   | String        |

##### Sample inbound contact message

```json
{
  "type":"whatsapp",
  "contacts":[
    {
      "profile":{
        "name":"John Smith"
      },
      "wa_id":"0732001122"
    }
  ],
  "notifications":[
    {
      "from":"0732001122",
      "to":"sinchbot",
      "message_id":"01DPNXZ0WCF9XD19MH84XD0P62",
      "message":{
        "type":"contacts",
        "contacts":[
          {
            "addresses":[
              {
                "city":"Menlo Park",
                "country":"United States",
                "country_code":"us",
                "state":"CA",
                "street":"1 Hacker Way",
                "type":"HOME",
                "zip":"94025"
              }
            ],
            "birthday":"2012-08-18",
            "emails":[
              {
                "email":"test@fb.com",
                "type":"WORK"
              }
            ],
            "name":{
              "first_name":"John",
              "formatted_name":"John Smith",
              "last_name":"Smith"
            },
            "org":{
              "company":"WhatsApp",
              "department":"Design",
              "title":"Manager"
            },
            "phones":[
              {
                "phone":"+1 (650) 555-1234",
                "type":"WORK",
                "wa_id":"16505551234"
              }
            ],
            "urls":[
              {
                "url":"https://www.facebook.com",
                "type":"WORK"
              }
            ]
          }
        ]
      }
    }
  ]
}

```

**Media**

|Name       | Description                                                            | JSON Type |
|-----------|------------------------------------------------------------------------|-----------|
|type       | Fixed value `image`, `document`, `audio`, `video`, `voice`, `sticker`  | String    |
|url        | The public url of the media file                                       | String    |
|mime_type  | Mime type of the media file                                            | String    |
|caption    | Caption of the media file                                              | String    |
|filename   | Optional filename, only valid for audio and document                   | String    |
|metadata   | Optional sticker metadata, only used for stickers                      | Object    |

The sticker metadata object has the following parameters:

|Name                   | Description                                                                 | JSON Type     |
|-----------------------|-----------------------------------------------------------------------------|---------------|
|stickerpack-id         | The id of the stickerpack the sticker belongs to                            | String        |
|stickerpack-name       | The name of the stickerpack the sticker belongs to                          | String        |
|stickerpack-publisher  | The publisher of the stickerpack the sticker belongs to                     | String        |
|emojis                 | The emojis included in the stickerpack the sticker belongs to               | Array[String] |
|ios-app-store-link     | A link to the stickerpack the sticker belongs to in the Apple iOS App Store | String        |
|android-app-store-link | A link to the stickerpack the sticker belongs to in the Google Play store   | String        |
|is-first-party-sticker | 1 if the sticker is part of a first-party stickerpack, 0 otherwise          | Integer       |

##### Sample inbound image message

```json
{
  "type":"whatsapp",
  "notifications":[
    {
      "from":"0732001122",
      "to":"sinchbot",
      "message_id":"01DPNXZ0WCF9XD19MH84XD0P62",
      "message":{
        "type":"image",
        "url":"http://www.example.com/img.jpg",
        "mime_type":"image/jpeg",
        "caption":"Fantastic headphones"
      }
    }
  ]
}
```

**Group event notifications**

|Name       | Description                                                            | JSON Type |
|-----------|------------------------------------------------------------------------|-----------|
|type       | Fixed value `group_event`                                              | String    |
|body       | Message describing the event                                           | String    |
|members    | MSISDNs of the users involved in the event                             | String    |
|event_type | Event type                                                             | String    |
|in_group   | Identifier of the group involved in the notification                   | String    |

The event types can be one of the following:

| Event type                          |
| ----------------------------------- |
| `group_created`                     |
| `group_user_promoted`               |
| `group_user_demoted`                |
| `group_user_joined`                 |
| `group_user_left`                   |
| `group_subject_changed`             |
| `group_description_changed`         |
| `group_icon_changed`                |
| `group_icon_deleted`                |
| `group_invite_link_revoked`         |
| `group_user_changed_number`         |
| `group_error_fetching_photo`        |
| `group_error_adding_users`          |
| `group_error_adding_user`           |
| `group_error_full_adding_users`     |
| `group_error_removing_user`         |
| `group_ended`                       |
| `group_error_blocked_adding_user`   |

##### Sample user joined a group

```json
{
  "type": "whatsapp",
  "notifications": [
    {
      "from": "46732619989",
      "message_id": "ACELGAWCUJAqFWkiU4IDM3N5cy0xODA1ODI1MDkwMi0xNTY5MjI1MzgyQGcudXMtMTU3MTE0NzMxMjY3Ni1hZGQtMBGGRnMmGZif",
      "message": {
        "type": "group_event",
        "body": "\u200e\u200e+46 73 200 11 22 was added\u200e",
        "members": [
          "46732001122"
        ],
        "event_type": "group_user_joined",
        "in_group": "group:18058333222-1569225382"
      },
      "timestamp": "2019-10-15T13:48:32Z",
      "in_group": "group:18058333222-1569225382",
      "to": "demo1"
    }
  ]
}
```

**Error notification**

In case an error happens, an error notification can be sent.

|Name       | Description                                                            | JSON Type |
|-----------|----------------------------------------------------------------------- |-----------|
|type       | Fixed value `error`.                                                   | String    |
|details    | A description of the error.                                            | String    |

##### Error notification

```json
{
    "type": "whatsapp",
    "notifications": [
        {
            "from": "46702291874",
            "message_id": "01E7T5K8CREY9K0HGZW3ME1F26ABGGRnAiI1JfAhC5kP7rPIamw3JHBDfxEzvm",
            "message": {
                "type": "error",
                "details": "Unexpected callback contents received. Remember to add quick reply buttons to the request payload when sending the message template, even if no quick reply button payload is added."
            },
            "timestamp": "2020-05-08T12:54:07Z",
            "to": "bot_id",
            "replying_to": {
                "from": "447537918329"
            }
        }
    ]
}
```
