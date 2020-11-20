---
title: Channel Support
excerpt: >-
  Sinch Conversation API channel specific configurations and message transcoding.
hidden: false
---

## Conversation API Channel Support <span class="betabadge">Beta</span>

Conversation API supports multiple **channels** that you can use to send different **message types**. 

---

### Message Types

Our current message types are:

* `Text Message` - A message containing only text

* `Media Message` - A message containing media such as images, GIFs, document and video.

* `Choice Message` - A message containing "choices"/"actions" and description.

* `Card Message` - A rich message which consists of text and description with image or video. It can also contain a set of "choices" ("actions").

* `Carousel Message` - A list of cards rendered horizontally on supported channels (Messenger, Viber Bot and RCS) and as a numbered list on SMS, Viber Business Messages and WhatsApp.

* `Location Message` - A message defining a physical location on a map.

* `Template message`- A message with predefined template. Requires an existing template.

---

### Supported Channels

Currently, the following channels are integrated into Conversation API. Each of the following documents will give you example requests for the message types, and rendered messages on handsets.
Please follow the links to the subpages to learn more:

 [<img src="https://files.readme.io/d0223ff-messages-chat-keynote-icon.svg" width="20" height="20" /> **SMS**](doc:conversation-sms)
 
 [<img src="https://files.readme.io/7474132-whatsapp.svg" width="20" height="20" /> **WhatsApp**](doc:conversation-whatsapp)
 
 [<img src="https://files.readme.io/d0223ff-messages-chat-keynote-icon.svg" width="20" height="20" /> **RCS**](doc:conversation-rcs)
 
 [<img src="https://files.readme.io/41a20d1-messenger.svg" width="20" height="20" /> **Facebook Messenger**](doc:conversation-facebook-messenger)
 
 [<img src="https://files.readme.io/8d98aa3-Viber-02.svg" width="20" height="20" /> **Viber Business Messages**](doc:conversation-viber-business)
 
 [<img src="https://files.readme.io/8d98aa3-Viber-02.svg" width="20" height="20" /> **Viber Bot**](doc:conversation-viber-bot)

---
There are some channel specific features offered by Conversation API that you can use in your requests, read more at: [**Channel Specific Properties**](doc:conversation-channel-properties).
