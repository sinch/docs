---
title: Opt-In & Opt-Out
excerpt: >-
  Registering Opt-In & Opt-Out requests
hidden: false
---

## Introduction to Opt-In & Opt-Out <span class="betabadge">Beta</span>

By using the Opt-In & Opt-Out service it is possible to register an Opt-In or an Opt-Out event for a [**contact**](https://developers.sinch.com/docs/conversation#contact) with the underlying channel. In order to execute the request either the contactId of the contact or its channel recipient identities are required.

In Beta version of Conversation API the Opt-In & Opt-Out registration is only supported for WhatsApp channel. To read more about relevant policies go to [**WhatsApp Opt-In and Opt-Out**](https://developers.sinch.com/docs/whatsapp-opt-in-and-outs).

Requests are executed asynchronously, therefore the endpoint immediately returns the registered request. The result of the Opt-In or Opt-Out request is posted to the webhook which has OPT_IN or OPT_OUT trigger respectively. To read more about setting up webhooks go to [**Webhooks**](https://developers.sinch.com/docs/conversation#webhook).
 
### Opt-In

#### Opt-In Request

Below you can find an example to register an Opt-In event.

Conversation API POST `optins:register`

```json
{
  "app_id": "{{APP_ID}}",
  "channels": [ "WHATSAPP" ],
  "recipient": {
    "contact_id": "{{CONTACT_ID}}"
  }
}
```

Alternatively the contact's channel recipient identities can be used:

```json
{
  "app_id": "{{APP_ID}}",
  "channels": [ "WHATSAPP" ],
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

#### Opt-In Response

The response for Opt-In is the registered request. The result is returned through the webhook with OPT_IN trigger, see [**Opt-In Notification**](doc:conversation-optin#opt-in-notification).

#### Opt-In Notification 

The result of registering an Opt-In is posted to the webhook which has OPT_IN trigger set.

Below you can find an example for Opt-In Notification.

```json
{
  "contact_id": "{{CONTACT_ID}}",
  "channel": "WHATSAPP",
  "identity": "46712312312",
  "status": "OPT_IN_SUCCEEDED"
}
```

The Opt-In Notification can have the following fields:

| Field             | Description                                                                                          |
| ----------------- | ---------------------------------------------------------------------------------------------------- |
| `contact_id`      | The id of the contact that the Opt-In was executed for.                                              |
| `identity`        | The channel recipient identity of the contact.                                                       |
| `channel`         | The channel that the Opt-In was registered with.                                                     |
| `status`          | The status response for the Opt-In request. See possible values below the table.                     |
| `error_details`   | If the Opt-In failed the reason is present in this field.                                            |

Possible values for `status` are:
* `OPT_IN_STATUS_UNSPECIFIED` : the underlying channel doesn't support Opt-In.
* ``OPT_IN_SUCCEEDED`` : the Opt-In registration succeeded.
* ``OPT_IN_FAILED`` : the Opt-In registration failed, see reason in `error_details` field.

### Opt-Out

#### Opt-Out Request

Below you can find an example to register an Opt-Out event.

Conversation API POST `optouts:register`

```json
{
  "app_id": "{{APP_ID}}",
  "channels": [ "WHATSAPP" ],
  "recipient": {
    "contact_id": "{{CONTACT_ID}}"
  }
}
```

Alternatively the contact's channel recipient identities can be used:

```json
{
  "app_id": "{{APP_ID}}",
  "channels": [ "WHATSAPP" ],
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

#### Opt-Out Response

The response for Opt-Out is the registered request. The result is returned through the webhook which has OPT_OUT trigger, see [**Opt-Out Notification**](doc:conversation-optin#opt-out-notification).

#### Opt-Out Notification 

The result of registering an Opt-Out is posted to the webhook which has OPT_OUT trigger set.

Below you can find an example for Opt-Out Notification.

```json
{
  "contact_id": "{{CONTACT_ID}}",
  "channel": "WHATSAPP",
  "identity": "46712312312",
  "status": "OPT_OUT_SUCCEEDED"
}
```

The Opt-Out Notification can have the following fields:

| Field             | Description                                                                                          |
| ----------------- | ---------------------------------------------------------------------------------------------------- |
| `contact_id`      | The id of the contact that the Opt-Out was executed for.                                             |
| `identity`        | The channel recipient identity of the contact.                                                       |
| `channel`         | The channel that the Opt-Out was registered with.                                                    |
| `status`          | The status response for the Opt-Out request. see below for possible values               |
| `error_details`   | If the Opt-Out failed the reason is present in this field.                                           |

 Possible values for `status` are:

* `OPT_OUT_STATUS_UNSPECIFIED` : the underlying channel doesn't support Opt-Out. 
* `OPT_OUT_SUCCEEDED` : the Opt-Out registration succeeded. 
* `OPT_OUT_FAILED` : the Opt-Out registration failed, see reason in `error_details`



