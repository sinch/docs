---
title: WhatsApp Capability
excerpt: >-
  Read how to discover if your users are capable of receiving messages via
  WhatsApp.
next:
  pages:
    - whatsapp-message
---
## Capability endpoint

The Sinch WhatsApp API offers two endpoints where you can request the WhatsApp capability status for a
list of numbers.

#### Request

`GET whatsapp/v1/{bot-id}/capability`
`POST whatsapp/v1/{bot-id}/capability`

JSON object parameters:

| Name    | Description                      | JSON Type    | Default    | Constraints           | Required |
| ------- | -------------------------------- | ------------ | ---------- | --------------------- | :------: |
| numbers | List of MSISDNs                  | String array | N/A        | 1 to 20 elements      | Yes      |


> **Note**
>
> JSON object should be sent in Request Body although this is GET request


#### Responses

`200 OK`

The response body is a JSON object with the following parameters:

|Name        | Description                         | JSON Type    |
|------------|------------------------------------ |--------------|
|capabilities| List of capability statuses         | Object array |

**Capability status**

|Name        | Description                         | JSON Type    |
|------------|------------------------------------ |--------------|
|msisdn      | Msisdn                              | String       |
|capable     | WhatsApp capability status          | boolean      |

```json
{
  "capabilities": [
    {
      "msisdn":"46732001122",
      "capable": true
    },
    {
      "msisdn":"46732002244",
      "capable": false
    }
  ]
}
```

`400 Bad Request`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors)

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors)

`500 Internal Server Error`

There was an error with your request. The body is a JSON object described in the [introduction](doc:whatsapp-introduction#section-http-errors)

*Request could not be processed* means that the Request rate limit, which is 20 requests sent per second, has been exceeded.
