---
title: KakaoTalk
excerpt: >-
  Sinch Conversation API KakaoTalk specific configurations and message transcoding.
hidden: false
---

### Conversation API KakaoTalk Support

Conversation API provides support for KakaoTalk channel. To start using KakaoTalk through Conversation API you need to first have KakaoTalk Business Channel ID configured with KakaoTalk then contact your Sinch account manager to obtain a Sender key.

> 📘 Note
>
> Currently Conversation API support only AlimTalk.
> 

#### Channel Configuration

The easiest way to configure your Conversation API **app** with KakaoTalk support is to use [Sinch Portal](https://dashboard.sinch.com/convapi/overview). Just select your **app** and click on "SET UP CHANNEL" beside the KakaoTalk channel.


##### Setup KakaoTalk integration using the API

Sending a KakaoTalk message requires a Conversation API **app** with `channel_credentials` for KAKAOTALK channel. Example **app** is given in the following snippet:

```json
{
  "channel_credentials": [
    {
      "channel": "KAKAOTALK",
      "kakaotalk_credentials": {
        "kakaotalk_plus_friend_id": "{{KAKAOTALK_BUSINESS_CHANNEL_ID}}",
        "kakaotalk_sender_key": "{{KAKAOTALK_SENDER_KEY}}"
      }
    }
  ]
}
```

You need to replace `{{KAKAOTALK_BUSINESS_CHANNEL_ID}}` with your KakaoTalk Business Channel ID and `{{KAKAOTALK_SENDER_KEY}}` with the Sender Key.

Do not forget that for receiving delivery receipts you also need to configure at least one Conversation API webhook which will trigger POST callbacks to the given URL. The most important triggers for your conversation application is:

- MESSAGE_DELIVERY - delivery receipts for business messages

#### Rich Message Support

Conversation API supports rich template messages for KakaoTalk API.

##### Sending Messages

This section provides examples of messaging capabilities of KakaoTalk channel, and how to utilize them using Conversation API generic message format. Please note that for the sake of brevity the JSON snippets do not include the **recipient** and **app_id** which are both required when sending a message.

###### Template messages

Sending a message requires an approved template. Sinch will register template on behalf of the customer. When sending a message you need to specify the template id, recipient, and the set of parameters defined in the template for the recipient. Each key in the dictionary corresponds to a property of a template parameter. Sinch provides possibility to send generic or authentication template messages.

**Generic Template Message**

Below is an example of sending a generic template message with two body parameters. The template id is _package_template_.

Conversation API POST `messages:send`

```json
{
  "message": {
    "template_message": {
      "channel_template": {
        "KAKAOTALK": {
          "template_id": "package_template",
          "parameters": {
            "package_id": "Value of first parameter",
            "expected_delivery": "Value of second parameter"
          }
        }
      }
    }
  }
}
```

**Authentication Template Message**

Authentication messages are threated differently than generic ones - they are delivered faster. 

> 🚧 Warning
>
> Authentication message must contain one of the keywords in the message body (template after replacement):
> 1. auth 
> 2. password
> 3. verif
> 4. にんしょう
> 5. 認証 
> 6. 비밀번호
> 7. 인증
> 
> Otherwise, the message cannot be delivered as authentication messages.

When sending authentication message you must also add an additional parameter under `channel_properties` -> KAKAOTALK_AUTHENTICATION with value set to *true* (which defines it is authentication message, not a generic one).

Below is an example of sending an authentication template message with two body parameters. The template id is _package_template_.

Conversation API POST `messages:send`

```json
{
  "message": {
    "template_message": {
      "channel_template": {
        "KAKAOTALK": {
          "template_id": "package_template",
          "parameters": {
            "package_id": "Value of first parameter",
            "expected_delivery": "Value of second parameter"
          }
        }
      }
    }
  },
  "channel_properties": {
    "KAKAOTALK_AUTHENTICATION": "true"
  },
}
```

##### Receiving Delivery Receipts

Messages sent on KakaoTalk channel can have two statuses: DELIVERED and FAILED.
If the status is FAILED the reason will include more information about the failure.
Below is an example for DELIVERED receipt - DELIVERED and FAILED differ by the
`status` and `reason` only.
Conversation API POST to `MESSAGE_DELIVERY` webhook:

```json
{
  "app_id": "{{app_id}}",
  "accepted_time": "{{accepted_time}}",
  "event_time": "{{event_time}}",
  "project_id": "{{project_id}}",
  "message_delivery_report": {
    "message_id": "{{message_id}}",
    "conversation_id": "{{conversation_id}}",
    "status": "DELIVERED",
    "channel_identity": {
      "channel": "KAKAOTALK",
      "identity": "{{identity}}",
      "app_id": ""
    },
    "contact_id": "{{contact_id}}",
    "metadata": ""
  }
}
```

Below is an example of FAILED delivery (lack of keyword in authentication message).

```json
{
  "app_id": "{{app_id}}",
  "accepted_time": "{{accepted_time}}",
  "event_time": "{{event_time}}",
  "project_id": "{{project_id}}",
  "message_delivery_report": {
    "message_id": "{{message_id}}",
    "conversation_id": "{{conversation_id}}",
    "status": "FAILED",
    "channel_identity": {
      "channel": "KAKAOTALK",
      "identity": "{{identity}}",
      "app_id": ""
    },
    "contact_id": "{{contact_id}}",
    "reason": {
      "code": "TEMPLATE_INSUFFICIENT_PARAMETERS",
      "description": "Authentication message not included in Template, when sending an authentication message.The content must contain auth guide ment.",
      "sub_code": "UNSPECIFIED_SUB_CODE"
    },
    "metadata": ""
  }
}
```

#### Support

KakaoTalk channel is currently open to selected customers on Sinch Portal, 
- to request access, send an email to kakaotalk@sinch.com 
- for beta support, send to ConvAPI_KakaoTalk_beta@sinch.com