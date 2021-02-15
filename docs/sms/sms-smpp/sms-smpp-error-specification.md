---
title: Error Specification
excerpt: >-
  This information covers error codes related to the SMPP communication
next:
  pages:
    - sms-smpp-encoding
---

### SMPP connection error codes

All errors on the SMPP connection received in SUBMIT\_SM\_RESP are described in the SMPP Protocol Specification v3.4 Issue 1.2 chapter 5.1.3 command\_status.

### Messages states

A message will always be in one of the following states:

| Message state | Value | Description                                                |
| ------------- | ----- | ---------------------------------------------------------- |
| ENROUTE       | 1     | Message is in the process of being sent to the destination |
| Delivered     | 2     | Message has been delivered to destination                  |
| EXPIRED       | 3     | Message validity period has expired                        |
| DELETED       | 4     | Message has been deleted                                   |
| UNDELIVERABLE | 5     | Message is undeliverable                                   |
| UNKNOWN       | 7     | Message is in invalid state                                |

The states are described in the SMPP Protocol Specification v3.4 Issue 1.2 chapter 5.2.28 message\_state. The message state value is also attached in a SMPP Optional Parameter “message\_state” value 0x0427 described in the SMPP Protocol Specification v3.4 Issue 1.2 chapter 5.3.2.35.

### Status reports error codes

The error codes are provided in status reports according to the SMPP Protocol Specification v3.4 Issue 1.2 Appendix B. The following are the error codes Sinch networks will be sending:

