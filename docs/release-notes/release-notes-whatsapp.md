---
title: Sinch WhatsApp API
excerpt: >-
  See how the Sinch WhatsApp API is evolving and find out about new features and
  bug fixes.
---

## 2020-05-26

  - Video files are now supported in media template
  - A profile image can now be included in a contact message sent to the customer's callback URL
  - Improvements for inbound sticker messages - auto cleanup for old stickers file enabled
  - Improvements for Customer Care Session management - DELETED and SYSTEM statuses do not start a CC Session anymore

## 2020-05-12

  - New interactive message templates. See [Sending template message](doc:whatsapp-message#template-message).
  - Fix issue with missing DELETED status for MO messages.
  - Improve error handling for sending messages.
  - Upgrade google libphonenumber library.

## 2020-04-28

  - Add POST method to the capability endpoint
  - Add Swagger UI to the API. See [introduction](doc:whatsapp-introduction#swagger).
  - Fix bug with delayed FAILED status when sent media file is not accessible because of SSL certificate error.
  - Improve error handling for video files with incorrect codecs.
  - Fix response code when calling the capability endpoint with empty body.

## 2020-03-31

  - Improvements for processing of status update event timestamps returned in callbacks (sometimes later events had earlier timestamps e.g. SENT before DISPATCHED).
  - Improvements in error handling when adding admin to chat group.
  - Improvements in recipient phone number validation.

## 2020-03-10

  - New Sticker pack management API - business can now create, update or delete stickers pack group and use it in Customer Care messages.
  - Support for stickers in incoming messages from end user - Sticker messages are now sent back to business’s callback as other media Customer Care messages.
  - Add limitations and validation for media provider name. When business creates new media provider, name for that provider can be 200 length max and contain only alphanumeric characters plus “-” and/or “_”.
  - MSISDN validation improvement with new version of google’s library libphonenumber.

## 2020-02-25

  - WhatsApp profile of message sender (MO messages) are now present in callback body sent to business endpoint (available only for text, location and contact message types).
  - New Customer Care message type sticker added. Supported media type is “image/webp”.

## 2020-02-11

  - Fix issue with missing customer callbacks for messages delivered over 14 days after sent.
  - Upgrade WhatsApp Business API client (dockers) to latest v2.27.8.
  - Upgrade google libphonenumber library.

## 2020-01-31

  - Fix bug with dot ('.') prefix in cookies domain.
  - Add fail message when trying to add a number as admin if that number is not part of that group. 
  - Add support for filename parameter for document template messages.
  - New API endpoint for getting the content of the blacklist.
  - Change validation of numbers when adding to and removing from blacklist.
  - Fix issue with missing customer callbacks for messages from recently created phone numbers.
  - Add additional MIME type support for media files.