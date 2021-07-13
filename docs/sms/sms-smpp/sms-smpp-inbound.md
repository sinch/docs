---
title: Inbound SMS SMPP
excerpt: ''
next:
  pages:
    - sms-smpp-outbound
    - sms-smpp-error-specification
    - sms-smpp-encoding
---
### How To Connect

Find the host and port number used to connect to our inbound service via SMPP in the table below. Username and password will be found in the CAD (Client Account Details) document provided to you by your account manager.

|           |                                     |
| --------- | ----------------------------------- |
| Host      | \<host\>.smpp.api.sinch.com         |
| Port      | 3600 (use 3601 for SSL)             |
| System-ID | \<system-id\>                       |
| Password  | \<password\>                        |

### How To Receive Messages

#### Encoding

With SMPP, Data Coding Scheme must be specified. We recommend the "data_coding" parameter be set to 0 for basic GSM-7 encoding. For better delivery of characters outside of the GSM-7 character set, we recommend the "data_coding" parameter be set to 8 for UCS-2 encoding.

#### Parameters

Inbound messages will be delivered to your platform as a "DELIVER\_SM". The table below shows which parameters you will receive for each mobile originated SMS sent to your number.

| Parameter                  | Description                                                                                             |
| -------------------------- | ------------------------------------------------------------------------------------------------------- |
| command\_id                | Will always be DELIVER\_SM                                                                              |
| service\_type              | Will always be 0                                                                                        |
| source\_addr\_ton          | TON for the source address, will be INTERNATIONAL (1) for all mobile originated messages                |
| source\_addr\_npi          | NPI for the source address, will be MSISDN (1) for all mobile originated messages                       |
| source\_addr               | Source address - the MSISDN the SMS was sent from                                                       |
| dest\_addr\_ton            | TON for the destination address, will be INTERNATIONAL (1) Long numbers and NATIONAL (2) for Short code |
| dest\_addr\_npi            | NPI for the destination address, will always be MSISDN (1)                                              |
| destination\_addr          | Destination address - the MSISDN the SMS was sent to (your Long Number or Short Code)                   |
| esm\_class                 | Will always be 0                                                                                        |
| protocol\_id               | Will always be 0                                                                                        |
| priority\_flag             | Will always be 0                                                                                        |
| schedule\_delivery\_time   | Will always be 0                                                                                        |
| validity\_period           | Will always be 0                                                                                        |
| registered\_delivery       | Will always be 0                                                                                        |
| replace\_if\_present\_flag | Will always be 0                                                                                        |
| data\_coding               | Encoding used. Default is GSM-7 (0), UCS-2 for messages containing characters outside GSM-7 (8)         |
| sm\_default\_msg\_id       | Will always be 0                                                                                        |
| sm\_length                 | Short Message Length - number of characters in message content                                          |
| short\_message             | Message content                                                                                         |

### Examples

#### Inbound SMS sent to a Long Number

| Parameter                  | Example     |
| -------------------------- | ----------- |
| command\_id                | DELIVER\_SM |
| service\_type              | 0           |
| source\_addr\_ton          | 1           |
| source\_addr\_npi          | 1           |
| source\_addr               | 46706160585 |
| dest\_addr\_ton            | 1           |
| dest\_addr\_npi            | 1           |
| destination\_addr          | 46737494630 |
| esm\_class                 | 0           |
| protocol\_id               | 0           |
| priority\_flag             | 0           |
| schedule\_delivery\_time   | 0           |
| validity\_period           | 0           |
| registered\_delivery       | 0           |
| replace\_if\_present\_flag | 0           |
| data\_coding               | 0           |
| sm\_default\_msg\_id       | 0           |
| sm\_length                 | 4           |
| short\_message             | Test        |

#### Inbound SMS sent to a Short Code

| Parameter                  | Example     |
| -------------------------- | ----------- |
| command\_id                | DELIVER\_SM |
| service\_type              | 0           |
| source\_addr\_ton          | 1           |
| source\_addr\_npi          | 1           |
| source\_addr               | 46706160585 |
| dest\_addr\_ton            | 2           |
| dest\_addr\_npi            | 1           |
| destination\_addr          | 71630       |
| esm\_class                 | 0           |
| protocol\_id               | 0           |
| priority\_flag             | 0           |
| schedule\_delivery\_time   | 0           |
| validity\_period           | 0           |
| registered\_delivery       | 0           |
| replace\_if\_present\_flag | 0           |
| data\_coding               | 0           |
| sm\_default\_msg\_id       | 0           |
| sm\_length                 | 4           |
| short\_message             | Test        |

