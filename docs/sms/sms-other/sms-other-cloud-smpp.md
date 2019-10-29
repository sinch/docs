---
title: Cloud SMPP
excerpt: >-
  Our Cloud SMPP service is available to you immediately after creating an
  account on our [web site](https://dashboard.sinch.com/#/signup). It uses the
  same core platform and backend connections as our Enterprise SMPP service.
---
## Connection Configuration


|||
|------------|----------|
|SMPP Version|3.3 or 3.4|
|Bind Type   |Transmitter, Receiver or Transceiver|
|Service Type, auth TON and auth NPI|All values are ignored and can be blank|
|Asynchronous outstanding operations window|10        |
|Maximum allowed sessions|3         

## Hosts

You can connect to any of the following hosts with your given username and password.

| Host                              | Location           |
| --------------------------------- | ------------------ |
| sms-cloud-1.clxcommunications.com | London, UK         |
| sms-cloud-2.clxcommunications.com | Virginia, US       |
| sms-cloud-3.clxcommunications.com | Dallas, US         |
| sms-cloud-5.clxcommunications.com | Frankfurt, Germany |

## Ports

Please ensure that you choose the correct port for the message encoding you are submitting.

| Encoding              | Port |
| --------------------- | ---- |
| GSM, Unicode & Binary | 8000 |
| Latin 1 (ISO-8859-1)  | 9000 |

Optionally, we support the SSL/TLS connection

| Encoding              | Port |
| --------------------- | ---- |
| GSM, Unicode & Binary | 8443 |
| Latin 1 (ISO-8859-1)  | 9443 |

## Bind Operations

There are three ways to open a connection using SMPP. You can connect as:

- **Transmitter** - send short messages to SMSC and receive responses from SMSC.
- **Receiver** - receive delivery receipts from the SMSC and return the corresponding responses.
- **Transceiver** - send and receive messages to and from the SMSC over a single SMPP session.

## Session States

Your connection to our server across an SMPP link can be in one of five states:

  - **OPEN** - connected and bind pending
  - **BOUND\_TX** - onnected and requested to bind as a Transmitter
  - **BOUND\_RX** - connected and requested to bind as a Receiver
  - **BOUND\_TRX** - connected and requested to bind as a Transceiver
  - **CLOSED** - unbound and disconnected

## Bind Parameters

The syntax for initiating a `bind_transmitter`, `bind_receiver` or `bind_transceiver` instance uses the following parameters:

  - **system\_id** - identifies the user requesting to bind (username)
  - **password** - password to allow access
  - **system\_type** - identifies the system type (ignored, set to blank)
  - **interface\_version** - indicates SMPP version supported by user
  - **addr\_ton** - identifies user type of number (ignored, set to blank)
  - **addr\_npi** - numbering plan indicator for user (ignored, set to blank)
  - **address\_range** - The user address

## Submit\_sm Parameters

The parameters required for the SUBMIT\_SM request (used to send an SMS) are:

  - **service\_type** - indicates SMS application service
  - **source\_addr\_ton** - type of number for source address
  - **source\_addr\_npi** - numbering plan indicator for source address
  - **source\_addr** - source address
  - **dest\_addr\_ton** - type of number for destination
  - **dest\_addr\_npi** - numbering plan indicator for destination
  - **destination\_addr** - destination address of the short message
  - **esm\_class** - message mode and type
  - **protocol\_id** - protocol identifier (network specific)
  - **priority\_flag** - sets the priority of the message (this is ignored)
  - **schedule\_delivery\_time** - set to NULL for immediate delivery (this is ignored)
  - **validity\_period** - validity period of message
  - **registered\_delivery** - indicator to signify if an SMSC delivery receipt or acknowledgment is required
  - **replace\_if\_present\_flag** - flag indicating if submitted message should replace an existing message (this is ignored)
  - **data\_coding** - defines the encoding scheme of the SMS message
  - **sm\_default\_msg\_id** - indicates short message to send from a predefined list of messages stored on SMSC (this is ignored)
  - **sm\_length** - length in octets of the short\_message user data
  - **short\_message** - up to 254 octets of short message user data.
  - **user\_message\_reference** - user assigned reference number

## Deliver\_sm Parameters

deliver\_sm has the same parameter list as the submit\_sm request:

  - **service\_type** - indicates SMS application service
  - **source\_addr\_ton** - type of number for source address
  - **source\_addr\_npi** - numbering plan indicator for source address
  - **source\_addr** - source address
  - **dest\_addr\_ton** - type of number for destination
  - **dest\_addr\_npi** - numbering plan indicator for destination
  - **destination\_addr** - destination address of the short message
  - **esm\_class** - message mode and type
  - **protocol\_id** - protocol identifier (network specific)
  - **priority\_flag** - sets the priority of the message (this is ignored)
  - **schedule\_delivery\_time** - set to NULL for immediate delivery (this is ignored)
  - **validity\_period** - validity period of message
  - **registered\_delivery** - indicator to signify if an SMSC delivery receipt or acknowledgment is required
  - **replace\_if\_present\_flag** - flag indicating if submitted message should replace an existing message (this is ignored)
  - **data\_coding** - defines the encoding scheme of the SMS message
  - **sm\_default\_msg\_id** - indicates short message to send from a predefined list of messages stored on SMSC (this is ignored)
  - **sm\_length** - length in octets of the short\_message user data
  - **short\_message** - up to 254 octets of short message user data.
  - **user\_message\_reference** - user assigned reference number

The SMSC delivery receipt is carried as the user data payload in the SMPP *deliver\_sm* operation.

*deliver\_sm\_resp* requests require only a *message\_id* parameter. Delivery receipts are addressed to the originator of the message.

## Transactional Error Codes

To help you identify what might be causing a problem with your SMPP transaction, here is a list of error codes with a small description:

| Code | Description                           |
| ---- | ------------------------------------- |
| 0    | No error                              |
| 3    | Invalid command ID                    |
| 4    | Invalid bind status for given command |
| 5    | ESME already in bound state           |
| 10   | Invalid source address                |
| 12   | Message ID is invalid                 |
| 13   | Bind failed                           |
| 14   | Invalid password                      |
| 15   | Invalid system ID                     |
| 20   | Message queue full                    |
| 21   | Invalid system type                   |
| 97   | Invalid scheduled delivery time       |
| 98   | Invalid message delivery period       |

## SMPP Commands

The SMS Gateway supports the following SMPP commands:

| Command                 | Description                        | HEX Code    |
| ----------------------- | ---------------------------------- | ----------- |
| generic\_nack           | Generic ‘Not Acknowledged’ status  | 0x80000000  |
| bind\_receiver          | Binds as ‘Receiver’                | 0x00000001  |
| bind\_receiver\_resp    | Response to bind\_receiver         | 0x80000001  |
| bind\_transmitter       | Binds as ‘Transmitter’             | 0x00000002  |
| bind\_transmitter\_resp | Response to bind\_transmitter      | 0x80000002  |
| submit\_sm              | Submit an SMS message              | 0x00000004  |
| submit\_sm\_resp        | Response to submit\_sm\_resp       | 0x80000004  |
| deliver\_sm\            | Receive an SMS or delivery receipt | 0x00000005  |
| deliver\_sm\_resp       | Response to deliver\_sm\_resp      | 0x80000005  |
| unbind                  | Close bind response                | 0x00000006  |
| unbind\_resp            | Response to unbind                 | 0x80000006  |
| bind\_transceiver       | Bind as ‘Transceiver’              | 0x00000009  |
| bind\_transceiver\_resp | Response to bind\_transceiver      | 0x80000009  |
| enquire\_link           | Check link status                  | 0x00000015  |
| enquire\_link\_resp     | Response to enquire\_link          | 0x80000015  |

Please note any command in the SMPP specification which is not listed above is not currently supported.

## Command States

SMPP supports the following commands through the following SMPP session states:

| Command                 | Required state                   |
| ----------------------- | -------------------------------- |
| bind\_transmitter       | OPEN                             |
| bind\_transmitter\_resp | OPEN                             |
| bind\_receiver          | OPEN                             |
| bind\_receiver\_resp    | OPEN                             |
| bind\_transceiver       | OPEN                             |
| bind\_transceiver\_resp | OPEN                             |
| unbind                  | BOUND\_TX, BOUND\_RX, BOUND\_TRX |
| unbind\_resp            | BOUND\_TX, BOUND\_RX, BOUND\_TRX |
| submit\_sm              | BOUND\_TX, BOUND\_TRX            |
| submit\_sm\_resp        | BOUND\_TX, BOUND\_TRX            |
| deliver\_sm             | BOUND\_RX, BOUND\_TRX            |
| deliver\_sm\_resp       | BOUND\_RX, BOUND\_TRX            |
| enquire\_link           | BOUND\_TX, BOUND\_RX, BOUND\_TRX |
| enquire\_link\_resp     | BOUND\_TX, BOUND\_RX, BOUND\_TRX |
| generic\_nack           | BOUND\_TX, BOUND\_RX, BOUND\_TRX |

NB: `data_sm`, `query_sm`, `cancel_sm`, `replace_sm` and `submit_sm_multi` are not supported.

`submit_sm` and `submit_sm_resp` transactions will include a message identifier and a status which identifies whether the message is valid or invalid. If invalid, an error status will be returned. Please note that the message identifier will be HEX encoded on SMPP 3.3 connections but will be a standard ASCII encoded integer on SMPP 3.4 connections.

## Delivery Receipts

SMPP delivery receipts take the following format:

**Format**
```text
id:IIIIIIIIII sub:SSS dlvrd:DDD submit date:YYMMDDhhmm done date:YYMMDDhhmm stat:DDDDDDD err:E Text .........
```


Where:

>   - **id** - the message ID allocated to the message by the server
>   - **sub** - the number of short messages originally submitted
>   - **dlvrd** - the number of short messages delivered
>   - **submit date** - the date and time at which the short message was submitted
>   - **done date** - the date and time at which the short message reached its final state
>   - **stat** - the final status of the message. Please see section 7.0 Message Status for more information.
>   - **err** - where appropriate this may hold a network specific error code or an SMSC error code
>   - **text** - the first 20 characters of the short message

Please note SMPP v3.3 and v3.4 differ, such that message IDs returned from an SMPP 3.3 connection are encoded as hex whereas 3.4 SMPP connections return message IDs as ASCII encoded integers.

## Message Status

The delivery report status indicates whether the SMS message was delivered successfully by the SMSC. If the SMS was not successfully delivered then the delivery report will give a reason in the form of an \[error code\].

SMPP message states and their meanings are listed here for your convenience:

| Code    | Description                         |
| ------- | ----------------------------------- |
| DELIVRD | Message delivered to destination    |
| ACCEPTD | Message is in accepted state        |
| EXPIRED | Message validity period has expired |
| DELETED | Message has been deleted            |
| UNDELIV | Message is undelivered              |
| UNKNOWN | Message is in unknown state         |
| REJECTD | Message is in rejected state        |

Please note some SMSCs will still return a delivery receipt when a message has been accepted or if the message is buffered in the SMSC, for example if the handset is switched off. This will use the UNKNOWN state and sets the buffered special parameter in the deliver\_sm under SMPP 3.4

## Error Codes

Sinch has a record of providing high quality and reliable reporting. Should your message not be delivered, an error code will be returned in the deliver\_sm with a reason why.

These are defined here:

| Hex | Decimal | Error Name                        | Description                                                                                                                                                | Duration  | Error From | Relating To                                |
| --- | ------- | --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- | --------- | ---------- | ------------------------------------------ |
| 0   | 0       | No Error                          | The message delivered successfully                                                                                                                         | Permanent | MSC        | Mobile Handset                             |
| 1   | 1       | Unknown Subscriber                | The MSISDN is inactive or no longer active.                                                                                                                | Permanent | HLR        | Destination Network                        |
| 2   | 2       | Unknown Subscriber -npdbMismatch  | Fault in Number Portability Database or HLR of MSISDN range holder. Occurs more frequently if number ported more than once in certain countries.           | Permanent | MSC        | Destination Network                        |
| 5   | 5       | Unidentified Subscriber           | Occurs when the MSC that a message has been sent to is not aware of the subscriber IMSI. Suggests HLR has not been updated or MSC malfunction.             | Temporary | MSC        | Destination Network                        |
| 6   | 6       | Unknown                           | It cannot be determined whether this message has been delivered or has failed due to lack of final delivery state information from the carrier.            | Permanent | MSC        | Destination Network                        |
| 9   | 9       | Illegal Subscriber                | Rejection due to failed authentication or filtering.                                                                                                       | Temporary | MSC        | Destination Network                        |
| A   | 10      | No Translation for Address        | Destination number is not a valid mobile number or its routing cannot be determined.                                                                       | Permanent | TRANSPORT  | Signalling                                 |
| B   | 11      | Teleservice Not Provisioned       | Rejection due to subscription not supporting SMS.                                                                                                          | Permanent | HLR        | Mobile Handset/Destination Network         |
| C   | 12      | Illegal Equiptment                | Rejection due to subscription, handset or network not supporting SMS.                                                                                      | Temporary | MSC        | Mobile Handset/Destination Network         |
| D   | 13      | Call Barred                       | Rejection due to subscription or network not allowing SMS.                                                                                                 | Temporary | HLR        | Mobile Handset/Destination Network         |
| 15  | 21      | Facility Not Supported            | Rejection due to subscription not supporting SMS.                                                                                                          | Temporary | MSC        | Destination Network                        |
| 1B  | 27      | Absent Subscriber                 | Subscriber handset is not logged onto the network due to it being turned off or out of coverage. Likely to have been unavailable for 12 hours or more.     | Temporary | HLR        | Mobile Handset                             |
| 1C  | 28      | Absent subscriber no-pageresponse | Subscriber handset is not reachable on the network due to it being turned off or out of coverage. Likely to have very recently become unavailable.         | Temporary | MSC        | Mobile Handset                             |
| 1D  | 29      | Absent subscriber IMSI-detached   | Subscriber handset is not reachable on the network due to it being turned off or out of coverage. Likely to have been unavailable for several hours.       | Temporary | MSC        | Mobile Handset                             |
| 1E  | 30      | Controlling MSC Failure           | The MSC that the subscriber is currently registered to is experiencing a fault.                                                                            | Temporary | MSC        | Destination Network                        |
| 1F  | 31      | Subscriber Busy For MT-SM         | MSC is busy handling an exisiting transaction with the handset. The subscriber could be currently receiving an SMS at exactly the same time.               | Temporary | MSC        | Mobile Handset                             |
| 20  | 32      | Equipment notSMEquipped           | Recieving handset or equipment does not support SMS or an SMS feature. This is temporary because the subscriber could switch to a different device.        | Temporary | MSC        | Mobile Handset                             |
| 21  | 33      | Memory Capacity Exceeded          | Rejection due to subscriber handset not having the memory capacity to recieve the message. Likely to have been in state for 12 hours or more.              | Temporary | HLR        | Destination Network                        |
| 22  | 34      | System Failure                    | Rejection due to SS7 protocol or network failure.                                                                                                          | Temporary | MSC        | Destination Network                        |
| 23  | 35      | Data Missing                      | Rejection due to subscriber network decoding error or signalling fault.                                                                                    | Temporary | MSC        | Destination Network                        |
| 24  | 36      | Unexpected Data Value             | Rejection due to subscriber network decoding error or signalling fault.                                                                                    | Temporary | MSC        | Destination Network                        |
| 25  | 37      | System Failure                    | Rejection due to SS7 protocol or network failure.                                                                                                          | Temporary | HLR        | Destination Network                        |
| 26  | 38      | Data Missing                      | Rejection due to subscriber network decoding error or signaling fault.                                                                                     | Temporary | HLR        | Destination Network                        |
| 27  | 39      | Unexpected Data Value             | Rejection due to subscriber network decoding error or signaling fault.                                                                                     | Temporary | HLR        | Destination Network                        |
| 28  | 40      | Memory capacity Exceeded          | Rejection due to subscriber handset not having the memory capacity to receive the message. Likely to have run out of capacity recently.                    | Temporary | MSC        | Destination Network                        |
| 45  | 69      | Generic delivery failure          | Generic delivery failure                                                                                                                                   | Permanent | MSC        | Destination Network                        |
| 8C  | 140     | SS7 Communication Error           | Internal SMSC error due to invalid message syntax.                                                                                                         | Temporary | SMSC       | Message Construction                       |
| A0  | 160     | Absent subscriber IMSI-detached   | Internal SMSC error caused by SS7 link/dialogue fault.                                                                                                     | Temporary | SMSC       | Destination Network/Signalling             |
| C8  | 200     | Unable to decode response         | SMSC cannot decode the response received from destination network due to an encoding or protocol fault.                                                    | Temporary | SMSC       | Destination Network/Signalling             |
| C9  | 201     | Provider Abort                    | Subscriber network or signalling partner has terminated the signalling connection, preventing message transmission.                                        | Temporary | TRANSPORT  | Destination Network/Signalling             |
| CA  | 202     | User Abort                        | Subscriber network or signalling partner has rejected the signalling connection, preventing message transmission.                                          | Temporary | TRANSPORT  | Destination Network/Signalling             |
| CB  | 203     | Timeout                           | Subscriber network not recieving packets from SMSC, or not responding to them. Alternatively, a 3rd party signalling partner may not be routing correctly. | Temporary | TRANSPORT  | Destination Network/Signalling             |
| CD  | 205     | Timeout-PAB                       | Subscriber network or signalling partner has not responded to signalling connection setup or maintenance packets.                                          | Temporary | TRANSPORT  | Destination Network/Signalling             |
| CE  | 206     | Rejected                          | Subscriber network refuses signalling connection or message packet.                                                                                        | Permanent | TRANSPORT  | Destination Network                        |
| CF  | 207     | Local Cancel                      | Signalling with destination network has been prevented by SMSC partner or signalling partner.                                                              | Permanent | CARRIER    | SMSC Partner Routing/Signalling            |
| 12C | 300     | Screening or Blocking             | SMSC partner or 3rd party signalling partner has prevented messages being sent to this MSISDN or destination network.                                      | Permanent | CARRIER    | SMSC Partner Routing/Signalling            |
| 12D | 301     | Carrier Syntax Error              | SMSC partner has rejected the message due to an unacceptable message parameter.                                                                            | Permanent | CARRIER    | SMSC Partner Platform/Message Construction |
| 12E | 302     | Carrier Internal Error            | SMSC partner could not process this message due to a platform fault, but it will be retired.                                                               | Temporary | CARRIER    | SMSC Partner Platform                      |
| 12F | 303     | Carrier Internal Error            | SMSC partner could not process this message due to a platform fault and it will not be retried.                                                            | Permanent | CARRIER    | SMSC Partner Platform                      |
| 130 | 304     | Carrier Routing Error             | SMSC partner cannot route this message currently, but it will be retried.                                                                                  | Temporary | CARRIER    | SMSC Partner Platform/Signalling           |
| 131 | 305     | Carrier Routing Error             | SMSC partner cannot route this message and it will not be retried.                                                                                         | Permanent | CARRIER    | SMSC Partner Platform/Signalling           |
| 3E7 | 999     | Congestion                        | SS7 signalling link at destination network, SMSC, or 3rd party signalling partner is overloaded.                                                           | Temporary | TRANSPORT  | Destination Network/Signalling             |

## Terminology

  - MSISDN  
    Mobile Subscriber Integrated Services Digital Network: the mobile
    number in international format.

  - MSC  
    Mobile Switching Centre: the destination network equipment that
    receives an SMS (via forward-SM operation) in a destination mobile
    network.

  - HLR  
    Home Location Register: the destination network equipment that
    returns status and routing information about an MSISDN to the SMSC
    (via SRI-SM operation).

  - SMSC  
    Short Message Service Centre: the equipment belonging to Sinch or
    one of its carriers that transmits SMS to the destination network
    via SS7.

  - SS7  
    Signalling System 7: the transport protocol that interconnects
    global GSM networks.

  - MNP  
    Mobile Number Portability: the process of a subscriber moving from
    one mobile network to another, but retaining the same MSISDN.

  - IMSI  
    International Mobile Subscriber Identity: a unique identification
    number which identifies the destination country, actual network, and
    network subscriber ID.

  - Subscriber  
    The mobile user who has a SIM card.

