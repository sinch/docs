---
title: Opt-In-and-Outs
excerpt: >-
  Read how to enable or disable users to receive business messages via WhatsApp
  via Sinch WhatsApp API.
next:
  pages:
    - whatsapp-callback
  description: Callback
---
All Business initiated conversations via the Sinch WhatsApp Business API must start with an “Opt-In” by the user. This can be collected through any third party channel. For example in an SMS message, In-Line with a Web Form, in an Email, or even via a deep-link in print media.

You can record a [opt-in](doc:whatsapp-opt-in-and-outs#opt-in-endpoint) by the API call described below and once the “Opt-In” is recorded you’ll be able to message that customer via the Sinch WhatsApp Business API.

Businesses must provide a method by which customers may opt-out of receiving future messages from your organization. The [opt-out-endpoint](doc:whatsapp-opt-in-and-outs#opt-out-endpoint-deprecated) should be handled using the API call below.

> 📘 Note
>
> If a customer initiates contact with a WhatsApp Business, this will constitute as a temporary opt-in meaning that the business is allowed to send messages to that customer for 24 hours since the last received message. After that period has expired, a active opt-in is required before the business is allowed to send messages to that customer again.

## Opt-In endpoint

Opt-in numbers to enable the receiving of business messages via WhatsApp. Opting in already opted in numbers
would be ignored but not rejected. To remove previously opted in number use DELETE Request.

#### Request

`POST whatsapp/v1/{bot-id}/provision/optin`
`DELETE whatsapp/v1/{bot-id}/provision/optin`

JSON object parameters:

| Name    | Description                      | JSON Type    | Default    | Constraints           | Required |
| ------- | -------------------------------- | ------------ | ---------- | --------------------- | :------: |
| numbers | List of MSISDNs                  | String array | N/A        | 1 to 20 elements      | Yes      |

**Sample**
```json
{
  "numbers": [
    "46732001122",
    "46732002244"
  ]
}
```

#### Responses

`200 OK`

Empty response body

`400 Bad Request`

There was an error with your request. The body is a JSON object described [here](doc:whatsapp-introduction#http-errors)

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described [here](doc:whatsapp-introduction#http-errors)



## Opt-Out endpoint DEPRECATED

Opt-out number(s) to prevent them from receiving messages from the business. This endpoint is deprecated - use `DELETE whatsapp/v1/{bot-id}/provision/optin` instead

#### request

`POST whatsapp/v1/{bot-id}/provision/optout`

JSON object parameters:

| Name    | Description                      | JSON Type    | Default    | Constraints           | Required |
| ------- | -------------------------------- | ------------ | ---------- | --------------------- | :------: |
| numbers | List of MSISDNs                  | String array | N/A        | 1 to 20 elements      | Yes      |

**Sample**
```json
{
  "numbers": [
    "46732001122",
    "46732002244"
  ]
}
```

#### Responses

`200 OK`

Empty response body

`400 Bad Request`

There was an error with your request. The body is a JSON object described [here](doc:whatsapp-introduction#http-errors)

`401 Unauthorized`

There was an authentication error with your request. Either you're using incorrect credentials or you're attempting to authenticate
in a region where your bot does not reside. The body is a JSON object described [here](doc:whatsapp-introduction#http-errors)



