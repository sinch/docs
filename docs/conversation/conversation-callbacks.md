---
title: Conversation API Callbacks
excerpt: >-
  Registering webhooks and listening to callbacks from Sinch Conversation API.
hidden: false
---

## Webhook Management! <span class="betabadge">Beta</span>

Conversation API delivers contact messages, delivery receipts for app messages and various notifications through callbacks.
API clients can create fine-grained subscriptions for up-to 5 endpoints (webhooks) per Conversation API app
through the [Sinch Portal](https://dashboard.sinch.com/convapi/apps) or using **/webhooks** management endpoint.
Each webhook represents a subscription for events defined by a list of triggers. The events are delivered by Conversation API
to the webhook target URL. The callbacks are signed with the webhook secret if such is provided. The signature can be
used to verify the authenticity and integrity of the callbacks.

### Webhook Triggers

Each webhook can subscribe to one or more of the following triggers:

- `MESSAGE_INBOUND` - subscribe to inbound messages from end users on the underlying channels.
- `EVENT_INBOUND` - subscribe to inbound events for e.g., composing events, from end users on the underlying channels.
- `MESSAGE_DELIVERY` - subscribe to delivery receipts for app message sent to channels.  
- `EVENT_DELIVERY` - subscribe to delivery receipts for an event sent to channels.
- `CONVERSATION_START` - subscribe to conversation started events.
- `CONVERSATION_STOP` - subscribe to conversation stopped events.
- `CONTACT_CREATE` - subscribe to contact created events.
- `CONTACT_DELETE` - subscribe to contact deleted events.
- `CONTACT_MERGE` - subscribe to contact merged events.
- `CAPABILITY` - this trigger is used to receive the results of asynchronous capability checks.
- `OPT_IN` - subscribe to opt-in events.
- `OPT_OUT` - subscribe to opt-out events.
- `UNSUPPORTED` - subscribe to receive channel callbacks which are not natively supported by the Conversation API.

The snipped below demonstrates how to create a webhook using the management API.
The webhook registers a POST capable endpoint with URL `{{WEBHOOK_URL}}` to receive
inbound messages and delivery receipts for app with ID `{{APP_ID}}` and project with ID `{{PROJECT_ID}}`.

```curl
curl -X POST \
'https://eu.conversation.api.sinch.com/v1/projects/{{PROJECT_ID}}/webhooks' \
-H 'Content-Type: application/json' --header 'Authorization: Bearer {{ACCESS_TOKEN}}' \
-d '{
	"app_id": "{{APP_ID}}",
	"target": "{{WEBHOOK_URL}}",
	"target_type": "HTTP",
	"triggers": ["MESSAGE_DELIVERY", "MESSAGE_INBOUND"]
}'
```

## Validating Callbacks

Conversation API callbacks triggered by a registered **webhook**, with a secret set, will contain the following headers:

| Field                               | Description                                                                                            |
| ----------------------------------- | ------------------------------------------------------------------------------------------------------ |
| x-sinch-webhook-signature-timestamp | Timestamp, in UTC, of when the signature was computed                                                  |
| x-sinch-webhook-signature-nonce     | A unique nonce that can be used to protect against reply attacks                                       |
| x-sinch-webhook-signature-algorithm | The HMAC signature algorithm that was used to compute the signature. For now it is set to HmacSHA256.  |
| x-sinch-webhook-signature           | The signature of the raw HTTP body, the timestamp, and the nonce as computed by the Conversation API.  |

The receiver of signed callbacks should perform the following steps:

1. Compute the signature using `base64(HMAC(secret, signed_data))` where `signed_data` is UTF-8 encoded dot-separated concatenation of the raw HTTP body, the nonce and the timestamp in that order:

  > raw callback body \|\| . \|\| x-sinch-webhook-signature-nonce \|\| . \|\| x-sinch-webhook-signature-timestamp

  ***Please note:*** do not disable padding when creating the `base64` encoding of the signature.

2. Compare the computed signature with the one included in the `x-sinch-webhook-signature` header. The callback should be discarded in case of mismatching signatures.
3. Make sure that the timestamp is within an acceptable time window. I.e., the difference between the current time, in UTC, and the timestamp in the headers is sufficiently small.
4. Make sure that the nonce is unique. This could be done by keeping track of previously received nonces.

## Callback Format

Each callback dispatched by Conversation API has a JSON payload with the following top-level properties:

| Field         | Type               | Description                                                                                |
| ------------- | ------------------ | ------------------------------------------------------------------------------------------ |
| project_id    | string             | The project ID of the app which has subscribed for the callback.                           |
| app_id        | string             | Id of the subscribed app.                                                                  |
| accepted_time | ISO 8601 timestamp | Timestamp marking when the channel callback was accepted/received by the Conversation API. |
| event_time    | ISO 8601 timestamp | Timestamp of the event as provided by the underlying channels.                             |

Each specific callback type adds additional properties to the payload which are described in detail in the
section below.

### Inbound Message

This callback delivers contact (end-user) messages to the API clients. The message details are given
in a top level `message` field. It is a JSON object with the following properties:

| Field            | Type               | Description                                                                                                                             |
| ---------------- | ------------------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| id               | string             | The message ID.                                                                                                                         |
| direction        | string             | The direction of the message. It is always `TO_APP` for contact messages.                                                               |
| contact_message  | object             | The content of the message. See [Contact Message](doc:conversation-callbacks#contact-message) for details.                              |
| channel_identity | object             | The identity of the contact in the underlying channel. See [Channel Identity](doc:conversation-callbacks#channel-identity) for details. |
| conversation_id  | string             | The ID of the conversation this message is part of.                                                                                     |
| contact_id       | string             | The ID of the contact.                                                                                                                  |
| metadata         | string             | Metadata associated with the contact.                                                                                                   |
| accept_time      | ISO 8601 timestamp | Timestamp marking when the channel callback was received by the Conversation API.                                                       |

#### Example of Inbound Message Callback

```json
{
  "app_id": "01EB37HMH1M6SV18ABNS3G135H",
  "accepted_time": "2020-11-16T08:17:44.993024Z",
  "event_time": "2020-11-16T08:17:42.814Z",
  "project_id": "c36f3d3d-1523-4edd-ae42-11995557ff61",
  "message": {
    "id": "01EQ8235TD19N21XQTH12B145D",
    "direction": "TO_APP",
    "contact_message": {
      "text_message": {
        "text": "Hi!"
      }
    },
    "channel_identity": {
      "channel": "MESSENGER",
      "identity": "2742085512340733",
      "app_id": "01EB37HMH1M6SV18ABNS3G135H"
    },
    "conversation_id": "01EQ8172WMDB8008EFT4M30481",
    "contact_id": "01EQ4174TGGY5B1VPTPGHW19R0",
    "metadata": "",
    "accept_time": "2020-11-16T08:17:43.915829Z"
  }
}
```

#### Contact Message

The table below shows the properties of the `contact_message` field in inbound message callbacks:

| Field         | Type               | Description                                                                                                                                       |
| ------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| reply_to      | object             | Optional. Included if the contact message is a response to a previous app message. See [ReplyTo](doc:conversation-callbacks#replyto) for details. |

It also contains one of the following properties depending on the type of message: `text_message`, `media_message`, `location_message`, `choice_response_message`, `media_card_message`.
All of these properties are JSON objects described in the sections below.

##### Text Message

| Field         | Type               | Description                               |
| ------------- | ------------------ | ----------------------------------------- |
| text          | string             | The text included in the contact message. |

##### Media Message

| Field         | Type               | Description           |
| ------------- | ------------------ | --------------------- |
| url           | string             | The URL of the media. |

##### Location Message

| Field         | Type               | Description                                                                                                                      |
| ------------- | ------------------ | -------------------------------------------------------------------------------------------------------------------------------- |
| title         | string             | The title shown above the the location. The title is clickable in some cases.                                                    |
| coordinates   | object             | Geo coordinates of the location specified in the message. See [Coordinates](doc:conversation-callbacks#coordinates) for details. |
| label         | string             | Label or name for the position.                                                                                                  |

###### Coordinates

| Field         | Type               | Description                             |
| ------------- | ------------------ | --------------------------------------- |
| latitude      | float              | Latitude of the geographic coordinate.  |
| longitude     | float              | Longitude of the geographic coordinate. |

##### Choice Response Message

The choice response message represents a contact response to a choice message.

| Field         | Type               | Description                                                                                              |
| ------------- | ------------------ | -------------------------------------------------------------------------------------------------------- |
| message_id    | string             | The message id containing the choice.                                                                    |
| postback_data | string             | The postback data if defined in the selected choice. Otherwise the default is `message_id_{text, title}` |

##### Media Card Message

Contact Message containing media and caption.

| Field         | Type               | Description                                                                                              |
| ------------- | ------------------ | -------------------------------------------------------------------------------------------------------- |
| url           | string             | The URL of the media.                                                                                    |
| caption       | string             | Caption for the media, if supported by channel.                                                          |

##### ReplyTo

| Field         | Type               | Description                                                       |
| ------------- | ------------------ | ----------------------------------------------------------------- |
| message_id    | object             | The id of the message that this contact message is a response to. |

#### Channel Identity

The table below shows the properties of the `channel_identity` field in Conversation API callbacks:

| Field         | Type               | Description                                                                                                                                                                              |
| ------------- | ------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| channel       | string             | Conversation API identifier of the underlying channel e.g., `SMS`, `RCS`, `MESSENGER`.                                                                                                   |
| identity      | string             | The channel identity e.g., a phone number for SMS, WhatsApp and Viber Business.                                                                                                          |
| app_id        | string             | The app ID if this is an app-scoped channel identity. Empty string otherwise. See [App-scoped Channel Identities](doc:conversation-callbacks#app-scoped-channel-identities) for details. |

##### App-scoped Channel Identities

Currently, Facebook Messenger and Viber Bot channels are using app-scoped channel identities
which means contacts will have different channel identities for different apps.
For example, Facebook Messenger uses PSIDs (Page-Scoped IDs) as channel identities.
The app_id is pointing to the app linked to the Facebook page for which this PSID is issued.

### Inbound Event

This callback delivers channel events such as *composing* to the API clients. The message details are given
in a top level `event` field. It is a JSON object with the following properties:

| Field            | Type               | Description                                                                                                                             |
| ---------------- | ------------------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| direction        | string             | The direction of the event. It is always `TO_APP` for contact events.                                                                   |
| contact_event    | object             | The content of the event. See [Contact Event](doc:conversation-callbacks#contact-event) for details.                                    |
| channel_identity | object             | The identity of the contact in the underlying channel. See [Channel Identity](doc:conversation-callbacks#channel-identity) for details. |
| contact_id       | string             | The ID of the contact.                                                                                                                  |
| accept_time      | ISO 8601 timestamp | Timestamp marking when the channel callback was received by the Conversation API.                                                       |

#### Example of Inbound Event Callback

```json
{
  "app_id": "01EB37HMH1M6SV18ABNS3G135H",
  "accepted_time": "2020-11-16T08:17:44.993024Z",
  "event_time": "2020-11-16T08:17:42.814Z",
  "project_id": "c36f3d3d-1523-4edd-ae42-11995557ff61",
  "event": {
    "direction": "TO_APP",
    "contact_event": {
      "composing_event": {}
    },
    "channel_identity": {
      "channel": "RCS",
      "identity": "123456789",
      "app_id": ""
    },
    "contact_id": "01EQ4174TGGY5B1VPTPGHW19R0",
    "accept_time": "2020-11-16T08:17:43.915829Z"
  }
}
```

#### Contact Event

The table below shows the properties of the `contact_event` field in inbound event callbacks:

| Field           | Type               | Description                                                                                                                                       |
| --------------- | ------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------- |
| composing_event | object             | Empty object denoting the contact is composing a message.                                                                                         |

### Message Delivery Receipt

This callback notifies the API clients about status changes of already sent app message. The delivery receipt details are given
in a top level `message_delivery_report` field. It is a JSON object with the following properties:

| Field            | Type               | Description                                                                                                                             |
| ---------------- | ------------------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| message_id       | string             | The ID of the app message.                                                                                                              |
| conversation_id  | string             | The ID of the conversation the app message is part of.                                                                                  |
| status           | string             | The delivery status. See [Delivery Status](doc:conversation-callbacks#delivery-status) for details.                                     |
| channel_identity | object             | The identity of the contact in the underlying channel. See [Channel Identity](doc:conversation-callbacks#channel-identity) for details. |
| contact_id       | string             | The ID of the contact.                                                                                                                  |
| reason           | object             | Error reason if status is `FAILED` or `SWITCHING_CHANNEL`. See [Reason](doc:conversation-callbacks#reason) for details.                 |
| metadata         | string             | Metadata specified when sending the message if any.                                                                                     |

#### Example of Message Delivery Callback

The example below shows a receipt for successfully enqueued message with
ID `01EQBC1A3BEK731GY4YXEN0C2R` on `MESSENGER` channel:

```json
{
  "app_id": "01EB37HMH1M6SV18BSNS3G135H",
  "accepted_time": "2020-11-17T15:09:11.659Z",
  "event_time": "2020-11-17T15:09:13.267185Z",
  "project_id": "c36f3d3d-1513-2edd-ae42-11995557ff61",
  "message_delivery_report": {
    "message_id": "01EQBC1A3BEK731GY4YXEN0C2R",
    "conversation_id": "01EPYATA64TMNZ1FV02JKF12JF",
    "status": "QUEUED_ON_CHANNEL",
    "channel_identity": {
      "channel": "MESSENGER",
      "identity": "2734085512340733",
      "app_id": "01EB27HMH1M6SV18ASNS3G135H"
    },
    "contact_id": "01EXA07N79THJ20WSN6AS30TMW",
    "metadata": ""
  }
}
```

When the sending of the message failed the receipt includes a reason object
describing the error:

```json
{
  "app_id": "01EB37HMH1M6SV18BSNS3G135H",
  "accepted_time": "2020-11-17T16:01:06.374Z",
  "event_time": "2020-11-17T16:01:07Z",
  "project_id": "c36f3d3d-1513-4edd-ae42-11995557ff61",
  "message_delivery_report": {
    "message_id": "01EQBF0BT63J7S1FEKJZ0Z08VD",
    "conversation_id": "01EQBCFQR3EGE60P42H6H1117J",
    "status": "FAILED",
    "channel_identity": {
      "channel": "WHATSAPP",
      "identity": "12345678910",
      "app_id": ""
    },
    "contact_id": "01EXA07N79THJ20WSN6AS30TMW",
    "reason": {
      "code": "OUTSIDE_ALLOWED_SENDING_WINDOW",
      "description": "The underlying channel reported: Message failed to send because more than 24 hours have passed since the customer last replied to this number"
    },
    "metadata": ""
  }
}
```

#### Delivery Status

The field `status` is included in delivery report callbacks and shows the status of
the message or event delivery. The `status` field can have the following values:

- `QUEUED`
- `QUEUED_ON_CHANNEL`
- `DELIVERED`
- `READ`
- `FAILED`
- `SWITCHING_CHANNEL`

Each message and event sent by the API clients to contacts go through the following states:

1. The message is successfully queued in Conversation API. It has status `QUEUED`.
2. The message is successfully dispatched to an underlying channel. It has status `QUEUED_ON_CHANNEL`.
3. A delivery report is sent from the underlying channel detailing the delivery status on the channel. Depending on the channel response and the processing state of the message the message is transitioned to one of following states:

    3.1 `DELIVERED` - the channel delivery report indicated the message has reached the end user. Some channels can later send new delivery report with status `READ`.

    3.2 `READ` - the channel delivery report indicated the message was seen or read by the end user. This is a terminal state. There cannot be more state changes after the message is in `READ` state. Some channel will omit sending `DELIVERED` when the message is seen immediately by the user. In such cases the `DELIVERED` status is implicit.

    3.3 `FAILED` - the channel delivery report indicated the message delivery failed and there are no more channels to try according to the channel priority defined in the send request. This is a terminal state. There cannot be more state changes after the message is `FAILED` state.

    3.4 `SWITCHING_CHANNEL` - the channel delivery report indicated the message delivery failed. However, there are more channels which Conversation API can try to send the message according to the channel priority defined in the send request.

***Please note:*** There are no callbacks sent for status `QUEUED` since this state is already known
by the API clients when they receive successful response from the **/messages:send** endpoint.

#### Reason

The `reason` field in `FAILED` or `SWITCHING_CHANNEL` delivery report callbacks provides information for the reason of the failure.
The table below shows the properties of the `reason` field:

| Field           | Type               | Description                                                                                                    |
| --------------- | ------------------ | -------------------------------------------------------------------------------------------------------------- |
| code            | string             | High-level classification of the error. See [Error Codes](doc:conversation-callbacks#error-codes) for details. |
| description     | string             | A description of the reason.                                                                                   |

##### Error Codes

Conversation API provides a set of common reason codes which can be used to automate the error handling by the API clients.
The codes are as follow:

- ***RATE_LIMITED*** - the message or event was not sent due to rate limiting.
- ***RECIPIENT_INVALID_CHANNEL_IDENTITY*** - the channel recipient identity was malformed.
- ***RECIPIENT_NOT_REACHABLE*** - it was not possible to reach the contact, or channel recipient identity, on the channel.
- ***RECIPIENT_NOT_OPTED_IN*** - the contact, or channel recipient identity, has not opt-ed in on the channel.
- ***OUTSIDE_ALLOWED_SENDING_WINDOW*** - the allowed sending window has expired. See the [channel support documentation](doc:conversation-channel-support) for more information about how the sending window works for the different channels.
- ***CHANNEL_FAILURE*** - the channel failed to accept the message. The Conversation API performs multiple retries in case of transient errors.
- ***CHANNEL_BAD_CONFIGURATION*** - the channel configuration of the app is wrong. The bad configuration caused the channel to reject the message.
- ***CHANNEL_CONFIGURATION_MISSING*** - the referenced app has no configuration for the channel.
- ***MEDIA_TYPE_UNSUPPORTED*** - indicates that the sent message had unsupported media type.
- ***MEDIA_TOO_LARGE*** - some of the referenced media files are too large. Please read the See the [channel support documentation](doc:conversation-channel-support) to find out the limitations on file size that the different channels impose.
- ***MEDIA_NOT_REACHABLE*** - the provided media link was not accessible from the Conversation API or from the underlying channels. Please make sure that the media file is accessible.
- ***NO_CHANNELS_LEFT*** - no channels to try to send the message to. This error will occur if all applicable channels have been attempted.
- ***TEMPLATE_NOT_FOUND*** - the referenced template was not found.
- ***TEMPLATE_INSUFFICIENT_PARAMETERS*** - not all parameters defined in the template were provided when sending a template message.
- ***TEMPLATE_NON_EXISTING_LANGUAGE_OR_VERSION*** - the selected language, or version, of the referenced template did not exist. Please check the available versions and languages of the template.
- ***DELIVERY_TIMED_OUT*** - the message or event delivery failed due to a channel-imposed timeout.
- ***DELIVERY_REJECTED_DUE_TO_POLICY*** - the message or event was rejected by the channel due to a policy. Some channels have specific policies that must be met to send a message. See the [channel support documentation](doc:conversation-channel-support) for more information about when this error will be triggered.
- ***CONTACT_NOT_FOUND*** - the provided Contact ID did not exist.
- ***BAD_REQUEST*** - Conversation API validates send requests in two different stages. The first stage is right before the message is enqueued. If this first validation fails the API responds with 400 Bad Request and the request is discarded immediately. The second validation kicks in during message processing and it normally contains channel specific validation rules. Failures during second request validation are delivered as callbacks to MESSAGE_DELIVERY (EVENT_DELIVERY) webhooks with ReasonCode BAD_REQUEST.
- ***UNKNOWN_APP*** - missing app. This error may occur when the app is removed during message processing.
- ***NO_CHANNEL_IDENTITY_FOR_CONTACT*** - the contact has no channel identities for the resolved channel priorities.
- ***CHANNEL_REJECT*** - generic error for channel permanently rejecting a message.
- ***UNKNOWN*** - returned if no other code can be used to describe the encountered error.
- ***INTERNAL_ERROR*** - an internal error occurred. Please save the entire callback if you want to report an error.

### Event Delivery Receipt

This callback notifies the API clients about status changes of already sent app events. The delivery receipt details are given
in a top level `event_delivery_report` field. It is a JSON object with the following properties:

| Field            | Type               | Description                                                                                                                             |
| ---------------- | ------------------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| event_id         | string             | The ID of the app event.                                                                                                                |
| status           | string             | The delivery status. See [Delivery Status](doc:conversation-callbacks#delivery-status) for details.                                     |
| channel_identity | object             | The identity of the contact in the underlying channel. See [Channel Identity](doc:conversation-callbacks#channel-identity) for details. |
| contact_id       | string             | The ID of the contact.                                                                                                                  |
| reason           | object             | Error reason if status is `FAILED` or `SWITCHING_CHANNEL`. See [Reason](doc:conversation-callbacks#reason) for details.                 |
| metadata         | string             | Metadata specified when sending the event if any.                                                                                       |

#### Example of Event Delivery Callback

```json
{
  "app_id": "01EB37HMH1M6SV18BSNS3G135H",
  "accepted_time": "2020-11-17T15:09:11.659Z",
  "event_time": "2020-11-17T15:09:13.267185Z",
  "project_id": "c36f3d3d-1513-2edd-ae42-11995557ff61",
  "event_delivery_report": {
    "event_id": "01EQBC1A3BEK731GY4YXEN0C2R",
    "status": "QUEUED_ON_CHANNEL",
    "channel_identity": {
      "channel": "MESSENGER",
      "identity": "2734085512340733",
      "app_id": "01EB27HMH1M6SV18ASNS3G135H"
    },
    "contact_id": "01EXA07N79THJ20WSN6AS30TMW",
    "metadata": ""
  }
}
```

### Conversation Start

This callback is sent when a new conversation between the subscribed app and a contact is started. The conversation details are given
in a top level `conversation_start_notification` field. It is a JSON object with the following properties:

| Field            | Type               | Description                                |
| ---------------- | ------------------ | ------------------------------------------ |
| conversation     | object             | The properties of the started conversation |

#### Example of Conversation Start Callback

```json
{
  "app_id": "01EB37HMH1M6SV18ASNS3G135H",
  "project_id": "c36f3d3d-1523-4edd-ae42-11995557ff61",
  "conversation_start_notification": {
    "conversation": {
      "id": "01EQ4174WMDB8008EFT4M30481",
      "app_id": "01EB37HMH1M6SF18ASNS3G135H",
      "contact_id": "01BQ8174TGGY5B1VPTPGHW19R0",
      "active_channel": "MESSENGER",
      "active": true,
      "metadata": ""
    }
  }
}
```

### Conversation Stop

This callback is sent when a conversation between the subscribed app and a contact is stopped. The conversation details are given
in a top level `conversation_stop_notification` field. It is a JSON object with the following properties:

| Field            | Type               | Description                                 |
| ---------------- | ------------------ | ------------------------------------------- |
| conversation     | object             | The properties of the stopped conversation. |

#### Example of Conversation Stop Callback

```json
{
  "app_id": "01EB37HMH1M6SV17ASNS3G135H",
  "project_id": "c36f3d3d-1523-4edd-ae42-11995557ff61",
  "conversation_stop_notification": {
    "conversation": {
      "id": "01EPYATZ64TMNZ1FV02JKD12JF",
      "app_id": "01EB37HMH1M6SV17ASNS3G135H",
      "contact_id": "01EKA07N79THJ20WAN6AS30TMW",
      "last_received": "2020-11-17T15:09:12Z",
      "active_channel": "MESSENGER",
      "active": false,
      "metadata": ""
    }
  }
}
```

### Contact Create

This callback is sent when a new contact is created. The contact details are given
in a top level `contact_create_notification` field. It is a JSON object with the following properties:

| Field            | Type               | Description                            |
| ---------------- | ------------------ | -------------------------------------- |
| contact          | object             | The properties of the created contact. |

#### Example of Contact Create Callback

```json
{
  "app_id": "01EB37KMH1M6SV18ASNS3G135H",
  "accepted_time": "2020-11-17T15:36:28.155494Z",
  "project_id": "c36f3a3d-1513-4edd-ae42-11995557ff61",
  "contact_create_notification": {
    "contact": {
      "id": "01EQBDK8771J6A1FV8MQPE1XAR",
      "channel_identities": [
        {
          "channel": "VIBER",
          "identity": "9KC0p+pi4zPGFO99ACDxdQ==",
          "app_id": "01EB37KMH1M6SV18ASNS3G135H"
        }
      ],
      "channel_priority": [
        "VIBER"
      ],
      "display_name": "Unknown",
      "email": "",
      "external_id": "",
      "metadata": "",
      "language": "UNSPECIFIED"
    }
  }
}
```

### Contact Delete

This callback is sent when a contact is deleted. The contact details are given
in a top level `contact_delete_notification` field. It is a JSON object with the following properties:

| Field            | Type               | Description                            |
| ---------------- | ------------------ | -------------------------------------- |
| contact          | object             | The properties of the deleted contact. |

#### Example of Contact Delete Callback

```json
{
  "app_id": "01EB37KMH1M6SV18ASNS3G135H",
  "accepted_time": "2020-11-17T15:44:33.517073Z",
  "project_id": "c36f3a3d-1513-4edd-ae42-11995557ff61",
  "contact_delete_notification": {
    "contact": {
      "id": "01EQBDK8771J6A1FV8MQPE1XAR",
      "channel_identities": [
        {
          "channel": "VIBER",
          "identity": "9KC0p+pi4zPGFO99ACDxdQ==",
          "app_id": "01EB37HMH1M6SV13ASNS3G135H"
        }
      ],
      "channel_priority": [
        "VIBER"
      ],
      "display_name": "Unknown",
      "email": "",
      "external_id": "",
      "metadata": "",
      "language": "UNSPECIFIED"
    }
  }
}
```

### Contact Merge

This callback is sent when two contacts are merged. The details of the resulting merged and
deleted contacts are given in a top level `contact_merge_notification` field.
It is a JSON object with the following properties:

| Field             | Type               | Description                                     |
| ----------------- | ------------------ | ----------------------------------------------- |
| preserved_contact | object             | The properties of the resulting merged contact. |
| deleted_contact   | object             | The properties of the deleted contact.          |

#### Example of Contact Merge Callback

```json
{
  "app_id": "01EB37KMH1M6SV18ASNS3G135H",
  "accepted_time": "2020-11-17T15:53:03.457706Z",
  "project_id": "c36f3a3d-1513-4edd-ae42-11995557ff61",
  "contact_merge_notification": {
    "preserved_contact": {
      "id": "01EQBECE7Z4XP21359SBKS1526",
      "channel_identities": [
        {
          "channel": "VIBER",
          "identity": "9KC0p+pi4zPGFO99ACDxdQ==",
          "app_id": "01EB37KMH1M6SV18ASNS3G135H"
        }
      ],
      "channel_priority": [
        "VIBER"
      ],
      "display_name": "Unknown",
      "email": "",
      "external_id": "",
      "metadata": "",
      "language": "UNSPECIFIED"
    },
    "deleted_contact": {
      "id": "01EQBEH7MNEZQC0881A4WS17K3",
      "channel_identities": [
        {
          "channel": "VIBER",
          "identity": "9KC0p+pi4zaGFO99ACDxdQ==",
          "app_id": "01EB37KMH1M6SV18ASNS3G135H"
        }
      ],
      "channel_priority": [
        "VIBER"
      ],
      "display_name": "Unknown",
      "email": "",
      "external_id": "",
      "metadata": "",
      "language": "UNSPECIFIED"
    }
  }
}
```

### Capability Check

This callback is used to deliver the results of the asynchronous capability checks.
The outcome of the capability check is given in a top level `capability_notification` field.
It is a JSON object with the following properties:

| Field                | Type               | Description                                                                                                                             |
| -------------------- | ------------------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| request_id           | string             | ID generated when submitting the capability request. Can be used to detect duplicates.                                                  |
| contact_id           | string             | The ID of the contact.                                                                                                                  |
| channel              | string             | The channel for which the capability lookup was performed.                                                                              |
| identity             | string             | The channel identity e.g., a phone number for SMS, WhatsApp and Viber Business.                                                         |
| capability_status    | string             | Status indicating the recipient's capability on the channel. One of `CAPABILITY_FULL`, `CAPABILITY_PARTIAL` or `NO_CAPABILITY`.         |
| channel_capabilities | string array       | Optional. When status `CAPABILITY_PARTIAL`, a list of channel-specific capabilities reported by the channel if supported.               |
| reason               | object             | Error reason if the capability check failed. See [Reason](doc:conversation-callbacks#reason) for details.                               |

You can get more information about the capability check endpoint provided by Conversation API at [Introduction to Capability](doc:conversation-capability).

#### Example of Capability Check Callback

```json
{
  "app_id": "01EB37KMH2M6SV18ASNS3G135H",
  "accepted_time": "2020-11-17T16:05:51.724083Z",
  "project_id": "",
  "capability_notification": {
    "contact_id": "01EKA07N79THJ20ZSN6AS30TMW",
    "identity": "12345678910",
    "channel": "WHATSAPP",
    "capability_status": "CAPABILITY_FULL",
    "request_id": "01EQBF91XWP9PW1J8EWRYZ1GK2"
  }
}
```

### Opt In

This callback is used to deliver opt-in notifications from the channels or the results of
the asynchronous opt-in registrations done through the */optins:register* endpoint.
The opt-in details are given in a top level `opt_in_notification` field with the following properties:

| Field             | Type               | Description                                                                                                                             |
| ----------------- | ------------------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| request_id        | string             | ID generated when making an opt-in registration request. Can be used to detect duplicates.                                              |
| contact_id        | string             | The ID of the contact which is the subject of the opt-in.                                                                               |
| channel           | string             | The channel of the opt-in.                                                                                                              |
| identity          | string             | The channel identity e.g., a phone number for SMS, WhatsApp and Viber Business.                                                         |
| status            | string             | Status of the opt-in registration. One of `OPT_IN_SUCCEEDED` or `OPT_IN_FAILED`.                                                        |
| error_details     | object             | It is set in case of errors. It contains a single string property `description` containing a human-readable error description.          |

You can get more information about the opt-in endpoint provided by Conversation API at [Opt-In & Opt-Out](doc:conversation-optin).

#### Example of Opt In Callback

```json
{
  "app_id": "01EB37HMH1M6SV18ASNS3G135H",
  "accepted_time": "2020-11-17T16:13:57.052735Z",
  "project_id": "",
  "opt_in_notification": {
    "contact_id": "01EKA07N79THJ20WSN6AS30TMW",
    "channel": "WHATSAPP",
    "identity": "46702471483",
    "status": "OPT_IN_SUCCEEDED",
    "request_id": "01EQBFQWDC9E9A16NJ852S1ATD"
  }
}
```

### Opt Out

This callback is used to deliver opt-out notifications from the channels or the results of
the asynchronous opt-out registrations done through the */optouts:register* endpoint.
The opt-out details are given in a top level `opt_out_notification` field with the following properties:

| Field             | Type               | Description                                                                                                                             |
| ----------------- | ------------------ | --------------------------------------------------------------------------------------------------------------------------------------- |
| request_id        | string             | ID generated when making an opt-out registration request. Can be used to detect duplicates.                                             |
| contact_id        | string             | The ID of the contact which is the subject of the opt-out.                                                                              |
| channel           | string             | The channel of the opt-out.                                                                                                             |
| identity          | string             | The channel identity e.g., a phone number for SMS, WhatsApp and Viber Business.                                                         |
| status            | string             | Status of the opt-out registration. One of `OPT_OUT_SUCCEEDED` or `OPT_OUT_FAILED`.                                                     |
| error_details     | object             | It is set in case of errors. It contains a single string property `description` containing a human-readable error description.          |

You can get more information about the opt-out endpoint provided by Conversation API at [Opt-In & Opt-Out](doc:conversation-optin).

#### Example of Opt Out Callback

```json
{
  "app_id": "01EB37KMH2M6SV18ASNS3G135H",
  "accepted_time": "2020-11-17T16:12:36.165864Z",
  "project_id": "",
  "opt_out_notification": {
    "contact_id": "01EKA07N79THJ20ZSN6AS30TMW",
    "channel": "WHATSAPP",
    "identity": "12345678910",
    "status": "OPT_OUT_SUCCEEDED",
    "request_id": "01EQBFNC9HVGDW1878RD3B15AC"
  }
}
```

### Unsupported Callback

Some of the callbacks received from the underlying channels might be specific to a single channel or
may not have a proper mapping in Conversation API yet. In such cases the callbacks are forwarded as is
all the way to the API clients subscribed to the `UNSUPPORTED` webhook trigger.
The source of the unsupported callback is given
in a top level `unsupported_callback` field. It is a JSON object with the following properties:

| Field             | Type               | Description                                       |
| ----------------- | ------------------ | ------------------------------------------------- |
| channel           | string             | The channel which is the source of this callback. |
| payload           | string             | Normally a JSON payload as sent by the channel.   |

#### Example of Unsupported Callback

```json
{
  "app_id": "01EB37HMF1M6SV18ASNS3G135H",
  "accepted_time": "2020-11-17T15:17:05.723864Z",
  "event_time": "2020-11-17T15:17:05.683253Z",
  "project_id": "c36f3a3d-1513-4edd-ae42-11995557ff61",
  "unsupported_callback": {
    "channel": "MESSENGER",
    "payload": "{\"object\":\"page\",\"entry\":[{\"id\":\"107710160841844\",\"time\":1605626225304,\"messaging\":[{\"sender\":{\"id\":\"107710160841844\"},\"recipient\":{\"id\":\"2744085512340733\"},\"timestamp\":1605626225228,\"message\":{\"mid\":\"m_CE0r2yTXOPe1DG_cepCqA9eapH28NipZLg3HuKNf-MA_Edr55tBwvLiTzhrvjZJWQioE4J4gpETQZBTREc7Hdg\",\"is_echo\":true,\"text\":\"Text message from Sinch Conversation API.\",\"app_id\":477442439539985}}]}]}"
  }
}
```
