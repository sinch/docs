---
title: Appendix
excerpt: >-
  XML API error code reference and postback notification codes.
---

## Error Code Reference

| Code | Description                                                          |
| ---- | -------------------------------------------------------------------- |
| E100 | Invalid request, please make a valid XML Post request.               |
| E104 | Authentication failed, Invalid API-key.                              |
| E105 | No API access to this user.                                          |
| E106 | Time interval between each API call should be at least X seconds.    |
| E107 | Invalid action. This API user is not allowed to use this action      |
| E108 | Invalid XML. XML parsing failed.                                     |
| E110 | Invalid ‘to’ / receiver number.                                      |
| E111 | Invalid ‘from’ / shortcode.                                          |
| E112 | IP was not whitelisted. API call rejected.                           |
| E113 | Exceeded the allowed throughput for this API action.                 |
| E114 | Phone number is blacklisted. API call rejected.                      |
| E212 | page-number is invalid.                                              |
| E213 | items-per-page is invalid                                            |
| E214 | start-date is invalid.                                               |
| E223 | More than one object is not allowed in the same slide.               |
| E224 | MMS audio/video/image are not allowed with object in the same slide. |
| E225 | Exceeded number of slides allowed for the MMS.                       |
| E226 | MMS audio / video are not allowed in the same slide.                 |
| E227 | MMS video / image are not allowed in the same slide.                 |
| E228 | MMS text cannot exceed X characters.                                 |
| E229 | Content not allowed.                                                 |
| E230 | Invalid / bad slide duration in slide X.                             |
| E241 | Invalid mms-id / MMS do not exist.                                   |
| E311 | Invalid MMS name / name is required.                                 |
| E312 | No slides.                                                           |
| E313 | Slide X is empty.                                                    |
| E314 | Invalid message-subject / message-subject is required.               |
| E331 | Image in slide X is too big.                                         |
| E332 | Audio in slide X is too big.                                         |
| E333 | Video in slide X is too big.                                         |
| E334 | Text in slide X is too long.                                         |
| E335 | vCard in slide X is too big.                                         |
| E336 | iCal in slide X is too big.                                          |
| E337 | PDF in slide X is too big.                                           |
| E338 | Passbook file in slide X is too big.                                 |
| E341 | Image file in slide X is corrupted.                                  |
| E351 | Could not copy Image in slide X.                                     |
| E352 | Could not copy Audio in slide X.                                     |
| E353 | Could not copy Video in slide X.                                     |
| E355 | Could not copy vCard in slide X.                                     |
| E356 | Could not copy iCal in slide X.                                      |
| E357 | Could not copy PDF in slide X.                                       |
| E358 | Could not copy Passbook file in slide X.                             |
| E618 | Carrier lookup failed. Please retry.                                 |
| E619 | Carrier not provisioned.                                             |
| E620 | mms-id is required.                                                  |
| E622 | fallback-sms-text is required.                                       |
| E626 | MMS not available. Encoding in progress, try again later.            |
| E627 | Invalid service-id / service-id is required.                         |
| E628 | Mobile operator / carrier not supported.                             |
| E629 | Unrecognized content type.                                           |
| E650 | Invalid operator-id / operator-id is required.                       |
| E651 | Invalid operator-id / operator-id not found in the system.           |

## Postback Notification Codes

| Code | Description                                                             |
| ---- | ----------------------------------------------------------------------- |
| E100 | Invalid request, please make a valid XML Post request.                  |
| E002 | Encoding of MMS audio failed. (saveMMS function)                        |
| E003 | Encoding of MMS video failed. (saveMMS function)                        |
| E012 | Encoding of MMS audio failed. (sendMMS function)                        |
| E013 | Encoding of MMS video failed. (sendMMS function)                        |
| E101 | Error occurred. Impossible to send MMS.                                 |
| E102 | Error occurred. MMS Delivery was not successful.                        |
| E202 | Error occurred. MMS as SMS Delivery was not successful.                 |
| E501 | Error occurred. Impossible to send DDM Message.                         |
| E502 | Error occurred. DDM Delivery was not successful.                        |
| E999 | Post Message Sending Queue Processing Errors.                           |
| N101 | Notification that MMS was sent.                                         |
| N102 | Notification that MMS status was updated.                               |
| N202 | Notification that MMS was delivered as SMS.                             |
| N401 | MMS MO received successfully.                                           |
