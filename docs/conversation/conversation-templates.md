---
title: Working with templates
excerpt: >-
  Managing and send templates messages with the Sinch Conversation API.
hidden: false
---

## Introduction

Conversation API supports sending template messages where all or parts of the message content is
pre-defined. The message template can also contain dynamic content which is populated when sending the message using template parameters.

There are two types of templates which can be used when sending a template message:
 - Omni-channel
 - Channel-specific 

The format of the omni-channel templates is exactly the same as the format of the `message` field of the `messages:send` request body. The omni-channel templates are stored and managed by [Template Management API](https://developers.sinch.com/reference#templates-1).

The channel-specific templates are templates which are stored and managed by the channels and are only referenced when sending through Conversation API. Currently WhatsApp templates are the only supported channel-specific templates.

WhatsApp templates are stored by Facebook WhatsApp Business API.

### Conversation API Omni-channel Templates

The omni-channel templates use Conversation API generic message format and therefore, can define a generic rich message or a concrete channel message by using the channel override feature of Conversation API. 

It is easy to see how an omni-channel template will render on a specific channel by using the transcoding (`messages:transcode`) endpoint of the Conversation API.

The omni-channel templates are versioned and support storing message content for different languages.

#### Managing Omni-channel Templates

Creating, updating, listing and deleting omni-channel templates is done through the
[Template Management API](https://developers.sinch.com/reference#templates-1) which is currently available in the following regions:

| Region | Host                              |
| ------ | --------------------------------- |
| US     | https://us.template.api.sinch.com |
| EU     | https://eu.template.api.sinch.com |

---

Templates are only stored in the region they are created and can only be used by Conversation API apps created in the same region.

Accessing the Template Management API requires a valid access token for your Sinch project ID.

To get an access token, you first need to create an Access Key in [Sinch Portal](https://dashboard.sinch.com/settings/access-keys) and store the Key Secret in a secure location.

Once you have a Key ID and Key Secret, obtaining a valid OAuth2 Access Token for the Template Management API is done as follows:

```curl
curl https://auth.sinch.com/oauth2/token -d grant_type=client_credentials --user <key_id>:<key_secret>
```

With the obtained Access Token you can, for example, store a text message template with a single English translation for a project with ID `{{PROJECT_ID}}`:

```curl
curl -X POST \
'https://us.template.api.sinch.com/v1/projects/{{PROJECT_ID}}/templates' \
-H 'Content-Type: application/json' --header 'Authorization: Bearer {{ACCESS_TOKEN}}' \
-d '{
  "description": "English text template with one parameter using Conversation API generic format.",
  "default_translation": "en",
  "channel": "CONVERSATION",
  "translations": [{
    "language_code": "en",
    "version": "20201130",
    "variables": [{
      "key": "name",
      "preview_value": "Mr Jones"
    }],
    "content": "{ \"text_message\": { \"text\": \"Hi ${name}.\" }}"
  }]
}'
```

The templates stored in the Template Management API have the following properties:

| Field               | Type             | Description                                                                                                                    |
| ------------------- | ---------------- | ------------------------------------------------------------------------------------------------------------------------------ |
| description         | string           | Template description.                                                                                                          |
| default_translation | string           | A translation to use if none given when sending a template message. Must be a valid BCP-47 language code.                      |
| channel             | string           | The Template Management API can store messages in different formats. The omni-channel templates use `CONVERSATION` as channel. |
| translations        | array of objects | List of translations for the template.                                                                                         |

Each translation object has the following properties:

| Field         | Type             | Description                                                                                                                 |
| ------------- | ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| language_code | string           | The BCP-47 language code, such as "en-US" or "sr-Latn".                                                                     |
| version       | string           | The version of this template translation.                                                                                   |
| variables     | array of objects | List of expected variables in the template translation. It is used to validate send template requests.                      |
| content       | string           | The template definition. Omni-channel templates are escaped JSON representation of Conversation API generic message format. |

Each variable object in the variables array has the following properties:

| Field         | Type             | Description                                                                                        |
| ------------- | ---------------- | -------------------------------------------------------------------------------------------------- |
| key           | string           | The key with which the variable is referred to in the template content placeholders e.g., `${key}` |
| preview_value | string           | A string representation to be used in the template previewers.                                     |

The Template Management API can be used to store both omni-channel templates potentially containing channel overrides or pure channel templates. For using the template when sending a template message with Conversation API, you need to use omni-channel templates i.e., set the `channel` to `CONVERSATION`
and use escaped JSON representation of Conversation API generic message format as `content`.

### Channel-specific Templates

Conversation API allows referring to channel-defined templates when sending a template message.

Currently, WhatsApp is the only channel that has channel-specific templates. They are uploaded to and approved by Facebook, and are required to send messages outside of the customer care session.

Once you have created a WhatsApp template in Facebook WhatsApp Business API and it has been approved,
you can start using it with Conversation API to send template messages to the WhatsApp channel as
in the example below:

```curl
curl -X POST \
'https://us.conversation.api.sinch.com/v1/projects/{{PROJECT_ID}}/messages:send' \
-H 'Content-Type: application/json' --header 'Authorization: Bearer {{ACCESS_TOKEN}}' \
-d '{
    "app_id": "{{APP_ID}}",
    "recipient": {
      "contact_id": "{{CONTACT_ID}}"
    },
    "message": {
        "template_message": {
            "channel_template": {
                "WHATSAPP": {
                    "template_id": "{{WHATSAPP_TEMPLATE_ID}}",
                    "language_code": "en",
                    "parameters": {
                        "header[1]image.link": "https://example.com",
                        "body[1]text": "Value of first parameter"
                    }
                }
            }
        }
    },
    "channel_priority_order": [
        "WHATSAPP"
    ]
}'
```

The snip above sends a template message to a recipient with `{{CONTACT_ID}}` and tries only
the `WHATSAPP` channel. The message to send is a template message referencing a
WhatsApp channel-defined template with ID `{{WHATSAPP_TEMPLATE_ID}}` and language code `en`.
It also specifies the parameters defined in the template as part of `parameters` field.
You can read more about sending WhatsApp channel-specific templates in [WhatsApp Channel Support](doc:conversation-whatsapp) page.

Omni- and channel-specific templates can both be used when sending a message. This is useful for example, to enable channel fallback in case the recipient has no capability on WhatsApp channel
as shown in the example below:

```curl
curl -X POST \
'https://us.conversation.api.sinch.com/v1/projects/{{PROJECT_ID}}/messages:send' \
-H 'Content-Type: application/json' --header 'Authorization: Bearer {{ACCESS_TOKEN}}' \
-d '{
    "app_id": "{{APP_ID}}",
    "recipient": {
      "contact_id": "{{CONTACT_ID}}"
    },
    "message": {
        "template_message": {
            "channel_template": {
                "WHATSAPP": {
                    "template_id": "{{WHATSAPP_TEMPLATE_ID}}",
                    "language_code": "en",
                    "parameters": {
                        "header[1]image.link": "https://example.com",
                        "body[1]text": "Value of first parameter"
                    }
                }
            },
            "omni_template": {
                "template_id": "{{OMNI_TEMPLATE_ID}}",
                "version": "{{OMNI_VERSION_ID}}",
                "language_code": "en",
                "parameters": {
                    "name": "Ms Smith"
                }
            }
        }
    },
    "channel_priority_order": [
        "WHATSAPP", "SMS"
    ]
}'
```

The Conversation API will use the channel-specific template if such exists for the channel and will fallback to the omni template otherwise.
