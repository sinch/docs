---
title: Opt-in & Opt-out
excerpt: >-
  Registering Opt-in & Opt-out requests
hidden: false
---

## Introduction to Opt-in & Opt-out

By using the Opt-in & Opt-out service, it is possible to register an opt-in or an opt-out event for a [**contact**](doc:conversation-keyconcepts#contact) with the underlying channel. In order to execute the request, either the contactId of the contact or its channel recipient identities are required.

In the Beta version of the Conversation API, the Opt-in & Opt-out registration is only supported for WhatsApp channel. To read more about relevant policies go to [**WhatsApp Opt-in and Opt-out**](doc:whatsapp-opt-in-and-outs).

Requests are executed asynchronously, therefore the endpoint immediately returns the registered request. The result of the Opt-in or Opt-out request is posted to the webhook which has an OPT_IN or OPT_OUT trigger respectively. To read more about setting up webhooks go to [**Webhooks**](doc:conversation-keyconcepts#webhook).

### Opt-in

#### Opt-in Request

Below you can find an example to register an Opt-in event.

Conversation API POST `optins:register`

```json
{
  "app_id": "{{APP_ID}}",
  "channels": ["WHATSAPP"],
  "recipient": {
    "contact_id": "{{CONTACT_ID}}"
  }
}
```

Alternatively the contact's channel recipient identities can be used:

```json
{
  "app_id": "{{APP_ID}}",
  "channels": ["WHATSAPP"],
  "recipient": {
    "identified_by": {
      "channel_identities": [
        {
          "channel": "WHATSAPP",
          "identity": "14707864783"
        }
      ]
    }
  }
}
```

#### Opt-in Response

The response for Opt-in is the registered request. The result is returned through the webhook with OPT_IN trigger, see [**Opt-In Notification**](doc:conversation-optin#opt-in-notification).

#### Opt-in Notification

The result of registering an Opt-in is posted to the webhook which has OPT_IN trigger set.

Below you can find an example for Opt-in Notification.

```json
{
  "contact_id": "{{CONTACT_ID}}",
  "channel": "WHATSAPP",
  "identity": "46712312312",
  "status": "OPT_IN_SUCCEEDED"
}
```

The Opt-in Notification can have the following fields:

| Field           | Description                                                                      |
| --------------- | -------------------------------------------------------------------------------- |
| `contact_id`    | The id of the contact that the Opt-in was executed for.                          |
| `identity`      | The channel recipient identity of the contact.                                   |
| `channel`       | The channel that the Opt-in was registered with.                                 |
| `status`        | The status response for the Opt-in request. See possible values below the table. |
| `error_details` | If the Opt-in failed the reason is present in this field.                        |

Possible values for `status` are:

- `OPT_IN_STATUS_UNSPECIFIED` : the underlying channel doesn't support Opt-in.
- `OPT_IN_SUCCEEDED` : the Opt-in registration succeeded.
- `OPT_IN_FAILED` : the Opt-in registration failed, see reason in `error_details` field.

### Opt-out

#### Opt-out Request

Below you can find an example to register an Opt-out event.

Conversation API POST `optouts:register`

```json
{
  "app_id": "{{APP_ID}}",
  "channels": ["WHATSAPP"],
  "recipient": {
    "contact_id": "{{CONTACT_ID}}"
  }
}
```

Alternatively the contact's channel recipient identities can be used:

```json
{
  "app_id": "{{APP_ID}}",
  "channels": ["WHATSAPP"],
  "recipient": {
    "identified_by": {
      "channel_identities": [
        {
          "channel": "WHATSAPP",
          "identity": "14707864783"
        }
      ]
    }
  }
}
```

#### Opt-out Response

The response for Opt-out is the registered request. The result is returned through the webhook which has OPT_OUT trigger, see [**Opt-Out Notification**](doc:conversation-optin#opt-out-notification).

#### Opt-out Notification

The result of registering an Opt-out is posted to the webhook which has OPT_OUT trigger set.

Below you can find an example for Opt-out Notification.

```json
{
  "contact_id": "{{CONTACT_ID}}",
  "channel": "WHATSAPP",
  "identity": "46712312312",
  "status": "OPT_OUT_SUCCEEDED"
}
```

The Opt-out notification can have the following fields:

| Field           | Description                                                                |
| --------------- | -------------------------------------------------------------------------- |
| `contact_id`    | The id of the contact that the Opt-out was executed for.                   |
| `identity`      | The channel recipient identity of the contact.                             |
| `channel`       | The channel that the Opt-out was registered with.                          |
| `status`        | The status response for the Opt-out request. see below for possible values |
| `error_details` | If the Opt-out failed the reason is present in this field.                 |

Possible values for `status` are:

- `OPT_OUT_STATUS_UNSPECIFIED` : the underlying channel doesn't support Opt-out.
- `OPT_OUT_SUCCEEDED` : the Opt-out registration succeeded.
- `OPT_OUT_FAILED` : the Opt-out registration failed, see reason in `error_details`
