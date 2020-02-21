---
title: Send WhatsApp Messages
excerpt: Send messages via WhatsApp with Sinch WhatsApp API. Get more information here.
next:
  pages:
    - whatsapp-group-management
---
The message endpoint is used as the primary endpoint of the API and this is where all the messages are sent through.

**WhatsApp message flow**

![image](images/whatsapp-msg-flow.png)

1.  Customer opt-in is essential before sending any messages.
2.  Businesses can only start a conversation with a defined message template.
3.  Once you get a reply from your customer, a *customer care session* starts. You can then send “session” rich content messages for 24 hours.
4.  Every time a customer replies to one of your messages, a new 24-hour cycle starts.
5.  If a “session” expires, you’ll need to re-initiate a conversation, starting with a defined message template again.
6.  Customers can start a rich content conversation with a business at any time  
    -   this opens up a new 24-hour session.


## Send a WhatsApp message

#### Request
`POST whatsapp/v1/{bot-id}/messages`

JSON object parameters:

| Name    | Description                                                          | JSON Type    | Default    | Constraints           | Required |
| ------- | -------------------------------------------------------------------- | ------------ | ---------- | --------------------- | :------: |
| to      | List of MSISDNs and group IDs                                        | String array | N/A        | 1 to 20 elements      | Yes      |
| message | Message object                                                       | Object       | N/A        | Valid Message object  | Yes      |
| callback| Callback URL to overwrite configured callback URL for status updates | String       | N/A        | Valid URL             | No       |

#### Response

`201 Created`

The response body is a JSON object with the same format as a [delivery report callback](doc:whatsapp-callback#section-delivery-report-callback).

```json
{
  "type": "whatsapp",
  "statuses":[
    {
      "message_id":"01DPNXZ0WCF9XD19MH84XD0P62",
      "recipient":"+46732001122",
      "status":"success",
      "state":"queued"
    },
  ]
}
```

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors).

### Message object types

The types of messages that can be sent are one of the following:


#### Template message

