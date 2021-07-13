---
title: Sinch Conversation API
excerpt: >-
  See how the Sinch Conversation API is evolving and find out about new features and bug fixes.
---

## 2021-07-14
- Release of KakaoTalk channel support Beta
- Improvements in validating content for WhatsApp message requests
- Validation of phone number contact identities

## 2021-07-08
- Release of Telegram channel support Beta

## 2021-06-24
- Improvements in transcoding of CardMessage and CarouselMessage for Instagram channel

## 2021-06-23
- Improvements on MMS channel for handling choice responses and contact messages that contain media attachments
- Improvements in message request validation
- Enable setting contact metadata in message request for newly created contacts
- Handling of Suspended state for WhatsApp bots

## 2021-06-09
- Bugfixes in the CardMessage text formatting on WhatsApp and Viber Business channels
- Improvements for WhatsApp channel error response mapping and forwarding
- Support for vCard .vcf files on MMS channel in MediaMessage and CardMessage
- Support for MMS_STRICT_VALIDATION channel property to validate MMS media message contents against best practices
- Support for contact based retention policy
- New feature in Contact Management: enables fetching user profile from channels (this first release only supports Facebook Messenger)

## 2021-06-02
- Release of Instagram channel support Beta

## 2021-05-26
- Bugfix for supporting RCS CardMessage without media
- Improvements in retention policy execution

## 2021-05-12
- Improved request body validation
- Support for SMS_SENDER channel property to populate originator on SMS channel
- Added support for sending and receiving documents on Viber Business Messaging
- Improved error response mapping and forwarding on Viber Business and SMS channels
- Bugfix for keeping card order in RCS CarouselMessages

## 2021-04-28
- Improvements in Viber Business Messaging transcoding with text formatting
- Various bugfixes

## 2021-04-14
 - Add support for MMS
 - Add validation of URLs in MT messages
 - Add validation against duplicated callback triggers
 - Handle ChoiceResponseMessages on Viber Bot that have no match in the choice tracking data
 - Fix prematurely closed HTTP connection exceptions for WhatsApp
 
