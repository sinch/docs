---
title: Channel properties
excerpt: >-
  Sinch Conversation API channel specific properties.
hidden: false
---

### Conversation API Channel Specific Properties

Conversation API allows you to use some channel specific properties while sending messages on certain channels. These properties can be listed under "channel_properties" in the message request.

Example for adding the channel specific property "MESSENGER_NOTIFICATION_TYPE" on Facebook Messenger:

```json
{
  "app_id": "{{APP_ID}}",
  "recipient": {
    "contact_id": "{{CONTACT_ID}}"
  },
  "message": {
    "text_message": {
      "text": "Text message from Sinch Conversation API."
    }
  },
  "channel_priority_order": ["MESSENGER"],
  "channel_properties": {
    "MESSENGER_NOTIFICATION_TYPE": "SILENT_PUSH"
  }
}
```

Example for adding "VIBER_SENDER_NAME" and "VIBER_SENDER_AVATAR" on Viber Bot:

```json
{
  "app_id": "{{APP_ID}}",
  "recipient": {
    "contact_id": "{{CONTACT_ID}}"
  },
  "message": {
    "text_message": {
      "text": "Text message from Sinch Conversation API."
    }
  },
  "channel_priority_order": ["VIBER"],
  "channel_properties": {
    "VIBER_SENDER_NAME": "John Doe",
    "VIBER_SENDER_AVATAR": "https://1vxc0v12qhrm1e72gq1mmxkf-wpengine.netdna-ssl.com/wp-content/uploads/2018/12/favicon.png"
  }
}
```

Example for adding the channel specific property "SMS_SENDER" on SMS channel:

```json
{
  "app_id": "{{APP_ID}}",
  "recipient": {
    "contact_id": "{{CONTACT_ID}}"
  },
  "message": {
    "text_message": {
      "text": "Text message from Sinch Conversation API."
    }
  },
  "channel_priority_order": ["SMS"],
  "channel_properties": {
    "SMS_SENDER": "mysender"
  }
}
```

The names of available channel specific properties indicate which channel they can be used for.

Possible values for property names and values:

| Property name                 | Property value                                                                                                                                                                                                                                                                                                                                                                                                                                |
| ----------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `MESSENGER_MESSAGING_TYPE`    | Messenger messaging type. For more information visit: https://developers.facebook.com/docs/messenger-platform/send-messages/#messaging_types Defaults to MESSAGE_TAG if MESSENGER_MESSAGE_TAG is set.                                                                                                                                                                                                                                         |
| `MESSENGER_MESSAGE_TAG`       | Messenger message tag. Enables sending specific updates to users outside the standard messaging window. If you want to send messages after the 24h response window it is required to include the proper message tag. For more information including a list of possible values and applicable policies visit: https://developers.facebook.com/docs/messenger-platform/send-messages/message-tags There is no default value for this property.  |
| `MESSENGER_NOTIFICATION_TYPE` | Messenger push notification type. Possible values are REGULAR (sound/vibration), SILENT_PUSH (on-screen notification only) and NO_PUSH (no notification). The default is REGULAR.                                                                                                                                                                                                                                                             |
| `VIBER_SENDER_NAME`           | Viber Bot sender’s name to display. Max 28 characters.                                                                                                                                                                                                                                                                                                                                                                                        |
| `VIBER_SENDER_AVATAR`         | Viber Bot sender’s avatar URL. Avatar size should be no more than 100 kb. Recommended 720x720.                                                                                                                                                                                                                                                                                                                                                |
| `SMS_FLASH_MESSAGE`           | Whether this is flash SMS message. Flash SMS messages are shown on screen without user interaction while not saving the message to the inbox. Possible values are true and false. The default is false.                                                                                                                                                                                                                                       |
| `MMS_SENDER`                  | Required on MMS channel, specifies the shortcode or longnumber to use as the sender.                                                                                                                                                                                                                                                                                                                                                          |
| `SMS_SENDER`                  | Optional, the sender to use when sending a message on SMS channel. Can be a valid MSISDN, short code or alphanumeric sender. If the sender is rejected by the underlying SMS channel you get the error information in the delivery report                                                                                                                                                                                                     |