Accepted language codes can be found in the [introduction](doc:whatsapp-introduction#section-supported-language-codes).

JSON object parameters:

| Name          | Description                                                          | JSON Type    | Default    | Constraints           | Required |
| ------------- | -------------------------------------------------------------------- | ------------ | ---------- | --------------------- | :------: |
| type          | Constant value `template`.                                            | String       | N/A        | N/A                   | Yes      |
| template_name | Name of the template.                                                 | String       | N/A        | N/A                   | Yes      |
| language      | Language to send the template in.                                     | String       | `en`       | Language codes and locales (e.g `en`, `en_us`) | No       |
| params        | Parameters to inject into the template.                              | String array | N/A        | This parameter can only be used for template messages with only a body of text. | No      |
| header_params | Parameters to inject into the header of the template.                | String array | N/A        | N/A                   | No      |
| body_params | Parameters to inject into the body of the template.                    | String array | N/A        | N/A                   | No      |
| media | An object describing the document or image to include in the header of the template. The objects are the same as described under Image message and Document message below, except that the `caption` parameter is not allowed. Also see the note below. For a message without media, set the media type to `text`.       | String array | N/A        | N/A                   | No      |
| ttl           | Time to live of the template message. If the receiver has not opened the template message before the time to live expires, the message will be deleted and a failed callback will be sent. The time to live can be specified in ISO-8601 Duration format or in seconds as a string. | String       | 30 Days    | See description | No      |

> **Note**
>
> The `caption` parameter is not supported for media in template messages. For document media, the `filename` parameter can be used to describe the file. If the `filename` parameter is not explicitly used, it will take the default value "Filename".


```json
{
  "to": [
    "46732001122",
    "group:447506616260-1565342732"
  ],
  "message": {
    "type": "template",
    "template_name": "sinch_test_greeting",
    "language": "en",
    "params": [
      "Nick"
    ],
    "ttl": "P1D"
  }
}

```

```json
{
  "to": [
    "46732001122",
    "group:447506616260-1565342732"
  ],
  "message": {
    "type": "template",
    "template_name": "some_template_name",
    "language": "en",
    "header_params": [
      "some_first_parameter",
      "some_second_parameter",
      "some_third_parameter"
    ],
    "body_params": [
      "some_first_parameter",
      "some_second_parameter"
    ],
    "media" : {
      "type": "image",
      "url": "https://www.example.com/some_image.jpg",
      "provider": "some_provider_name"
    },
    "ttl": "P1D"
  }
}
```

```json
{
  "to": [
    "46732001122",
    "group:447506616260-1565342732"
  ],
  "message": {
    "type": "template",
    "template_name": "some_template_name",
    "language": "en",
    "header_params": [
      "some_parameter"
    ],
    "body_params": [
      "some_first_parameter",
      "some_second_parameter"
    ],
    "media" : {
      "type": "text"
    }
  }
}
```

#### Text message

JSON object parameters:

| Name        | Description                                                          | JSON Type    | Default    | Constraints           | Required |
| ----------- | -------------------------------------------------------------------- | ------------ | ---------- | --------------------- | :------: |
| type        | Constant value `text`                                                | String       | N/A        | N/A                   | Yes      |
| preview_url | Message object                                                       | Boolean      | false      | `true` or `false`     | No       |
| text        | The text message content                                             | String       | N/A        | Valid URL             | Yes      |

```json
{
  "to": [
    "46732001122",
    "group:447506616260-1565342732"
  ],
  "message": {
    "type": "text",
    "preview_url": false,
    "text": "Greetings from Sinch"
  }
}
```

#### Image message

> **Note**
>
> Any media file sent through the Sinch WhatsApp API can be at most 100.0 mb

Accepted content types can be found in the [introduction](doc:whatsapp-introduction#section-accepted-media-types).

JSON object parameters:

| Name        | Description                                                               | JSON Type    | Default    | Constraints           | Required |
| ----------- | ------------------------------------------------------------------------- | ------------ | ---------- | --------------------- | :------: |
| type        | Constant value `image`                                                    | String       | N/A        | N/A                   | Yes      |
| url         | Public url of the image file. Should be either HTTP or HTTPS link.        | String       | N/A        | Accepted Content-Type header | Yes      |
| caption     | Optional caption that will be displayed underneath the image.             | String       | None       | N/A                   | No       |
| provider    | Optional name of a provider to be used when trying to download the file.  | String       | None       | N/A                   | No       |

```json
{
  "to": [
    "46732001122",
    "group:447506616260-1565342732"
  ],
  "message": {
    "type": "image",
    "url": "https://example.com/image.jpg",
    "caption": "Example Image"
  }
}
```

#### Video message

> **Note**
>
> Any media file sent through the Sinch WhatsApp API can be at most 100.0 mb

Accepted content types can be found in the [introduction](doc:whatsapp-introduction#section-accepted-media-types).

JSON object parameters:

| Name        | Description                                                               | JSON Type    | Default    | Constraints           | Required |
| ----------- | ------------------------------------------------------------------------- | ------------ | ---------- | --------------------- | :------: |
| type        | Constant value `video`                                                    | String       | N/A        | N/A                   | Yes      |
| url         | Public url of the video file (mp4). Should be either HTTP or HTTPS link.  | String       | N/A        | Accepted Content-Type header| Yes      |
| caption     | Optional caption that will be displayed underneath the video.             | String       | None       | N/A                   | No       |
| provider    | Optional name of a provider to be used when trying to download the file.  | String       | None       | N/A                   | No       |

```json
{
  "to":[
    "46732001122",
    "group:447506616260-1565342732"
  ],
  "message":{
    "type": "video",
    "url": "https://example.com/video.mp4",
    "caption": "Example Video",
    "provider": "your-bearer-provider"
  }
}
```

#### Document message

> **Note**
>
> Any media file sent through the Sinch WhatsApp API can be at most 100.0 mb

Accepted content types can be found in the [introduction](doc:whatsapp-introduction#section-accepted-media-types).

JSON object parameters:

| Name        | Description                                                               | JSON Type    | Default    | Constraints           | Required |
| ----------- | ------------------------------------------------------------------------- | ------------ | ---------- | --------------------- | :------: |
| type        | Constant value `document`                                                 | String       | N/A        | N/A                   | Yes      |
| url         | Public url of the document file. Should be either HTTP or HTTPS link.     | String       | N/A        | Accepted Content-Type header| Yes      |
| filename    | Optional parameter that describes the filename of the document.           | String       | None       | N/A                   | No       |
| caption     | Optional caption that will be displayed as the document title.            | String       | None       | N/A                   | No       |
| provider    | Optional name of a provider to be used when trying to download the file.  | String       | None       | N/A                   | No       |

```json
{
  "to": [
    "46732001122",
    "group:447506616260-1565342732"
  ],
  "message": {
    "type": "document",
    "url": "https://example.com",
    "caption": "Example study",
    "filename": "document.pdf"
  }
}
```

#### Audio message

> **Note**
>
> Any media file sent through the Sinch WhatsApp API can be at most 100.0 mb

Accepted content types can be found in the [introduction](doc:whatsapp-introduction#section-accepted-media-types).

JSON object parameters:

| Name        | Description                                                               | JSON Type    | Default    | Constraints           | Required |
| ----------- | ------------------------------------------------------------------------- | ------------ | ---------- | --------------------- | :------: |
| type        | Constant value `audio`                                                    | String       | N/A        | N/A                   | Yes      |
| url         | Public url of the audio file. Should be either HTTP or HTTPS link.        | String       | N/A        | Accepted Content-Type header| Yes      |
| provider    | Optional name of a provider to be used when trying to download the file.  | String       | None       | N/A                   | No       |

```json
{
  "to": [
    "46732001122",
    "group:447506616260-1565342732"
  ],
  "message": {
    "type": "audio",
    "url": "https://example.com/song.mp3"
  }
}
```


#### Location message

JSON object parameters:

| Name        | Description                                                          | JSON Type    | Default    | Constraints           | Required |
| ----------- | -------------------------------------------------------------------- | ------------ | ---------- | --------------------- | :------: |
| type        | Constant value `location`                                            | String       | N/A        | N/A                   | Yes      |
| lat         | The latitude position as a float number.                             | Number       | N/A        | [-90, 90]             | Yes      |
| lng         | The longitude position as a float number.                            | Number       | N/A        | [-180, 180]           | Yes      |
| name        | The name for the location. Will be displayed in the message.         | String       | N/A        | N/A                   | No       |
| address     | The address for the location. Will be displayed in the message.      | String       | N/A        | N/A                   | No       |

```json
{
  "to": [
    "46732001122",
    "group:447506616260-1565342732"
  ],
  "message": {
    "type": "location",
    "lat": 55.7047,
    "lng": 13.191,
    "name": "Sinch Ideon Lund",
    "address": "Scheelevägen 17"
  }
}
```
#### Contacts message

JSON object parameters:

| Name        | Description                                                          | JSON Type    | Default    | Constraints           | Required |
| ----------- | -------------------------------------------------------------------- | ------------ | ---------- | --------------------- | :------: |
| type        | Constant value `contacts`                                            | String       | N/A        | N/A                   | Yes      |
| contacts    | List of contact cards                                                | Object array | N/A        | Valid contact cards   | Yes      |

```json
{
  "to": [
    "46732001122",
    "group:447506616260-1565342732"
  ],
  "message": {
    "type": "contacts",
    "contacts": [
      {
        "addresses": [
          {
            "city": "Menlo Park",
            "country": "United States",
            "country_code": "us",
            "state": "CA",
            "street": "1 Hacker Way",
            "type": "HOME",
            "zip": "94025"
          }
        ],
        "birthday": "2012-08-18",
        "emails": [
          {
            "email": "test@fb.com",
            "type": "WORK"
          }
        ],
        "name": {
          "first_name": "John",
          "formatted_name": "John Smith",
          "last_name": "Smith"
        },
        "org": {
          "company": "WhatsApp",
          "department": "Design",
          "title": "Manager"
        },
        "phones": [
          {
            "phone": "+1 (650) 555-1234",
            "type": "WORK",
            "wa_id": "16505551234"
          }
        ],
        "urls": [
          {
            "url": "https://www.facebook.com",
            "type": "WORK"
          }
        ]
      }
    ]
  }
}
```

#### Sticker message

> **Note**
>
> Stickers are required to have width 512 pixels and height 512 pixels. The maximum size allowed is 100 KB.

Accepted content types can be found in the [introduction](doc:whatsapp-introduction#section-accepted-media-types).

JSON object parameters:

| Name        | Description                                                               | JSON Type    | Default    | Constraints                  | Required |
| ----------- | ------------------------------------------------------------------------- | ------------ | ---------- | ---------------------------- | :------: |
| type        | Constant value `sticker`                                                  | String       | N/A        | N/A                          | Yes      |
| url         | Public url of the sticker file. Should be either HTTP or HTTPS link.      | String       | N/A        | Accepted Content-Type header | Yes      |
| provider    | Optional name of a provider to be used when trying to download the file.  | String       | None       | N/A                          | No       |

```json
{
  "to": [
    "46732001122",
    "group:447506616260-1565342732"
  ],
  "message": {
    "type": "sticker",
    "url": "https://example.com/sticker.webp"
  }
}
```
