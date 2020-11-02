---
title: HTTP Basic
excerpt: >-
  Our basic HTTP API offers a simple way to send and receive single SMS
  messages. For more features such as bulk send, group send, scheduling,
  parameterization and higher security you may want to consider our REST SMS
  API.
---
> **Note**    
>
> You need to reach out to our Sales team to get your credentials to use this service. You can contact them [here](https://www.sinch.com/contact-us/).

## Outbound SMS HTTP

### Account details

Your account details are found in the Customer Account Details document received by your account manager and are used according to below table.

Your available host options will be visible in the Customer Account Details document.

|          |                                     |
| -------- | ----------------------------------- |
| Host     | http-\<host\>.clxcommunications.com |
| Port     | 3800 (use 3801 for SSL)             |
| URL      | /sendsms?                           |
| User     | \<user\>                            |
| Password | \<password\>                        |

### CGI Variables / Query String parameters

|Variable|Description|Type             |O/M|
|--------|-----------|-----------------|---|
|username|Username or account name.|String           |[M]|
|password|Password associated with given username.|String           |[M]|
|from    |Phone number of the sender  Short number max length is 16  Alphanumeric sender is limited to GSM default alphabet with max length 11  MSISDN sender max length is 18|String           |[M]|
|to      |Phone number of the received.  Plus signs (+) must be URL encoded|                 |[M]|
|text    |Contents of the message, URL encoded as necessary.|String           |[M]|
|charset |Charset of text message.  Used to convert to a format suitable for 7-bit or UCS2. Defaults to ISO-8859-1  if coding is 7-bit and UTF16BE if coding is UCS2|String           |[O]|
|udh     |Optional User Data Header (UDH) part of the message. Must be URL encoded.|String           |[O]|
|mclass  |Sets the Message Class in DCS Field.  Accepts values between 0 and 3.  0 = Sends the message directly to display  1 = Sends to mobile  2 = Sends to SIM  3 = Sends to SIM Toolkit|Number           |[O]|
|mwi     |Sets Message Waiting Indicator bits in the DCS field. If given,  the message will be encoded as Message Waiting Indicator.  The accepted,values are 0, 1, 2, 3 for activating the voice,  fax, e-mail and other,indicator.  Or 4, 5, 6, 7 for deactivating respectively.|Number           |[O]|
|coding  |Sets the coding scheme bits in DCS field.  Accepts values between 0 and 2.  0 = 7-bit  1 = 8-bit  2 = UCS2  If unset, defaults to 7-bit unless a UDH is defined, which sets coding to 8-bit.|Number           |[O]|
|validity|If given, Sinch will,only try to send the message for this many minutes.  If the destination,mobile is unreachable the SMSC discards the message.|Number (Minutes) |[O]|
|deferred|If given, the message will be postponed to be  delivered at now plus this many minutes|Number (Minutes) |[O]|
|dlr-mask|Request for delivery reports with the state of the sent message. The value is a bit mask composed of:  1 = Delivered to phone  2 = Non-delivered to Phone  4 = Queued on SMSC  8 = Delivered to SMSC  16 = Non-delivered to SMSC  If you want multiple report types, you simply add the values together.  For,example if you want to get delivery (1) and non-delivery (2) you set  the dlr-mask value to 3 (1+2). If given, dlr-url must be given as well.|Number (bit mask)|[O]|
|dlr-url |If dlr-mask is given, this is the URL to be fetched. (Must be url-encoded)|String (url)     |[O]|
|pid     |Sets the PID value [2]. For example, a SIM Toolkit message would use something like the following:  &pid=127&coding=2&alt-dcs=1&mclass=3|Number           |[O]|
|alt-dcs |If unset, 0X per default.  1= uses FX.  2 = force 0X|Number           |[O]|
|id      |A user provided identity of the message.  If DLR is requested it will be,possible to have this user provided identity included into DLR-URL.  Maximum length of this field is 32 bytes.|String           |[O]|

`O` = Optional

`M` = Mandatory

### DLR-URL Escape Codes

|Escape codes|Description|
|-------------|------------|
|%t          |The time the message was sent, formatted as “YYYY-MM-DD HH:MM” , e.g., “1999-09-21+14:18:00”|
|%T          |The time the message was sent, in UNIX epoch timestamp format.|
|%p          |The phone number of the sender of the SMS message.|
|%P          |The phone number of the received of the SMS message.|
|%q          |Like %p, but a leading ‘00’ is replaced with ‘+’.|
|%Q          |Like %P, but a leading ‘00’ is replaced with ‘+’.|
|%d          |The delivery report type value (check dlr-mask in chapter 2)|
|%A          |The delivery report SMSC reply, if any.|
|%i          |the message identity.|
|%I          |the user provided identity for this message, i.e. the id provided via ‘id’ parameter in sendsms request|
|%e          |the error code of the SMS delivery|
|%s          |the status of the SMS delivery. Following statuses can be returned:  UNDELIV  DELIVRD  ENROUTE  DELETED EXPIRED UNKNOWN |
|%u          |the done date, formatted as as “YYYY-MM-DD HH:MM”, e.g., “1999- 09-21 14:18”|
|%U          |the done date, in UNIX epoch timestamp format|
|%m          |the MCC + MNC of the receiver for the SMS message|


### Time zone

Sinch SMSC uses UTC (Coordinated Universal Time)

### Response Format

#### Message Accepted

All requests that are correct will be accepted and the SMS server responds with status HTTP/1.1 202 and a message ID as content.

Test case with correct settings:

**Correct Settings**
```shell
$ curl -v “http://sms1.clxnetworks.net:3800/sendsms?username=user&password=pass&from=CLX&to=46700 123456&text=Test”
* About to connect() to sms1.clxnetworks.net port 3800 (#0)
*           Trying 93.158.78.4â€¦ connected
* Connected to sms1.clxnetworks.net (93.158.78.4) port 3800 (#0)
> GET /sendsms?username=user&password=pass&from=CLX&to=46700123456&text=Test HTTP/1.1
> User-Agent: curl/7.19.7 (universal-apple-darwin10.0) libcurl/7.19.7 OpenSSL/0.9.8l zlib/1.2.3
> Host: sms1.clxnetworks.net:3800
> Accept: */*
>
< HTTP/1.1 202 Accepted
< Date: Tue, 9 Nov 2010 12:38:58 +0100
< Content-Length: 32
< Content-Type: text/plain; charset=ISO-8859-1
* Connection #0 to host sms1.clxnetworks.net left intact
* Closing connection #0
12c306e85bc5d9e4e52211c741e21277
```


#### Message rejected

All requests that are failing get a corresponding HTTP status code and also a fail message as content.

Test case with wrong settings - “mclass=123”

**Wrong Settings**
```shell
$ curl -v “http://sms1.clxnetworks.net:3800/sendsms?username=user&password=pass&from=CLX&to=46700 123456&mclass=123&text=Test”
* About to connect() to sms1.clxnetworks.net port 3800 (#0)
*           Trying 93.158.78.4â€¦ connected
* Connected to sms1.clxnetworks.net (93.158.78.4) port 3800 (#0)
> GET /sendsms?username=user&password=pass&from=CLX&to=46700123456&mclass=123&text=Test HTTP/1.1
> User-Agent: curl/7.19.7 (universal-apple-darwin10.0) libcurl/7.19.7 OpenSSL/0.9.8l zlib/1.2.3
> Host: sms1.clxnetworks.net:3800
> Accept: */*
>
< HTTP/1.1 400 Bad Request - server could not understand request
< Date: Tue, 9 Nov 2010 12:42:41 +0100
< Content-Length: 20
< Content-Type: text/plain; charset=ISO-8859-1
<
* Connection #0 to host sms1.clxnetworks.net left intact
* Closing connection #0
Invalid mclass value
```


|Error Message (as content)|HTTP Status 1|Code|
|--------------------------|-------------|----|
|Not acceptable 3          |NOT_ACCEPTABLE|406 |
|Method not allowed, use GET|METHOD_NOT_ALLOWED|405 |
|Query missing             |BAD_REQUEST  |400 |
|Syntax error              |BAD_REQUEST  |400 |
|User/Password parameter missing in the request  (Basic Authorization is NOT used)|NON_AUTHORITATIVE_INFORMATION|203 |
|Account not found         |UNAUTHORIZED |401 |
|Originator IP address is not authorized|UNAUTHORIZED |401 |
|Account blocked           |PAYMENT_REQUIRED|402 |
|Incorrect password        |UNAUTHORIZED |401 |
|Account throttled         |TEMPORARY_REDIRECT|307 |
|From number missing       |BAD_REQUEST  |400 |
|Charset not supported     |BAD_REQUEST  |400 |
|From number blocked       |FORBIDDEN    |403 |
|To number missing         |BAD_REQUEST  |400 |
|To number blocked         |FORBIDDEN    |403 |
|User data header incorrectly formatted|BAD_REQUEST  |400 |
|User data header too long |BAD_REQUEST  |400 |
|Message ID parameter not found|BAD_REQUEST  |400 |
|Invalid coding            |BAD_REQUEST  |400 |
|Invalid coding value      |BAD_REQUEST  |400 |
|Invalid mclass            |BAD_REQUEST  |400 |
|Invalid mclass value      |BAD_REQUEST  |400 |
|Invalid alt-dcs           |BAD_REQUEST  |400 |
|Invalid alt-dcs value     |BAD_REQUEST  |400 |
|Invalid parameter combination, alt-dcs can  only be set to 1 in conjunction with mclass|BAD_REQUEST  |400 |
|Invalid parameter combination, alt-dcs cannot  be set to 1 when coding is UCS2|BAD_REQUEST  |400 |
|Invalid mwi               |BAD_REQUEST  |400 |
|Invalid mwi value         |BAD_REQUEST  |400 |
|Invalid parameter combination, mwi can only be set with 7bit coding|BAD_REQUEST  |400 |
|Invalid parameter combination, mclass and mwi cannot coexist|BAD_REQUEST  |400 |
|Invalid validity          |BAD_REQUEST  |400 |
|Invalid validity value    |BAD_REQUEST  |400 |
|Invalid deferred          |BAD_REQUEST  |400 |
|Invalid deferred value    |BAD_REQUEST  |400 |
|Invalid pid               |BAD_REQUEST  |400 |
|Invalid pid value         |BAD_REQUEST  |400 |
|Invalid dlr-mask          |BAD_REQUEST  |400 |
|Invalid dlr-mask value    |BAD_REQUEST  |400 |
|Invalid parameter combination, dlr-url set but no dlr-mask|BAD_REQUEST  |400 |
|Invalid dlr-url length    |BAD_REQUEST  |400 |
|Invalid dlr-url, cannot find / after domain|BAD_REQUEST  |400 |
|Invalid dlr-url, invalid port value|BAD_REQUEST  |400 |
|Invalid dlr-url, unknown host|BAD_REQUEST  |400 |
|Invalid parameter combination, dlr-mask set but no dlr-url|BAD_REQUEST  |400 |
|Text too long to fit in one SMS and auto concat not allowed|BAD_REQUEST  |400 |
|Text and udh too long to fit in one SMS|BAD_REQUEST  |400 |
|Incorrect binary coding of text, should be e.g. %01%a0%34|BAD_REQUEST  |400 |
|Binary text plus idh too long to fit in one SMS|BAD_REQUEST  |400 |
|Charset UTH-16BE not supported|BAD_REQUEST  |400 |
|UCS2 text plus udh too long to fit in one SMS|BAD_REQUEST  |400 |
|Unknown error             |BAD_REQUEST  |400 |

### Examples

Basic Configuration

| Variable     | Value                                            |
| ------------ | ------------------------------------------------ |
| http-API URL | <http://sms1.clxnetworks.net:3800/sendsms>?      |
| username     | userX                                            |
| password     | passX                                            |
| from         | Test                                             |
| to           | 123456                                           |
| message      | “Hello world”                                    |
| dlr-mask     | 31                                               |
| dlr-url      | <http://your.host.com/dlr.php?type=%d&dr-msg=%A> |

#### Example 1 - Submit text message

**Submit Text Message**

```shell
http://sms1.clxnetworks.net:3800/sendsms?username=userX&password=passX&from=test&to=123456&text=Hello+world
```


**(Content of the message must be URL encoded)**


|                                                                                  |                                                                                                                                        |
| -------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------- |
| **Variable / Values**                                                            | **Comment**                                                                                                                            |
| <http://sms1.clxnetworks.net:3800/sendsms>                                       | http-API URL                                                                                                                           |
| username=userX                                                                   | Set username to userX                                                                                                                  |
| password=passX                                                                   | Set password to passX                                                                                                                  |
| from=test                                                                        | Set originator to test                                                                                                                 |
| to=123456                                                                        | Set MSISDN to 123456                                                                                                                   |
| text=Hello+world                                                                 | Set message content to “Hello world”                                                                                                   |
| dlr-mask=7                                                                       | Set dlr-mask to 7 (see How dlr-mask works for further explanations)                                                                    |
| dlr-url=http%3a%2f%2fyour.host.com%2fdlr.php%3ftype%3d%25d%26dr-%c2%admsg%3d%25A | Set the url where delivery reports will be sent and which values, must be URL encoded (see How dlr-url works for further explanations) |

#### Example 2 - Submit text message with delivery report

**Submit text message with delivery report**

```shell
http://sms1.clxnetworks.net:3800/sendsms?username=userX&password=passX&from=test&to=123456&text=Hello+world&dlr-mask=7&dlr-url=http%3a%2f%2fyour.host.com%2fdlr.php%3ftype%3d%25d%26dr-%c2%admsg%3d%25A

```


| Value (decimal) | Description            |
| --------------- | ---------------------- |
| 1               | Delivered to phone     |
| 2               | Non-Delivered to phone |
| 4               | Queued on SMSC         |
| 8               | Delivered to SMSC      |
| 16              | Non-Delivered to SMSC  |

If you want multiple report types, you simply add the values together. For example, if you want to get delivery success and/or failure you set the dlr-mask value to 7 (1+2+4).

Enter the url where you want the dlr request to be sent example:

**Example**
```shell
http://your.host.com/dlr.php

```


Then add what type of dlr-url escape codes you which to receive.

|Escape codes|                                                                    |
|------------|--------------------------------------------------------------------|
|%t          |The time the message was sent, formatted as “YYYY-MM-DD HH:MM”.     |
|%T          |The time the message was sent, in UNIX epoch timestamp format.      |
|%p          |The phone number of the sender of the SMS message.                  |
|%P          |The phone number of the receiver of the SMS message.                |
|%q          |Like %p, but a leading ‘00’ is replaced with ‘+’.                   |
|%Q          |Like %P, but a leading ‘00’ is replaced with ‘+’.                   |
|%d          |The delivery report type value (check dlr-mask for more information)|
|%A          |The delivery report SMSC reply, if any.                             |

**Example**
```shell
type=%d&dr-msg=%A
```


One example of a delivery report received: (Note that the delivery report is url-encoded)

**Delivrey Report Received**
```shell
/dlr.php?type=1&dr-msg=id%3A128fc8d26225d9e4e52097f4249028b7%2Bsub%3A001%2Bdlvrd%3A001
%2Bsubmit%2Bdate%3A1006030843%2Bdone%2Bdate%3A1006030843%2Bstat%3ADELIVRD
%2Berr%3A000%2Btext%3A%2B%2B%2B%2B%2B%2B%2B%2B%2B%2B%2B%2B
%2B%2B%2B%2B%2B%2B%2B%2B
```


The delivery report decoded:

**Delivery Report Decoded**
```shell
/dlr.php?type=1&dr-msg=id:128fc8d26225d9e4e52097f4249028b7+sub:001+dlvrd:001+submit
+date:1006030843+done+date:1006030843+stat:DELIVRD+err:000
+text:++++++++++++++++++++
```


#### Example 3 - Submit flash message


**Example**
```shell
http://sms1.clxnetworks.net:3800/sendsms?username=userX&password=passX&from=test&to=123456&text=Hello+world&alt-dcs=1&mclass=0

```


| Variable / Values                          | Comment                               |
| ------------------------------------------ | ------------------------------------- |
| <http://sms1.clxnetworks.net:3800/sendsms> | http-API URL                          |
| username=userX                             | Set username to userX                 |
| password=passX                             | Set password to passX                 |
| from=test                                  | Set originator to test                |
| to=123456                                  | Set MSISDN to 123456                  |
| text=Hello+world                           | Set message content to “Hello world”  |
| alt-dcs=1                                  | Uses FX                               |
| mclass=0                                   | Sends the message directly to display |

Parameter “mclass” sets the Message Class in DCS field.

|          |                                       |
| -------- | ------------------------------------- |
| mclass=0 | Sends the message directly to display |
| mclass=1 | Sends to mobile                       |
| mclass=2 | Sends to SIM                          |
| mclass=3 | Sends to SIM Toolkit                  |

Parameter “alt-dcs”:

  - If unset, 0X per default
  - If equals to 1, uses FX
  - If equals to 2, force 0X

#### Example 4 - Submit UCS2 message

Content not URL-Encoded:


**Not URL-Encoded**
```shell
http://sms1.clxnetworks.net:3800/sendsms?username=userX&password=passX&from=test&to=123456&text=??????+???&coding=2

```


Content URL-Encoded:

**URL-Encoded**
```shell
http://sms1.clxnetworks.net:3800/sendsms?username=userX&password=passX&from=test&to=123456&text=%D0%BF%D1%80%D0%B8%D0%B2%D0%B5%D1%82%20%D0%BC%D0%B8%D1%80&coding=2

```


|Variable / Values|Comment                                                             |
|-----------------|--------------------------------------------------------------------|
|http://sms1.clxnetworks.net:3800/sendsms|http-API URL                                                        |
|username=userX   |Set username to userX                                               |
|password=passX   |Set password to passX                                               |
|from=test        |Set originator to test                                              |
|to=123456        |Set MSISDN to 123456                                                |
|text=Hello+world |Set message content to “Hello world”                                |
|alt-dcs=1        |Uses FX                                                             |
|mclass=0         |Sends the message directly to display                               |

Parameter “coding” sets the coding scheme bits in DCS field.

|          |       |
| -------- | ----- |
| coding=0 | 7-bit |
| coding=1 | 8-bit |
| coding=2 | UCS2  |

If unset, defaults to 7-bit unless a UDH is defined, which sets coding to 8-bit.

## Inbound SMS HTTP

### Destination URI

To receive the MO message Sinch needs a URL that specify where the messages should be sent. The URL will be passed as HTTP1.1 GET request. Sinch need:

>   - Hostname
>   - Port
>   - URL: Use escape codes when construct the URL

### URL escape codes

| **Escape codes**|Description|
|----|----|
| %t | The time the message was sent, formatted as "YYYY-MM-DD HH:MM".  |
| %T | The time the message was sent, in UNIX epoch timestamp format.   |
| %p | The MO number.                                                   |
| %P | The phone number of the sender.                                  |
| %q | Like %p, but a leading "00" is replaced with "+".                |
| %Q | Like %P, but a leading "00" is replaced with "+".                |
| %A | The short message content.                                       |
| %u | udh of incoming message.                                         |
| %C1 | MCC + MNC of the sender for the SMS message. (Short codes only) |

### Example

This URL will pass the MO message to http server "mo.yourdomain.net" on tcp port 5000. The example script to receive and pars the request is "yourGetMo.php". Sinch will escape the destination MSISDN to variable "msisdn", originator address to variable "originator" and the message to variable "msg".

The URL to use:

**URL to use**
```shell
http://mo.yourdomain.net:5000/yourGetMo.php?originator=%q&msisdn=%P&msg=%A
```


## Error Specification

### Overview

This specification covers error codes related to the HTTP communication.

### Messages states

A message will always be in one of the following states:

|                   |           |                                                            |
| ----------------- | --------- | ---------------------------------------------------------- |
| **Message state** | **Value** | **Description**                                            |
| ENROUTE           | 1         | Message is in the process of being sent to the destination |
| Delivered         | 2         | Message has been delivered to destination                  |
| EXPIRED           | 3         | Message validity period has expired                        |
| DELETED           | 4         | Message has been deleted                                   |
| UNDELIVERABLE     | 5         | Message is undeliverable                                   |
| UNKNOWN           | 7         | Message is in invalid state                                |

### Status reports error codes

The following are the error codes Sinch Networks will be sending:

| Error | Error Class         | Description                                                                | Comment                                                                            | Treated as |
| ----- | ------------------- | -------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- | ---------- |
| 0     | NO ERROR            | No error / Unknown error                                                   |                                                                                    | Temporary  |
| 1     | ROUTING ERROR       | Internal routing error                                                     | Report to Sinch                                                                    | Permanent  |
| 2     | ROUTING ERROR       | Internal routing error                                                     | Report to Sinch                                                                    | Permanent  |
| 3     | ROUTING ERROR       | Internal routing error                                                     | Report to Sinch                                                                    | Permanent  |
| 4     | ROUTING ERROR       | Internal routing error                                                     | Report to Sinch                                                                    | Temporary  |
| 5     | ROUTING ERROR       | Internal routing error                                                     | Report to Sinch                                                                    | Permanent  |
| 6     | ROUTING ERROR       | Internal routing error                                                     | Report to Sinch                                                                    | Permanent  |
| 7     | ROUTING ERROR       | Internal routing error                                                     | Report to Sinch                                                                    | Permanent  |
| 8     | ROUTING ERROR       | Internal routing error                                                     | Report to Sinch                                                                    | Permanent  |
| 9     | ROUTING ERROR       | Unsupported number plan                                                    |                                                                                    | Permanent  |
| 10    | ROUTING ERROR       | Unsupported type of number                                                 |                                                                                    | Permanent  |
| 11    | ROUTING ERROR       | Message not deliver                                                        |                                                                                    | Permanent  |
| 12    | ROUTING ERROR       | Dialling zone not found                                                    |                                                                                    | Permanent  |
| 13    | ROUTING ERROR       | Not home zone and IMSI not allowed                                         |                                                                                    | Permanent  |
| 14    | ROUTING ERROR       | Not home zone and IMSI fetch failed                                        |                                                                                    | Temporary  |
| 15    | SCREENING ERROR     | Screening block                                                            |                                                                                    | Permanent  |
| 16    | SCREENING ERROR     | Terminating IMSI blocked                                                   |                                                                                    | Permanent  |
| 17    | ROUTING ERROR       | Destination network type unknown                                           |                                                                                    | Permanent  |
| 18    | ESME ERROR          | ESME error                                                                 |                                                                                    | Temporary  |
| 19    | SCREENING ERROR     | Originating location mismatch                                              | Permanent                                                                          | Permanent  |
| 40    | INTERNAL ERROR      | Internal error                                                             | Report to Sinch                                                                    | Temporary  |
| 50    | INTERNAL ERROR      | Internal error                                                             | Report to Sinch                                                                    | Temporary  |
| 51    | INTERNAL ERROR      | Internal error                                                             | Report to Sinch                                                                    | Temporary  |
| 52    | INTERNAL ERROR      | Internal error                                                             | Report to Sinch                                                                    | Temporary  |
| 53    | INTERNAL ERROR      | Internal error                                                             | Report to Sinch                                                                    | Permanent  |
| 54    | INTERNAL ERROR      | Internal error                                                             | Report to Sinch                                                                    | Permanent  |
| 55    | INTERNAL ERROR      | Internal error                                                             | Report to Sinch                                                                    | Permanent  |
| 60    | SCREENING ERROR     | Error, originator blocked                                                  |                                                                                    | Permanent  |
| 61    | SCREENING ERROR     | Error, destination blocked                                                 |                                                                                    | Permanent  |
| 62    | SCREENING ERROR     | Error, keyword blocked                                                     |                                                                                    | Permanent  |
| 63    | SCREENING ERROR     | Error, SC address blocked                                                  |                                                                                    | Permanent  |
| 64    | SCREENING ERROR     | Error, blocked due to exceeded quota                                       |                                                                                    | Permanent  |
| 65    | SCREENING ERROR     | Error, loop detected                                                       |                                                                                    | Permanent  |
| 66    | SCREENING ERROR     | Error, data coding scheme blocked                                          |                                                                                    | Permanent  |
| 67    | SCREENING ERROR     | Error, information element identifier blocked                              |                                                                                    | Permanent  |
| 70    | ESME ERROR          | Internal error                                                             | Report to Sinch                                                                    | Temporary  |
| 71    | ESME ERROR          | Internal error                                                             | Report to Sinch                                                                    | Temporary  |
| 72    | ESME ERROR          | Internal error                                                             | Report to Sinch                                                                    | Permanent  |
| 73    | ESME ERROR          | Internal error                                                             | Report to Sinch                                                                    | Temporary  |
| 74    | ESME ERROR          | Internal error                                                             | Report to Sinch                                                                    | Temporary  |
| 75    | ESME ERROR          | Internal error                                                             | Report to Sinch                                                                    | Temporary  |
| 76    | ESME ERROR          | Internal error                                                             | Report to Sinch                                                                    | Temporary  |
| 77    | ESME ERROR          | IMSI lookup blocked                                                        |                                                                                    | Permanent  |
| 100   | SMSC ERROR          | Unidentified Subscriber                                                    |                                                                                    | Permanent  |
| 101   | SMSC ERROR          | Facility not supported                                                     |                                                                                    | Temporary  |
| 102   | SMSC ERROR          | System failure                                                             |                                                                                    | Temporary  |
| 103   | SMSC ERROR          | Unexpected data value                                                      |                                                                                    | Permanent  |
| 104   | SMSC ERROR          | Data missing                                                               |                                                                                    | Permanent  |
| 105   | SMSC ERROR          | Equipment protocol error                                                   |                                                                                    | Permanent  |
| 106   | SMSC ERROR          | Unknown service centre address                                             |                                                                                    | Temporary  |
| 107   | SMSC ERROR          | Service centre congestion                                                  |                                                                                    | Temporary  |
| 108   | SMSC ERROR          | Invalid short message entity address                                       |                                                                                    | Permanent  |
| 109   | SMSC ERROR          | Subscriber not service centre subscriber                                   |                                                                                    | Temporary  |
| 110   | SMSC ERROR          | Reject                                                                     | Indicates temporary problem or lost reach                                          | Permanent  |
| 111   | SMSC ERROR          | Local Cancel                                                               | Indicates temporary problem or lost reach                                          | Temporary  |
| 112   | SMSC ERROR          | Abort                                                                      | Indicates temporary problem or lost reach                                          | Temporary  |
| 113   | SMSC ERROR          | Exception (internal)                                                       | Report to Sinch                                                                    | Permanent  |
| 114   | SMSC ERROR          | Unknown error                                                              |                                                                                    | Temporary  |
| 150   | HLR ERROR           | Unknown subscriber                                                         | Message is rejected because there is no directory number for the mobile subscriber | Permanent  |
| 151   | HLR ERROR           | Call barred                                                                | Message is rejected due to barring of the MS                                       | Permanent  |
| 152   | HLR ERROR           | Teleservice not provisioned                                                | Message is rejected because the recipient MS has no SMS subscription               | Permanent  |
| 153   | HLR ERROR           | Absent subscriber                                                          |                                                                                    | Temporary  |
| 154   | HLR ERROR           | Facility not supported                                                     | The message is rejected due to no provision of the SMS in the VPLMN                | Permanent  |
| 155   | HLR ERROR           | System failure                                                             | Message rejected due to network or protocol failure                                | Temporary  |
| 156   | HLR ERROR           | Unexpected data value                                                      |                                                                                    | Permanent  |
| 157   | HLR ERROR           | Data missing                                                               |                                                                                    | Permanent  |
| 158   | HLR ERROR           | Memory capacity exceeded                                                   | Message rejected because the MS doesn’t have enough memory                         | Temporary  |
| 159   | HLR ERROR           | Mobile subscriber not reachable                                            |                                                                                    | Temporary  |
| 160   | HLR ERROR           | Reject                                                                     | Indicates temporary problem or lost reach                                          | Permanent  |
| 161   | HLR ERROR           | Local Cancel                                                               | Indicates temporary problem or lost reach                                          | Temporary  |
| 162   | HLR ERROR           | Abort                                                                      | Indicates temporary problem or lost reach                                          | Temporary  |
| 163   | HLR ERROR           | Exception (internal)                                                       | Report to Sinch (Local error)                                                      | Permanent  |
| 164   | HLR ERROR           | Unknown error                                                              |                                                                                    | Temporary  |
| 200   | MSC ERROR           | Unidentified subscriber                                                    |                                                                                    | Temporary  |
| 201   | MSC ERROR           | Absent subscriber, IMSI detached                                           | Subscriber is absent and have been for a period of time                            | Temporary  |
| 202   | MSC ERROR           | Absent subscriber, no page response                                        | The message is rejected because there was no paging response                       | Temporary  |
| 203   | MSC ERROR           | Subscriber busy for MT SMS                                                 | The message is rejected because of congestion encountered at the visited MSC       | Temporary  |
| 204   | MSC ERROR           | Facility not supported                                                     | The message is rejected due to no provision of the SMS in the destination SIM      | Permanent  |
| 205   | MSC ERROR           | Illegal subscriber                                                         | Message rejected because of failed authentication                                  | Permanent  |
| 206   | MSC ERROR           | Illegal equipment                                                          | Message rejected because the MS was black-listed                                   | Permanent  |
| 207   | MSC ERROR           | System failure                                                             | Message rejected due to network or protocol failure                                | Temporary  |
| 208   | MSC ERROR           | Unexpected data value                                                      |                                                                                    | Permanent  |
| 209   | MSC ERROR           | Data missing                                                               |                                                                                    | Permanent  |
| 210   | MSC ERROR           | Memory capacity exceeded                                                   | Message rejected because the MS doesn’t have enough memory                         | Temporary  |
| 211   | MSC ERROR           | Equipment protocol error                                                   |                                                                                    | Temporary  |
| 212   | MSC ERROR           | Equipment not short message equipped                                       |                                                                                    | Temporary  |
| 213   | MSC ERROR           | Reject                                                                     | Indicates temporary problem or lost reach                                          | Permanent  |
| 214   | MSC ERROR           | Local Cancel                                                               | Indicates temporary problem or lost reach                                          | Temporary  |
| 215   | MSC ERROR           | Abort                                                                      | Indicates temporary problem or lost reach                                          | Temporary  |
| 216   | MSC ERROR           | Exception (internal)                                                       | Report to Sinch                                                                    | Permanent  |
| 217   | MSC ERROR           | Unknown error                                                              |                                                                                    | Temporary  |
| 250   | SCREENING ERROR     | Error, personal service barring, MO Personal Determined Barring White List |                                                                                    | Permanent  |
| 251   | SCREENING ERROR     | Error, personal service barring, MO Personal Determined Barring Black List |                                                                                    | Permanent  |
| 252   | SCREENING ERROR     | Error, personal service barring, MO Operator Determined Barring White List |                                                                                    | Permanent  |
| 253   | SCREENING ERROR     | Error, personal service barring, MO Operator Determined Barring Black List |                                                                                    | Permanent  |
| 254   | SCREENING ERROR     | Error, personal service barring, MT Personal Determined Barring White List |                                                                                    | Permanent  |
| 255   | SCREENING ERROR     | Error, personal service barring, MT Personal Determined Barring Black List |                                                                                    | Permanent  |
| 256   | SCREENING ERROR     | Error, personal service barring, MT Operator Determined Barring White List |                                                                                    | Permanent  |
| 257   | SCREENING ERROR     | Error, personal service barring, MT Operator Determined Barring Black List |                                                                                    | Permanent  |
| 300   | ESME EXTERNAL ERROR | Invalid destination address                                                |                                                                                    | Permanent  |
| 301   | ESME EXTERNAL ERROR | Invalid destination numbering plan                                         |                                                                                    | Permanent  |
| 302   | ESME EXTERNAL ERROR | Invalid destination type of number                                         |                                                                                    | Permanent  |
| 303   | ESME EXTERNAL ERROR | Invalid destination flag                                                   |                                                                                    | Permanent  |
| 304   | ESME EXTERNAL ERROR | Invalid number of destinations                                             |                                                                                    | Permanent  |
| 310   | ESME EXTERNAL ERROR | Invalid source address                                                     |                                                                                    | Permanent  |
| 311   | ESME EXTERNAL ERROR | Invalid source numbering plan                                              |                                                                                    | Permanent  |
| 312   | ESME EXTERNAL ERROR | Invalid source type of number                                              |                                                                                    | Permanent  |
| 320   | ESME EXTERNAL ERROR | ESME Receiver permanent error                                              |                                                                                    | Permanent  |
| 321   | ESME EXTERNAL ERROR | ESME Receiver reject error                                                 |                                                                                    | Permanent  |
| 322   | ESME EXTERNAL ERROR | ESME Receiver temporary error                                              |                                                                                    |            |
| 330   | ESME EXTERNAL ERROR | Invalid command length                                                     |                                                                                    | Permanent  |
| 331   | ESME EXTERNAL ERROR | Invalid service type                                                       |                                                                                    | Permanent  |
| 332   | ESME EXTERNAL ERROR | Invalid operation                                                          |                                                                                    | Permanent  |
| 333   | ESME EXTERNAL ERROR | Operation not allowed                                                      |                                                                                    | Permanent  |
| 334   | ESME EXTERNAL ERROR | Invalid parameter                                                          |                                                                                    | Permanent  |
| 335   | ESME EXTERNAL ERROR | Parameter not allowed                                                      |                                                                                    | Permanent  |
| 336   | ESME EXTERNAL ERROR | Invalid parameter length                                                   |                                                                                    | Permanent  |
| 337   | ESME EXTERNAL ERROR | Invalid optional parameter                                                 |                                                                                    | Permanent  |
| 338   | ESME EXTERNAL ERROR | Optional parameter missing                                                 |                                                                                    | Permanent  |
| 339   | ESME EXTERNAL ERROR | Invalid validity parameter                                                 |                                                                                    | Permanent  |
| 340   | ESME EXTERNAL ERROR | Invalid scheduled delivery parameter                                       |                                                                                    | Permanent  |
| 341   | ESME EXTERNAL ERROR | Invalid distribution list                                                  |                                                                                    | Permanent  |
| 342   | ESME EXTERNAL ERROR | Invalid message class                                                      |                                                                                    | Permanent  |
| 343   | ESME EXTERNAL ERROR | Invalid message length                                                     |                                                                                    | Permanent  |
| 344   | ESME EXTERNAL ERROR | Invalid message reference                                                  |                                                                                    | Permanent  |
| 345   | ESME EXTERNAL ERROR | Invalid number of messages                                                 |                                                                                    | Permanent  |
| 346   | ESME EXTERNAL ERROR | Invalid predefined message                                                 |                                                                                    | Permanent  |
| 347   | ESME EXTERNAL ERROR | Invalid priority                                                           |                                                                                    | Permanent  |
| 348   | ESME EXTERNAL ERROR | Invalid replace flag                                                       |                                                                                    | Permanent  |
| 349   | ESME EXTERNAL ERROR | Request failed                                                             |                                                                                    | Permanent  |
| 350   | ESME EXTERNAL ERROR | Invalid delivery report request                                            |                                                                                    | Temporary  |
| 360   | ESME EXTERNAL ERROR | Message queue full                                                         |                                                                                    | Temporary  |
| 361   | ESME EXTERNAL ERROR | Extenal error                                                              | Report to Sinch                                                                    | Temporary  |
| 362   | ESME EXTERNAL ERROR | Extenal error                                                              | Report to Sinch                                                                    | Temporary  |
| 370   | ESME EXTERNAL ERROR | Cannot find information                                                    |                                                                                    | Temporary  |
| 399   | ESME EXTERNAL ERROR | Unknown                                                                    |                                                                                    | Temporary  |
