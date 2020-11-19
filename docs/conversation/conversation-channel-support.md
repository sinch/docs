---
title: Channel Support
excerpt: >-
  Sinch Conversation API channel specific configurations and message transcoding.
hidden: false
---

## Conversation API Channel Support <span class="betabadge">Beta</span>

Conversation API supports multiple **channels** that you can use to send different **message types**. 

Our current message types are:

* `Text Message` - A message containing only text
* `Media Message` - A message containing media such as images, GIFs, document and video.
* `Choice Message` - A message containing "choices"/"actions" and description.
* `Card Message` - A rich message which consists of text and description with image or video. It can also contain a set of "choices" ("actions").
* `Carousel Message` - A list of cards rendered horizontally on supported channels (Messenger, Viber Bot and RCS) and as a numbered list on SMS, Viber Business Messages and WhatsApp.
* `Location Message` - A message defining a physical location on a map.
* `Template message`- A message with predefined template. Requires an existing template.

Currently, the following channels are integrated into Conversation API. Each of the following documents will give you example requests for the message types, and rendered messages on handsets.
Please follow the links to the subpages to learn more:

* [**SMS**](doc:conversation-channel-support-sms) 

* [**WhatsApp**](doc:conversation-channel-support-whatsapp) 

* [**RCS**](doc:conversation-channel-support-rcs)

* [**Facebook Messenger**](doc:conversation-channel-support-messenger)

* [**Viber Business Messages**](doc:conversation-channel-support-viberbm)

* [**Viber Bot**](doc:conversation-channel-support-viber)

There are some channel specific features offered by Conversation API that you can use in your requests, read more at:
[**Channel Specific Properties**](doc:conversation-channel-support-channel-properties)