| Error | Error Class         | Description                                     | Comment                                                                            | Treated as |
| ----- | ------------------- | ----------------------------------------------- | ---------------------------------------------------------------------------------- | ---------- |
| 0     | NO ERROR            | No error / Unknown error                        |                                                                                    | Temporary  |
| 1     | ROUTING ERROR       | Internal routing error                          | Report to Sinch                                                                    | Permanent  |
| 2     | ROUTING ERROR       | Internal routing error                          | Report to Sinch                                                                    | Permanent  |
| 3     | ROUTING ERROR       | Internal routing error                          | Report to Sinch                                                                    | Permanent  |
| 4     | ROUTING ERROR       | Internal routing error                          | Report to Sinch                                                                    | Temporary  |
| 5     | ROUTING ERROR       | Internal routing error                          | Report to Sinch                                                                    | Permanent  |
| 6     | ROUTING ERROR       | Internal routing error                          | Report to Sinch                                                                    | Permanent  |
| 7     | ROUTING ERROR       | Internal routing error                          | Report to Sinch                                                                    | Permanent  |
| 8     | ROUTING ERROR       | Internal routing error                          | Report to Sinch                                                                    | Permanent  |
| 9     | ROUTING ERROR       | Unsupported number plan                         |                                                                                    | Permanent  |
| 10    | ROUTING ERROR       | Unsupported type of number                      |                                                                                    | Permanent  |
| 11    | ROUTING ERROR       | Message not delivered                           |                                                                                    | Permanent  |
| 12    | ROUTING ERROR       | Dialing zone not found                          |                                                                                    | Permanent  |
| 13    | ROUTING ERROR       | Not home zone and IMSI not allowed              |                                                                                    | Permanent  |
| 14    | ROUTING ERROR       | Not home zone and IMSI fetch failed             |                                                                                    | Temporary  |
| 15    | SCREENING ERROR     | Screening block                                 |                                                                                    | Permanent  |
| 16    | SCREENING ERROR     | Terminating IMSI blocked                        |                                                                                    | Permanent  |
| 17    | ROUTING ERROR       | Destination network type unknown                |                                                                                    | Permanent  |
| 18    | ESME ERROR          | ESME error                                      |                                                                                    | Temporary  |
| 19    | SCREENING ERROR     | Originating location mismatch                   | Permanent                                                                          | Permanent  |
| 40    | INTERNAL ERROR      | Internal error                                  | Report to Sinch                                                                    | Temporary  |
| 50    | INTERNAL ERROR      | Internal error                                  | Report to Sinch                                                                    | Temporary  |
| 51    | INTERNAL ERROR      | Internal error                                  | Report to Sinch                                                                    | Temporary  |
| 52    | INTERNAL ERROR      | Internal error                                  | Report to Sinch                                                                    | Temporary  |
| 53    | INTERNAL ERROR      | Internal error                                  | Report to Sinch                                                                    | Permanent  |
| 54    | INTERNAL ERROR      | Internal error                                  | Report to Sinch                                                                    | Permanent  |
| 55    | INTERNAL ERROR      | Internal error                                  | Report to Sinch                                                                    | Permanent  |
| 56    | INTERNAL ERROR      | Internal error                                  | Report to Sinch                                                                    | Temporary  |
| 60    | SCREENING ERROR     | Error, originator blocked                       |                                                                                    | Permanent  |
| 61    | SCREENING ERROR     | Error, destination blocked                      |                                                                                    | Permanent  |
| 62    | SCREENING ERROR     | Error, keyword blocked                          |                                                                                    | Permanent  |
| 63    | SCREENING ERROR     | Error, SC address blocked                       |                                                                                    | Permanent  |
| 64    | SCREENING ERROR     | Error, blocked due to exceeded quota            |                                                                                    | Permanent  |
| 65    | SCREENING ERROR     | Error, loop detected                            |                                                                                    | Permanent  |
| 66    | SCREENING ERROR     | Error, data coding scheme blocked               |                                                                                    | Permanent  |
| 67    | SCREENING ERROR     | Error, information element identifier blocked   |                                                                                    | Permanent  |
| 70    | ESME ERROR          | Internal error                                  | Report to Sinch                                                                    | Temporary  |
| 71    | ESME ERROR          | Internal error                                  | Report to Sinch                                                                    | Temporary  |
| 72    | ESME ERROR          | Internal error                                  | Report to Sinch                                                                    | Permanent  |
| 73    | ESME ERROR          | Internal error                                  | Report to Sinch                                                                    | Temporary  |
| 74    | ESME ERROR          | Internal error                                  | Report to Sinch                                                                    | Temporary  |
| 75    | ESME ERROR          | Internal error                                  | Report to Sinch                                                                    | Temporary  |
| 76    | ESME ERROR          | Internal error                                  | Report to Sinch                                                                    | Temporary  |
| 77    | ESME ERROR          | IMSI lookup blocked                             |                                                                                    | Permanent  |
| 100   | SMSC ERROR          | Unidentified Subscriber                         |                                                                                    | Permanent  |
| 101   | SMSC ERROR          | Facility not supported                          |                                                                                    | Temporary  |
| 102   | SMSC ERROR          | System failure                                  |                                                                                    | Temporary  |
| 103   | SMSC ERROR          | Unexpected data value                           |                                                                                    | Permanent  |
| 104   | SMSC ERROR          | Data missing                                    |                                                                                    | Permanent  |
| 105   | SMSC ERROR          | Equipment protocol error                        |                                                                                    | Permanent  |
| 106   | SMSC ERROR          | Unknown service centre address                  |                                                                                    | Temporary  |
| 107   | SMSC ERROR          | Service centre congestion                       |                                                                                    | Temporary  |
| 108   | SMSC ERROR          | Invalid short message entity address            |                                                                                    | Permanent  |
| 109   | SMSC ERROR          | Subscriber not service centre subscriber        |                                                                                    | Temporary  |
| 110   | SMSC ERROR          | Reject                                          | Indicates temporary problem or lost reach                                          | Permanent  |
| 111   | SMSC ERROR          | Local Cancel                                    | Indicates temporary problem or lost reach                                          | Temporary  |
| 112   | SMSC ERROR          | Abort                                           | Indicates temporary problem or lost reach                                          | Temporary  |
| 113   | SMSC ERROR          | Exception (internal)                            | Report to Sinch                                                                    | Permanent  |
| 114   | SMSC ERROR          | Unknown error                                   |                                                                                    | Temporary  |
| 150   | HLR ERROR           | Unknown subscriber                              | Message is rejected because there is no directory number for the mobile subscriber | Permanent  |
| 151   | HLR ERROR           | Call barred                                     | Message is rejected due to barring of the mobile subscriber                        | Permanent  |
| 152   | HLR ERROR           | Teleservice not provisioned                     | Message is rejected because the recipient mobile subscriber has no SMS subscription | Permanent  |
| 153   | HLR ERROR           | Absent subscriber                               |                                                                                    | Temporary  |
| 154   | HLR ERROR           | Facility not supported                          | The message is rejected due to no provision of the SMS in the VPLMN                | Permanent  |
| 155   | HLR ERROR           | System failure                                  | Message rejected due to network or protocol failure                                | Temporary  |
| 156   | HLR ERROR           | Unexpected data value                           |                                                                                    | Permanent  |
| 157   | HLR ERROR           | Data missing                                    |                                                                                    | Permanent  |
| 158   | HLR ERROR           | Memory capacity exceeded                        | Message rejected because the mobile subscriber doesn’t have enough memory          | Temporary  |
| 159   | HLR ERROR           | Mobile subscriber not reachable                 |                                                                                    | Temporary  |
| 160   | HLR ERROR           | Reject                                          | Indicates temporary problem or lost reach                                          | Permanent  |
| 161   | HLR ERROR           | Local Cancel                                    | Indicates temporary problem or lost reach                                          | Temporary  |
| 162   | HLR ERROR           | Abort                                           | Indicates temporary problem or lost reach                                          | Temporary  |
| 163   | HLR ERROR           | Exception (internal)                            | Report to Sinch (Local error)                                                      | Permanent  |
| 164   | HLR ERROR           | Unknown error                                   |                                                                                    | Temporary  |
| 200   | MSC ERROR           | Unidentified subscriber                         |                                                                                    | Temporary  |
| 201   | MSC ERROR           | Absent subscriber, IMSI detached                | Subscriber is absent and has been for a period of time                            | Temporary  |
| 202   | MSC ERROR           | Absent subscriber, no page response             | The message is rejected because there was no paging response                      | Temporary  |
| 203   | MSC ERROR           | Subscriber busy for MT SMS                      | The message is rejected because of congestion encountered at the visited MSC      | Temporary  |
| 204   | MSC ERROR           | Facility not supported                          | The message is rejected due to no provision of the SMS in the destination SIM      | Permanent  |
| 205   | MSC ERROR           | Illegal subscriber                              | Message rejected because of failed authentication                                  | Permanent  |
| 206   | MSC ERROR           | Illegal equipment                               | Message rejected because the mobile subscriber was blocklisted                    | Permanent  |
| 207   | MSC ERROR           | System failure                                  | Message rejected due to network or protocol failure                                | Temporary  |
| 208   | MSC ERROR           | Unexpected data value                           |                                                                                    | Permanent  |
| 209   | MSC ERROR           | Data missing                                    |                                                                                    | Permanent  |
| 210   | MSC ERROR           | Memory capacity exceeded                        | Message rejected because the mobile subscriber doesn’t have enough memory          | Temporary  |
| 211   | MSC ERROR           | Equipment protocol error                        |                                                                                    | Temporary  |
| 212   | MSC ERROR           | Equipment not short message equipped            |                                                                                    | Temporary  |
| 213   | MSC ERROR           | Reject                                          | Indicates temporary problem or lost reach                                          | Permanent  |
| 214   | MSC ERROR           | Local Cancel                                    | Indicates temporary problem or lost reach                                          | Temporary  |
| 215   | MSC ERROR           | Abort                                           | Indicates temporary problem or lost reach                                          | Temporary  |
| 216   | MSC ERROR           | Exception (internal)                            | Report to Sinch                                                                    | Permanent  |
| 217   | MSC ERROR           | Unknown error                                   |                                                                                    | Temporary  |
| 250   | SCREENING ERROR     | Error, personal service barring, MO Personal De | termined Barring White List                                                        | Permanent  |
| 251   | SCREENING ERROR     | Error, personal service barring, MO Personal De | termined Barring Black List                                                        | Permanent  |
| 252   | SCREENING ERROR     | Error, personal service barring, MO Operator De | termined Barring White List                                                        | Permanent  |
| 253   | SCREENING ERROR     | Error, personal service barring, MO Operator De | termined Barring Black List                                                        | Permanent  |
| 254   | SCREENING ERROR     | Error, personal service barring, MT Personal De | termined Barring White List                                                        | Permanent  |
| 255   | SCREENING ERROR     | Error, personal service barring, MT Personal De | termined Barring Black List                                                        | Permanent  |
| 256   | SCREENING ERROR     | Error, personal service barring, MT Operator De | termined Barring White List                                                        | Permanent  |
| 257   | SCREENING ERROR     | Error, personal service barring, MT Operator De | termined Barring Black List                                                        | Permanent  |
| 300   | ESME EXTERNAL ERROR | Invalid destination address                     |                                                                                    | Permanent  |
| 301   | ESME EXTERNAL ERROR | Invalid destination numbering plan              |                                                                                    | Permanent  |
| 302   | ESME EXTERNAL ERROR | Invalid destination type of number              |                                                                                    | Permanent  |
| 303   | ESME EXTERNAL ERROR | Invalid destination flag                        |                                                                                    | Permanent  |
| 304   | ESME EXTERNAL ERROR | Invalid number of destinations                  |                                                                                    | Permanent  |
| 310   | ESME EXTERNAL ERROR | Invalid source address                          |                                                                                    | Permanent  |
| 311   | ESME EXTERNAL ERROR | Invalid source numbering plan                   |                                                                                    | Permanent  |
| 312   | ESME EXTERNAL ERROR | Invalid source type of number                   |                                                                                    | Permanent  |
| 320   | ESME EXTERNAL ERROR | ESME Receiver permanent error                   |                                                                                    | Permanent  |
| 321   | ESME EXTERNAL ERROR | ESME Receiver reject error                      |                                                                                    | Permanent  |
| 322   | ESME EXTERNAL ERROR | ESME Receiver temporary error                   |                                                                                    |            |
| 330   | ESME EXTERNAL ERROR | Invalid command length                          |                                                                                    | Permanent  |
| 331   | ESME EXTERNAL ERROR | Invalid service type                            |                                                                                    | Permanent  |
| 332   | ESME EXTERNAL ERROR | Invalid operation                               |                                                                                    | Permanent  |
| 333   | ESME EXTERNAL ERROR | Operation not allowed                           |                                                                                    | Permanent  |
| 334   | ESME EXTERNAL ERROR | Invalid parameter                               |                                                                                    | Permanent  |
| 335   | ESME EXTERNAL ERROR | Parameter not allowed                           |                                                                                    | Permanent  |
| 336   | ESME EXTERNAL ERROR | Invalid parameter length                        |                                                                                    | Permanent  |
| 337   | ESME EXTERNAL ERROR | Invalid optional parameter                      |                                                                                    | Permanent  |
| 338   | ESME EXTERNAL ERROR | Optional parameter missing                      |                                                                                    | Permanent  |
| 339   | ESME EXTERNAL ERROR | Invalid validity parameter                      |                                                                                    | Permanent  |
| 340   | ESME EXTERNAL ERROR | Invalid scheduled delivery parameter            |                                                                                    | Permanent  |
| 341   | ESME EXTERNAL ERROR | Invalid distribution list                       |                                                                                    | Permanent  |
| 342   | ESME EXTERNAL ERROR | Invalid message class                           |                                                                                    | Permanent  |
| 343   | ESME EXTERNAL ERROR | Invalid message length                          |                                                                                    | Permanent  |
| 344   | ESME EXTERNAL ERROR | Invalid message reference                       |                                                                                    | Permanent  |
| 345   | ESME EXTERNAL ERROR | Invalid number of messages                      |                                                                                    | Permanent  |
| 346   | ESME EXTERNAL ERROR | Invalid predefined message                      |                                                                                    | Permanent  |
| 347   | ESME EXTERNAL ERROR | Invalid priority                                |                                                                                    | Permanent  |
| 348   | ESME EXTERNAL ERROR | Invalid replace flag                            |                                                                                    | Permanent  |
| 349   | ESME EXTERNAL ERROR | Request failed                                  |                                                                                    | Permanent  |
| 350   | ESME EXTERNAL ERROR | Invalid delivery report request                 |                                                                                    | Temporary  |
| 360   | ESME EXTERNAL ERROR | Message queue full                              |                                                                                    | Temporary  |
| 361   | ESME EXTERNAL ERROR | External error                                   | Report to Sinch                                                                    | Temporary  |
| 362   | ESME EXTERNAL ERROR | External error                                   | Report to Sinch                                                                    | Temporary  |
| 370   | ESME EXTERNAL ERROR | Cannot find information                         |                                                                                    | Temporary  |
| 399   | ESME EXTERNAL ERROR | Unknown                                         |                                                                                    | Temporary  |
