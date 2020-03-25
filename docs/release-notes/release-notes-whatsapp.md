---
title: Sinch WhatsApp API
excerpt: >-
  See how the Sinch WhatsApp API is evolving and find out about new features
  and bug fixes.
---
<h3>2020-03-31</h3>
  - Improvements for processing of status update event timestamps returned in callbacks (sometimes later events had earlier timestamps e.g. SENT before DISPATCHED).
  - Improvements in error handling when adding admin to chat group.
  - Improvements in recipient phone number validation.

<h3>2020-03-10</h3>
  - New Sticker pack management API - business can now create, update or delete stickers pack group and use it in Customer Care messages.
  - Support for stickers in incoming messages from end user - Sticker messages are now sent back to business’s callback as other media Customer Care messages.
  - Add limitations and validation for media provider name. When business creates new media provider, name for that provider can be 200 length max and contain only alphanumeric characters plus “-” and/or “_”.
  - MSISDN validation improvement with new version of google’s library libphonenumber.

<h3>2020-02-25</h3>
  - WhatsApp profile of message sender (MO messages) are now present in callback body sent to business endpoint (available only for text, location and contact message types).
  - New Customer Care message type sticker added. Supported media type is “image/webp”.

<h3>2020-02-11</h3>
  - Fix issue with missing customer callbacks for messages delivered over 14 days after sent.
  - Upgrade WhatsApp Business API client (dockers) to latest v2.27.8.
  - Upgrade google libphonenumber library.

<h3>2020-01-31</h3>
  - Fix bug with dot ('.') prefix in cookies domain.
  - Add fail message when trying to add a number as admin if that number is not part of that group. 
  - Add support for filename parameter for document template messages.
  - New API endpoint for getting the content of the blacklist.
  - Change validation of numbers when adding to and removing from blacklist.
  - Fix issue with missing customer callbacks for messages from recently created phone numbers.
  - Add additional MIME type support for media files.