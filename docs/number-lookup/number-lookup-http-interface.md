---
title: HTTP Interface
excerpt: HTTP Interface for the Sinch Number Lookup service.
next:
  pages:
    - number-lookup-enum-interface
    - number-lookup-glossary
---
## How To Connect

Please find the hosts and port numbers for our Number Lookup HTTP/HTTPS server in the table below. Your username and password is available in the CAD (Client Account Details) document provided to you by your account manager.

### For servers in Europe

|               Server                     |         Host:Port                            |
| ---------------------------------- | ----------------------------------- |
| Primary Number Lookup HTTP Server  | http-eu3.clxcommunications.com:3700 |
| Backup Number Lookup HTTP Server   | http-eu1.clxcommunications.com:3700 |
| Primary Number Lookup HTTPS Server | http-eu3.clxcommunications.com:3701 |
| Backup Number Lookup HTTPS Server  | http-eu1.clxcommunications.com:3701 |

### For servers in the U.S.

|              Server                      |         Host:Port                            |
| ---------------------------------- | ----------------------------------- |
| Primary Number Lookup HTTP Server  | http-us3.clxcommunications.com:3700 |
| Backup Number Lookup HTTP Server   | http-us2.clxcommunications.com:3700 |
| Primary Number Lookup HTTPS Server | http-us3.clxcommunications.com:3701 |
| Backup Number Lookup HTTPS Server  | http-us2.clxcommunications.com:3701 |

## Query Specification

### Request

The request has the format:

**Format**
```html
http://<username>:<password>@<server>:<port>/lookup?msisdn=[&nocache]

```


|         Parameter                  |            Description                      |
| ------------------------- | -------------------------------- |
| \<username\>:\<password\> | Basic authentication parameters  |
| \<server\>                | Authority \[93.158.78.4\]        |
| \<port\>                  | Port number \[3700\]             |
| \<msisdn\>                | MSISDN in international format   |
| nocache                   | Bypass cache function (optional) |

### Response

One of the responses when using Number Portability Lookup service:

**Example 1**
```html
result=<result>;imsi=<imsi>
```


**Example 2**
```html
result=<result>;imsi=<imsi>;location=<location>
```


Response when using the Real-Time Lookup service:
```html
result=<result>;imsi=<imsi>;location=<location>

```


|      Parameter         |                    Description                                                        |
| ------------- | -------------------------------------------------------------------------- |
| \<result\>    | Lookup result (Response Code)                                              |
| \<imsi\>      | MCC+MNC corresponding to the MSISDN (only included when result is OK)      |
| \<location\>  | Country Code of current handset location (only included when result is OK) |

## Response Codes

|      Result                                |              Description                                                                               |
| ------------------------------------ | ------------------------------------------------------------------------------------------- |
| OK                                   | The request was successful                                                                  |
| DATA\_MISSING                        | The data was missing                                                                        |
| UNKNOWN\_SUBSCRIBER                  | The subscriber is unknown                                                                   |
| CALL\_BARRED                         | The service is restricted by the destination network                                        |
| ABSENT\_SUBSCRIBER\_SM               | The subscriber is absent                                                                    |
| UNEXPECTED\_DATA\_VALUE              | An unexpected data value in the request                                                     |
| SYSTEM\_FAILURE                      | A system failure occurred in the HLR                                                        |
| FACILITY\_NOT\_SUPPORTED             | Short message facility is not supported                                                     |
| TELE\_SERVICE\_NOT\_PROVISIONED      | SMS teleservice is not provisioned                                                          |
| HLR\_REJECT                          | The HLR request was rejected                                                                |
| HLR\_ABORT                           | The HLR (or some other entity) aborted the request. No response to the request was received |
| HLR\_LOCAL\_CANCEL                   | No response for the HLR request was received                                                |
| TIMEOUT                              | No response to the request was received                                                     |
| REQUEST\_THROTTLED                   | Maximum ongoing requests exceeded                                                           |
| IMSI\_LOOKUP\_BLOCKED                | Request is blocked                                                                          |
| Mandatory parameter msisdn not found | Some mandatory parameter are missing in the request                                         |
| MSISDN range is not accepted         | The number are not allowed on this service                                                  |
| msisdn is invalid                    | Wrong format of the MSISDN parameter                                                        |

## Examples

### HTTP Request

**Request**
```shell
http://username:password@93.158.78.4:3700/lookup?msisdn=46708100100

```


**HTTP Request with cache function bypass**

### Successful response, Number Portability Lookup

**Successful response**
```shell
result=OK;imsi=24008

or

result=OK;imsi=24008;location=46
```


### Unsuccessful response

**Unsuccessful response**
```shell
result=UNKNOWN_SUBSCRIBER

```


### Request with successful response, using curl
```curl
$ curl -v -u user:password http://93.158.78.4:3700/lookup?msisdn=46708100200
* About to connect() to 93.158.78.4 port 3700 (#0)
* Trying 93.158.78.4…
* Connected to 93.158.78.4 (93.158.78.4) port 3700 (#0)
* Server auth using Basic with user ‘user’
> GET /lookup?msisdn=46708100200 HTTP/1.1
> Authorization: Basic dXNlcjpwYXNzd29yZA==
> User-Agent: curl/7.30.0
> Host: 93.158.78.4:3700
> Accept: */*
>
< HTTP/1.1 200 OK, Success
< Date: Mon, 12 Feb 2014 11:00:41 +0100
< Content-Length: 20
< Content-Type: text/plain; charset=ISO-8859-1
<
* Connection #0 to host 93.158.78.4 left intact

result=OK;imsi=24004
```
