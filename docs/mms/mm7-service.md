---
title: MM7 Service
excerpt: ''
---
MMS APIs are currently offered via an implementation of the MM7 protocol. MM7 is the standard protocol used by the carriers to send and receive MMS messages. It is a SOAP based protocol sent over HTTP. MM7 supports the following API actions:

|   **Action**         |       **Functionality**                                              |
| -------------------- | -------------------------------------------------------------------- |
| `mm7_submit`         | Send an MT Message to a device.                                      |
| `mm7_deliver`        | Receive an MO message from the device.                               |
| `mm7_deliveryreport` | Receive a Delivery report for a previously submitted MT MMS Message. |

The use of our MM7 API is only available for accounts with a paid plan. We support submitting MMS messages with MM7 version 5.3.0 to 6.8.0. Your VASPID will be your API Key. We will also issue you a VASID to submit with your message. The VASID will be unique on each short code. All traffic is encrypted in transit via SSL/TLS.

