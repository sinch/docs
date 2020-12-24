---
title: SMS Rest Guide
excerpt: >-
  The most feature rich API that Sinch offers is the SMS REST API. Single
  messages, scheduled batch send-outs, using message templates and more.
---
### Using the Rest api

To get started in minutes to [send your first SMS](doc:sms)

#### Authentication

You will be provided with an authentication token for each service plan.

The token is sent in the `Authorization` header preceded by `Bearer`. It is required for all requests made to the REST API.

**Example request with token to send a SMS**

``` shell
curl -X POST \
     -H "Authorization: Bearer {token}" \
     -H "Content-Type: application/json"  -d '
      {
          "from": "{Number you have on your account}",
          "to": [
              "123456789"
          ],
          "body": "Hi there! How are you?"
      }' \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches"
```

#### Base URL

The following URLs can be used by the REST API. We have servers in the US and EU. By default your account will be created in the US environment. If you'd like access to the European environment, please contact our support team.  


| Server        |  URL                                   |
|---------------|----------------------------------------|
| General API | https://us.sms.api.sinch.com     |
| Geographical APIs | If you want to set one up, contact your account manager     |
| US Production | https://us.sms.api.sinch.com     |
| EU Production | https://eu.sms.api.sinch.com     |

#### Rate Limits

Each service plan comes with a rate limit which sets the maximum number of messages that can be sent per second. The rate limit is calculated from all messages sent via the API, so a batch with 10 recipients will count as 10 messages for rate limiting purposes.

Each service plan gets it's own message queue served in First-In-First-Out order. This means that new batches will be accepted immediately but might be delayed if earlier batches are still on queue.

### SMS REST formats and conventions

This section will take a brief look at some of the formats used in the REST API.

#### JSON

JSON (`application/json`) is the content type of both requests and responses if not otherwise specified.

Requests with invalid JSON will be rejected.

Null values can be omitted in requests and will be omitted in responses. In some cases explicitly setting `null` will overwrite a previously set value with `null`. See [Update a group](#update-a-group) for an example.
New features might result in additional request and response
parameters. New request parameters will either have a default value or
considered optional to retain backwards compatibility. It is highly recommended to ignore any unexpected parameters when reading JSON in API responses and in callback handlers.

#### Phone numbers (MSISDN)

Only MSISDNs in international format are accepted by the API.

MSISDNs can be sent in with or without a leading `+` (i.e. `+123456789` or `123456789`) or with a leading `00`. Any space, dash or bracket will also be ignored by the API.

All MSISDNs returned by the REST API will be without a `+` or `00` prefix, even if they were sent in with one.

#### Timestamps

Timestamps are used for expressing a moment in time. They are represented using the [ISO-8601 standard](https://en.wikipedia.org/wiki/ISO_8601). Examples of those are the fields `send_at` and `expire_at` in the [Send a batch message](#send-a-batch-message) operation.

A time offset can be specified in accordance with [ISO-8601 time offsets from UTC](http://en.wikipedia.org/wiki/ISO_8601#Time_offsets_from_UTC). If no time offset is specified (_local time_ in ISO-8601) then UTC will be used.

All timestamps returned by the API will be represented in UTC time, with millisecond precision.

### HTTP Status Codes

The REST API returns an HTTP status and code each time a request is made.

##### HTTP Statuses

The following HTTP status codes are used by the API. Additional codes might be added in the future and if you encounter a code not in this list please consult the [HTTP specification](http://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html#sec10/) for a definition.

| Status    | Reason                           | Description                                                                                                                 |
|-- -       | ---                              | ---                                                                                                                       --|
| 200       | OK                               | The request was successful.                                                                                                 |
| 201       | Created                          | The `POST` request was successful and a new resource was created.                                                           |
| 400       | Bad&nbsp;Request                 | The request does not conform to the API. The response body should provide more information.                                 |
| 401       | Unauthorized                     | Authentication token is invalid for this service plan.                                                                      |
| 403       | Forbidden                        | The request syntax is valid but cannot be performed. This could for example be because a referenced resource doesn't exist. |
| 404       | Not&nbsp;Found                   | The path is invalid or no resource exists with the given ID.                                                                |
| 405       | Method&nbsp;Not&nbsp;Allowed     | The path is valid but not for this method.                                                                                  |
| 415       | Unsupported&nbsp;Media&nbsp;Type | The `Content-Type` header is missing or unsupported. Most operations expect `application/json`.                             |
| 429       | Too&nbsp;Many&nbsp;Requests      | The user or path has too many outstanding requests.                                                                         |
| 500       | Internal&nbsp;Server&nbsp;Error  | An unexpected internal error occurred and the request was not processed.                                                    |
| 503       | Service&nbsp;Unavailable         | The service is unable to perform the request at this point. Most likely due to a required subsystem being unavailable.      |


##### HTTP Errors

Responses with status `400 Bad Request` and `403 Forbidden` will present a JSON object in the body explaining the error. It has the following structure:

| Name   | Description                                                                                                             | JSON Type |
|-- -    | ---                                                                                                                     | ---     --|
| `code` | A code that can be used to programmatically recognize the error. See [below](#error-codes) for possible values. | `String`  |
| `text` | Human readable description of the error. Can be used for debugging.                                                     | `String`  |

##### Error codes

The following error codes can be returned as values for the `code` field:

| HTTP Status | Code                                | Description                                                                                                         |
|-- -         | ---                                 | ---                                                                                                               --|
| 400         | `syntax_invalid_json`               | The JSON in the request is invalid or does not conform to the API specification.                                    |
| 400         | `syntax_invalid_parameter_format`   | The format of a field value is invalid. For example if a MSISDN is not correctly formatted.                         |
| 400         | `syntax_constraint_violation`       | The request body doesn't fulfill all of the constraints set by the API. For example if a required field is missing. |
| 403         | `unknown_group`                     | A referenced group ID is unknown. This could happen if the ID is invalid or if the group has been deleted.          |
| 403         | `unknown_campaign`                  | The campaign ID does not match the specified originator.                                                            |
| 403         | `missing_callback_url`              | Callback has been requested but no URL is provided.                                                                 |
| 403         | `llegal_number_type`                | Illegal phone number type of MSISDN for a chosen region was used.                                                   |

## Send SMS Messages

### Send a batch message

The following operation will send a batch message.

Depending on the length of the *body* one message might be [split into multiple parts](#long-messages) and charged accordingly.

Any groups targeted in a scheduled batch will be evaluated at the time of sending. If a group is deleted between batch creation and scheduled date it will be considered empty.

#### Request

`POST /xms/v1/{service_plan_id}/batches`

JSON body fields:

|Name                               |Description                                                                                                        |JSON Type         |Default             |Constraints                                                                    |Required                                      |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------|:----------------:|--------------------|:-----------------------------------------------------------------------------:|:--------------------------------------------:|
|to                                 |List of MSISDNs and group IDs that will receive the batch                                                          |   String array   |N/A                 |                               1 to 1000 elements                              |                     Yes                      |
|from                               |Sender number                                                                                                      |      String      |N/A                 |               Must be valid MSISDN, short code or alphanumeric.               |If Automatic Default Originator not configured|
|type                               |Identifies the type of batch message                                                                               |      String      |mt_text             |                     Valid types are mt_text and mt_binary                     |                     Yes                      |
|body                               |The message content. Normal text string for mt_text and Base64 encoded for mt_binary                               |      String      |N/A                 | Max 2000 chars for mt_text and max 140 bytes together with udh for mt_binary  |                     Yes                      |
|udh                                |The UDH header of a binary message                                                                                 |HEX encoded string|N/A                 |                       Max 140 bytes together with body                        |             If type is mt_binary             |
|campaign_id                        |The campaign/service ID this message belongs to. US only.                                                          |      String      |N/A                 |                                     None                                      |                      No                      |
|delivery_report                    |Request delivery report callback. Note that delivery reports can be fetched from the API regardless of this setting|      String      |none                |             Valid types are none, summary, full and per_recipient             |                     Yes                      |
|send_at                            |If set in the future the message will be delayed until send_at occurs                                              | ISO-8601 string  |Now                 |Must be before expire_at. If set in the past messages will be sent immediately.|                      No                      |
|expire_at                          |If set the system will stop trying to deliver the message at this point                                            | ISO-8601 string  |3 days after send_at|                             Must be after send_at                             |                      No                      |
|callback_url                       |Override the default callback URL for this batch                                                                   |    URL string    |N/A                 |                  Must be valid URL. Max 2048 characters long                  |                      No                      |
|flash_message                      |Shows message on screen without user interaction while not saving the message to the inbox                         |     Boolean      |false               |                                 true or false                                 |                                              |
|parameters                         |Contains the parameters that will be used for customizing the message for each recipient                           |      Object      |N/A                 |                    Not applicable to if type is mt_binary                     |                      No                      |
|parameters.{parameter_key}         |The name of the parameter that will be replaced in the message body                                                |      String      |N/A                 |    Letters A-Z and a-z, digits 0-9 and .-_ allowed. Max 16 characters long    |                      No                      |
|parameters.{parameter_key}.{msisdn}|The recipient that should get this value                                                                           |      String      |N/A                 |                            Max 160 characters long                            |                      No                      |
|parameters.{parameter_key}.default |The fall back value for omitted recipient MSISDNs                                                                  |      String      |None                |                            Max 160 characters long                            |                      No                      |
|client_reference                   |The client identifier of batch message. If set, it will be added in the delivery report/callback of this batch     |      String      |N/A                 |        Max 128 characters long. No personally identifiable information        |                      No                      |
|max_number_of_message_parts        |Message will be dispatched only if it is not split to more parts than Max Number of Message Parts                  |      String      |N/A                 |                           Must be higher or equal 1                           |                      No                      |

**Send message to one recipient**
```shell
curl -X POST \
     -H "Authorization: Bearer {token}" \
     -H "Content-Type: application/json"  -d '
      {
          "from": "12345",
          "to": [
              "123456789"
          ],
          "body": "Hi there! How are you?"
      }' \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches"
```


**Send message to two recipients**
```shell
curl -X POST \
     -H "Authorization: Bearer {token}" \
     -H "Content-Type: application/json"  -d '
      {
          "from": "12345",
          "to": [
              "123456789",
              "987654321"
          ],
          "body": "Hi there! How are you?"
      }' \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches"
```


#### Response

`201 Created`

The batch was successfully created. The JSON response object contains the request parameters as well as the following additional parameters:

|Name       |Description                                     |JSON Type      |
|-----------|------------------------------------------------|:-------------:|
|id         |Unique identifier for batch                     |    String     |
|created_at |Timestamp for when batch was created.           |ISO-8601 String|
|modified_at|Timestamp for when batch was last updated.      |ISO-8601 String|
|canceled   |Indicates if the batch has been canceled or not.|    Boolean    |

`400 Bad Request`

There was an error with your request. The body is a JSON object described in rest\_http\_errors. Possible error codes include `syntax_invalid_json`, `syntax_invalid_parameter_format` and `syntax_constraint_violation`.

`403 Forbidden`

The system was not able to fulfill your request. The body is a JSON object described in rest\_http\_errors. Possible error codes include `unknown_group`, `unknown_campaign` and `missing_callback_url`.

Response for a successfully created batch message.

**JSON**
```json
{
    "from": "12345",
    "to": [
        "123456789",
        "987654321"
    ],
    "body": "Hi there! How are you?",
    "id": "{batch_id}",
    "created_at": "2014-10-02T09:34:28.542Z",
    "modified_at": "2014-10-02T09:34:28.542Z",
    "canceled": "False"
}
```


Send scheduled batch message with expiry.

**Scheduled batch message**
```shell
curl -X POST \
     -H "Authorization: Bearer {token}" \
     -H "Content-Type: application/json"  -d '
      {
          "from": "12345",
          "to": [
              "123456789",
              "987654321"
          ],
          "body": "Hi there! How are you?",
          "send_at": "2014-10-02T09:30Z",
          "expire_at": "2014-10-02T12:30Z"
      }' \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches"
```


**Batch message with custom delivery report URL**
```shell
curl -X POST \
     -H "Authorization: Bearer {token}" \
     -H "Content-Type: application/json"  -d '
      {
          "from": "12345",
          "to": [
              "123456789",
              "987654321"
          ],
          "body": "Hi there! How are you?",
          "delivery_report": "summary",
          "callback_url": "http://www.example.com"
      }' \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches"
```


**Parameterized message**
```shell
curl -X POST \
     -H "Authorization: Bearer {token}" \
     -H "Content-Type: application/json"  -d '
      {
      "from": "12345",
      "to": [
          "123456789",
          "987654321"
       ],
      "body": "Hi ${name}! How are you?",
      "parameters": {
          "name": {
              "123456789": "Joe",
              "default": "there"
           }
      }
  }' \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches"
```


### Cancel batch message

A batch can be canceled at any point. If a batch is canceled while it's currently being delivered some messages currently being processed might still be delivered. The delivery report will indicate which messages were canceled and which weren't.

Canceling a batch scheduled in the future will result in an empty delivery report while canceling an already sent batch would result in no change to the completed delivery report.

#### Request

`DELETE /xms/v1/{service_plan_id}/batches/{batch_id}`

#### Response

`200 OK`

The response is a JSON object described in send\_batch\_msg.

**Cancel a batch message**
```shell
curl-X DELETE \
   -H "Authorization: Bearer {token}" \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches/{batch_id}"
```


### List batch messages

With the list operation you can list batch messages created in the last 14 days that you have created. This operation supports pagination.

Batches are returned in reverse chronological order.

#### Request

`GET /xms/v1/{service_plan_id}/batches`

Query parameters:

|Name                |Description                                                                                                                 |Type                                                                   |Default|Constraints                  |Required|
|--------------------|----------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------:|:-----:|-----------------------------|:------:|
|page                |The page number starting from 0                                                                                             |                                Integer                                |   0   |0 or larger                  |   No   |
|page_size           |Determines the size of a page                                                                                               |                                Integer                                |  30   |Max 100                      |   No   |
|to                  |Only list messages set to this destination. Multiple destinations can be comma separated.                                   |                         MSISDN or short code                          |  No   |No                           |   No   |
|start_date          |Only list messages received at or after this date time.                                                                     |                               ISO-8601                                |Now-24 |Must be valid ISO-8601 string|   No   |
|end_date            |Only list messages received before this date time.                                                                          |                               ISO-8601                                |  No   |Must be valid ISO-8601 string|   No   |

#### Response

`200 OK`

|Name                |Description                                                                                                                 |JSON Type                                                              |
|--------------------|----------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------:|
|page                |The requested page                                                                                                          |                                Integer                                |
|page_size           |The number of batches returned in this request                                                                              |                                Integer                                |
|count               |The total number of batches matching the given filters                                                                      |                                Integer                                |
|batches             |The page of batches matching the given filters                                                                              |Array of objects described in [Send a batch message](#send-a-batch-mess|

### Dry run a batch

This operation will perform a dry run of a batch which calculates the bodies and number of parts for all messages in the batch without actually sending any messages.

#### Request

`POST /xms/v1/{service_plan_id}/batches/dry_run`

The request body is a JSON object described in send\_batch\_msg request.

Query parameters:

| Name                   | Description                                                                   | Type    | Default | Constraints | Required |
|-- -                    | ---                                                                           | ---     | ---     | ---         | ---    --|
| per\_recipient         | Whether to include per recipient details in the response                      | Boolean | false   |             | No       |
| number\_of\_recipients | Max number of recipients to include per recipient details for in the response | Integer | 100     | Max 1000    | No       |


#### Response

`200 OK`

The number of recipients in the batch, the number of messages in the batch and an array containing detail for each recipient:

|Name                |Description                                                                                                                 |JSON Type                                                              |
|--------------------|----------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------:|
|number_of_recipients|The number of recipents in the batch                                                                                        |                                integer                                |
|number_of_messages  |The total number of SMS message parts to be sent in the batch                                                               |                                integer                                |
|per_recipient       |The recipient, the number of message parts to this recipient, the body of the message, and the encoding type of each message|Array of message objects containing fields with the aforementioned data|

`400 Bad request`

There was an error with your request. The body is a JSON object described in rest\_http\_errors. Possible error codes include **syntax\_invalid\_json**, **syntax\_invalid\_parameter\_format** and **syntax\_constraint\_violation**.

**Dry run a batch**
```shell
curl -X POST \
     -H "Authorization: Bearer {token}" \
     -H "Content-Type: application/json"  -d '
      {
      "from": "12345",
      "to": [
          "123456789",
          "987654321"
       ],
      "body": "Hi ${name}! How are you?",
      "parameters": {
          "name": {
              "123456789": "Joe",
              "default": "there"
           }
      }
  }' \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches/dry_run"
```


### Retrieve a batch message

This operation retrieves a specific batch with the provided batch ID.

#### Request

`GET /xms/v1/{service_plan_id}/batches/{batch_id}`

#### Response

`200 OK`

The response is a JSON object described in send\_batch\_msg response.

`404 Not Found`

If the batch ID is unknown to the system.



**Retrieve a batch**
```shell
curl -H "Authorization: Bearer {token}" \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches/{batch_id}"
```


**Retrieve the first 30 batches from the last 24 hours**
```shell
curl -H "Authorization: Bearer {token}" \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches"
```


**Retrieve the third page of batches with a page size of 50 from the last 24 hours**
```shell
curl -H "Authorization: Bearer {token}" \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches?page=2&page_size=50"
```


**Retrieve batches created on June 23rd, 2014 UTC**
```shell
curl -H "Authorization: Bearer {token}" \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches?start_date=2014-06-23&end_date=2014-06-24"
```


**Retrieve the batches sent from 12345 or 54321**
```shell
curl -H "Authorization: Bearer {token}" \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches?from=12345,54321"
```


### Update a batch message

This operation will update all provided parameters of a batch for the given batch ID.

#### Request

`POST /xms/v1/{service_plan_id}/batches/{id}`

JSON body fields:

|Name                |Description                                                                                                                 |Type                                                                   |Default             |Constraints                                                                    |Required|
|--------------------|----------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------:|:------------------:|-------------------------------------------------------------------------------|:------:|
|toAdd               |List of MSISDNs and group IDs to add to the batch.                                                                          |                             String array                              |                    |1 to 100 elements.                                                             |   No   |
|toRemove            |List of MSISDNs and group IDs to remove from the batch.                                                                     |                             String array                              |                    |1 to 100 elements.                                                             |   No   |
|from                |Sender number                                                                                                               |                                String                                 |                    |Must be valid MSISDN, short code or alphanumeric.                              |   No   |
|body                |The message content. Normal text string for mt_text and Base64 encoded for mt_binary.                                       |                                String                                 |                    |Max 2000 chars for mt_text and max 140 bytes together with udh for mt_binary   |   No   |
|campaign_id         |The campaign/service ID this message belongs to. US only.                                                                   |                                String                                 |                    |                                                                               |   No   |
|delivery_report     |Request delivery report callback. Note that delivery reports can be fetched from the API regardless of this setting.        |                                String                                 |        none        |Valid types are none, summary, full and per_recipient                          |   No   |
|send_at             |If set in the future the message will be delayed until send_at occurs.                                                      |                            ISO-8601 string                            |        Now         |Must be before expire_at. If set in the past messages will be sent immediately.|   No   |
|expire_at           |If set the system will stop trying to deliver the message at this point                                                     |                            ISO-8601 string                            |3 days after send_at|Must be after send_at                                                          |   No   |
|callback_url        |Override the default callback URL for this batch                                                                            |                              URL string                               |                    |Must be valid URL. Max 2048 characters long.                                   |   No   |

#### Response

`200 OK`

The response is a JSON object described in send\_batch\_msg response.

`400 Bad request`

There was an error with your request. The body is a JSON object described in rest\_http\_errors. Possible error codes include **syntax\_invalid\_json**, **syntax\_invalid\_parameter\_format** and **syntax\_constraint\_violation**.

`403 Forbidden`

+The system was not able to fulfill your request. The body is a JSON object described in rest\_http\_errors. +Possible error codes include **batch\_already\_dispatched**.

**Update batch**
```shell
curl -X POST \
     -H "Authorization: Bearer {token}" \
     -H "Content-Type: application/json"  -d '
      {
      "from": "12345",
      "toAdd": [
          "123456789",
          "987654321"
       ],
      "toRemove": [
          "111111111",
          "999999999"
       ],
      "body": "Hi ${name}! How are you?"

  }' \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches/{id}"
```


### Replace a batch

This operation will replace all the parameters of a batch with the provided values. Effectively the same as cancelling then batch and sending a new one instead.

#### Request

`PUT /xms/v1/{service_plan_id}/batches/{id}`

The request body is a JSON object described in send\_batch\_msg request.

#### Response

`200 OK`

The response is a JSON object described in send\_batch\_msg response.

`400 Bad request`

There was an error with your request. The body is a JSON object described in rest\_http\_errors. Possible error codes include **syntax\_invalid\_json**, **syntax\_invalid\_parameter\_format** and **syntax\_constraint\_violation**.

**Replace batch**
```shell
curl -X PUT \
     -H "Authorization: Bearer {token}" \
     -H "Content-Type: application/json"  -d '
      {
      "from": "12345",
      "to": [
          "123456789",
          "987654321"
       ],
      "body": "Hi ${name}! How are you?",
      "parameters": {
          "name": {
              "123456789": "Joe",
              "default": "there"
           }
      }
  }' \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches/{id}"
```


### Retrieve a delivery report

Delivery reports can be retrieved even if no callback was requested. The difference between a summary and a full report is only that the full report contains the actual MSISDNs for each status code.

#### Request

`GET /xms/v1/{service_plan_id}/batches/{batch_id}/delivery_report`

Query parameters:

| Name   | Description                                                        | Type   | Default | Constraints             | Required |
|-- -    | ---                                                                | ---    | ---     | ---                     | ---    --|
| type   | The type of delivery report                                        | String | summary | Must be summary or full | Yes      |
| status | Comma separated list of delivery\_report\_statuses to include      | String | N/A     | N/A                     | No       |
| code   | Comma separated list of delivery\_receipt\_error\_codes to include | String | N/A     | N/A                     | No       |

#### Response

`200 OK`

The response is a JSON object with the following fields:

|Name                |Description                                                                                                                 |JSON Type                                                              |
|--------------------|----------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------:|
|type                |The object type. Will always be delivery_report_sms                                                                         |                                String                                 |
|batch_id            |The ID of the batch this delivery report belongs to                                                                         |                                String                                 |
|total_message_count |The total number of messages for the batch                                                                                  |                                String                                 |
|statuses            |Array with status objects. Only status codes with at least one recipient will be listed.                                    |                                Object                                 |
|statuses.code       |The detailed status code. See Error Codes                                                                                   |                                Integer                                |
|statuses.status     |The simplified status as described in Delivery Report Statuses                                                              |                                String                                 |
|statuses.count      |The number of messages that currently has this code. Will always be at least 1                                              |                                Integer                                |
|statuses.recipients |Only for full report. A list of the MSISDN recipients which messages has this status code.                                  |                             String array                              |
|client_reference    |The client identifier of the batch this delivery report belongs to, if set when submitting batch.                           |                                String                                 |

`404 Not Found`

The batch ID is not known to the system or the delivery report type is not recognized.

**Request summary report**
```shell
curl -H "Authorization: Bearer {token}" \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches/{batch_id}/delivery_report"
```


**Summary report response**
```json
{
    "type": "delivery_report_sms",
    "batch_id": "{batch_id}",
    "total_message_count": 3,
    "statuses": [
        {
            "code": 400,
            "status": "Queued",
            "count": 1
        },
        {
            "code": 0,
            "status": "Delivered",
            "count": 2
        }
    ]
}
```




**Request full report**
```shell
curl -H "Authorization: Bearer {token}" \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches/{batch_id}/delivery_report?type=full"
```


**Full report response**
```json
{
    "type": "delivery_report_sms",
    "batch_id": "{batch_id}",
    "total_message_count": 3,
    "statuses": [
        {
            "code": 400,
            "status": "Queued",
            "count": 1,
            "recipients": [
                "123456789"
            ]
        },
        {
            "code": 0,
            "status": "Delivered",
            "count": 2,
            "recipients": [
                "987654321",
                "123459876"
            ]
        }
    ]
}
```


### Retrieve a recipient delivery report

A recipient delivery report contains the message status for a single recipient MSISDN.

#### Request

`GET /xms/v1/{service_plan_id}/batches/{batch_id}/delivery_report/{recipient_msisdn}`

**Request report for 123456789**
```shell
curl -H "Authorization: Bearer {token}" \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches/{batch_id}/delivery_report/123456789"
```


#### Response

**Recipient delivery report for 123456789**
```json
{
    "type": "recipient_delivery_report_sms",
    "batch_id": "{batch_id}",
    "recipient": "123456789",
    "code": "0",
    "status": "Delivered",
    "at": "2016-10-02T09:34:18.542Z",
    "operator_status_at": "2016-10-02T09:34:18.101Z"
}
```


`200 OK`

The response is a JSON object with the following fields:

|Name                |Description                                                                                                                 |JSON Type                                                              |
|--------------------|----------------------------------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------:|
|type                |The object type. Will always be _recipient_delivery_report_sms                                                              |                                String                                 |
|batch_id            |The ID of the batch this delivery report belongs to                                                                         |                                String                                 |
|recipient           |The recipient MSISDN                                                                                                        |                                String                                 |
|code                |The detailed status code. See Error Codes                                                                                   |                                Integer                                |
|status              |The simplified status as described in Delivery Report Statuses                                                              |                                String                                 |
|status_message      |A description of the status, if available.                                                                                  |                                String                                 |
|at                  |A timestamp of when the Delivery Report was created in the Sinch service                                                    |                            ISO-8601 String                            |
|operator_status_at  |A timestamp extracted from the Delivery Receipt from the originating SMSC                                                   |                            ISO-8601 String                            |
|client_reference    |The client identifier of the batch this delivery report belongs to, if set when submitting batch.                           |                                String                                 |
|applied_originator  |The default originator used for the recipient this delivery report belongs to, if default originator pool configured and no originator set when submitting batch.|                                String                                 |
|number_of_message_parts|The number of parts the message was split into. Present only if `max_number_of_message_parts` parameter was set.              |                                Integer                                |

`404 Not Found`

The batch ID is not known to the system or the recipient is not a target of this batch.

### Receiving delivery report callbacks

Delivery report callbacks will be received for batches where *delivery\_report* parameter is set to *summary*, *full* or *per\_recipient*.

If no *callback\_url* was specified for the batch then the default callback URL for the given service plan will be used.

#### Summary and full

If a batch was created with a request for *full* or *summary* *delivery report* then one callback will be made to the specified callback URL when all messages in the batch (which may be one message) are either delivered, failed or expired. If you want to know the delivery status for a message before all messages are completed then you can always use retrieve\_delivery\_report at any time.

The format for summary and full reports is the same as the retrieve\_delivery\_report response. The difference being that when *full* is requested a list of phone numbers / recipients per delivery status is provided.

#### Per recipient

If a batch was created with a request for *per\_recipient* *delivery\_report* then a callback will be made for each status change of a message. This could result in a lot of callbacks and should be **used with caution for larger batches**. These delivery reports also include a timestamp of when the Delivery Report originated from the SMSC.

### Callbacks

A callback is a HTTP POST request with a notification made by the Sinch SMS REST API to a URI of your choosing. The REST API expects the receiving server to respond with a response code within the `2xx` Success range. If no successful response is received then the REST API will either schedule a retry if the error is expected to be temporary or discard the callback if the error seems permanent. The first initial retry will happen 5 seconds after the first try. The next attempt is after 10 seconds, then after 20 seconds, after 40 seconds, after 80 seconds and so on, doubling on every attempt. The last retry will be at 81920 seconds (or 22 hours 45 minutes) after the initial failed attempt.

The REST API offers the following callback options which can be configured for your account upon request to your account manager.

 * Callback with mutual authentication over TLS (HTTPS) connection by provisioning the callback URL with client keystore and password.
 * Callback with basic authentication by provisioning the callback URL with username and password.
 * Callback with OAuth 2.0 by provisioning the callback URL with username, password and the URL to fetch OAuth access token.
 * Callback using AWS SNS by provisioning the callback URL with an Access Key ID, Secret Key and Region.

#### Delivery report callback

A delivery report contains the status and status code for each recipient of a batch. To get a delivery report callback for a message or batch of messages you need to set the `delivery_report` field accordingly when [creating a batch](#send-a-batch-message). The formats of the different types of delivery reports are described in [Retrieve a delivery report](#retrieve-a-delivery-report) and in [Retrieve a recipient delivery report](#retrieve-a-recipient-delivery-report).

The callback URL can either be provided for each batch in the [Send a batch message](#send-a-batch-message) operation or provisioned globally for your account.

#### Inbound message callback

An inbound message or MO (*Mobile Originated*) is a message sent to one of your shortcodes or long numbers from a mobile phone. The format of an inbound callback is described in [Inbounds Endpoint](#inbounds-endpoint).

To receive inbound message callbacks, a URL needs to be provisioned for your account. This URL can be specified in the Sinch Dashboard.

### Message Body

When specifying the message body in the request, the characters used as well as the length of the message affect how many actual SMS messages are sent out. When using [parameterization](#parameterization), the length of each message may also vary depending on the recipient-specific data.

#### Supported Characters

Individual characters used in the message determine the type of encoding that will ultimately be used to send the SMS message. The API automatically detects the encoding required from the characters used, which allows us to support the delivery of SMS in any language.

##### Basic Character Set

You can send up to 160 characters in a single SMS message if all characters in your message are part of the GSM 7-bit character set:

|      |       |      |     |     |     |     |       |
|-- -  | ---   | ---  | --- | --- | --- | --- | --- --|
| @    | Δ     | `SP` | 0   | ¡   | P   | ¿   | p     |
| £    | _     | !    | 1   | A   | Q   | a   | q     |
| $    | Φ     | "    | 2   | B   | R   | b   | r     |
| ¥    | Γ     | #    | 3   | C   | S   | c   | s     |
| è    | Λ     | ¤    | 4   | D   | T   | d   | t     |
| é    | Ω     | %    | 5   | E   | U   | e   | u     |
| ù    | Π     | &    | 6   | F   | V   | f   | v     |
| ì    | Ψ     | '    | 7   | G   | W   | g   | w     |
| ò    | Σ     | (    | 8   | H   | X   | h   | x     |
| Ç    | Θ     | )    | 9   | I   | Y   | i   | y     |
| `LF` | Ξ     | *    | :   | J   | Z   | j   | z     |
| Ø    | `ESC` | +    | ;   | K   | Ä   | k   | ä     |
| ø    | Æ     | ,    | <   | L   | Ö   | l   | ö     |
| `CR` | æ     | -    | =   | M   | Ñ   | m   | ñ     |
| Å    | ß     | .    | >   | N   | Ü   | n   | ü     |
| å    | É     | /    | ?   | O   | §   | o   | à     |

`LF` is the Line Feed character - for JSON format, provide it as `\n`
`SP` is the Space character

##### Extended Character Set

The following characters are also available, but they are counted as two characters in the SMS message rather than one:

`|` , `^` , `&euro;` , `{` , `}` , `[` , `]` , `~` , `\`

##### Other Characters

If other characters are required for different languages, 16-bit Unicode (UCS-2) encoding will be used. When using UCS-2 encoding, each character will take 2 bytes, which means up to 70 characters can be sent per UCS-2 encoded SMS message.

### Long Messages

The message body in a request can contain up to 2000 characters. Longer messages will be split and sent as multiple distinct SMS messages. In most cases, those messages will be re-assembled on the handset and displayed to the end-user as a single long message. You can use the tables below to determine the actual number of SMS messages your message will use depending on its length and encoding.

#### Using only 7-bit Characters

| Message Length | Number of SMS Parts |
|-- -            | ---               --|
| 1 - 160        | 1                   |
| 161 - 306      | 2                   |
| 307 - 459      | 3                   |
| 460 - 612      | 4                   |
| 613 - 765      | 5                   |
| 766 - 918      | 6                   |
| 919 - 1061     | 7                   |
| 1062 - 1214    | 8                   |
| 1215 - 1367    | 9                   |
| 1368 - 1520    | 10                  |
| 1521 - 1673    | 11                  |
| 1674 - 1826    | 12                  |
| 1827 - 1979    | 13                  |
| 1980 - 2000    | 14                  |

Each SMS in a multi-part 7-bit encoded message, has a maximum length of 153 characters.

#### Using Unicode Characters

| Message Length | Number of SMS Parts |
|-- -            | ---               --|
| 1 - 70         | 1                   |
| 71 - 134       | 2                   |
| 134 - 201      | 3                   |
| 202 - 268      | 4                   |
| 269 - 335      | 5                   |
| 336 - 402      | 6                   |
| 403 - 469      | 7                   |
| 470 - 538      | 8                   |
| 539 - 605      | 9                   |
| 606 - 672      | 10                  |
| 673 - 739      | 11                  |
| 740 - 796      | 12                  |
| 797 - 853      | 13                  |
| 854 - 924      | 14                  |
| 925 - 991      | 15                  |
| 992 - 1058     | 16                  |
| 1059 - 1115    | 17                  |
| 1116 - 1182    | 18                  |
| 1183 - 1249    | 19                  |
| 1250 - 1316    | 20                  |
| 1317 - 1383    | 21                  |
| 1384 - 1450    | 22                  |
| 1451 - 1517    | 23                  |
| 1518 - 1584    | 24                  |
| 1585 - 1651    | 25                  |
| 1652 - 1781    | 26                  |
| 1782 - 1848    | 27                  |
| 1849 - 1915    | 28                  |
| 1916 - 1982    | 29                  |
| 1983 - 2000    | 30                  |

Each SMS in a multi-part Unicode encoded message, has a maximum length of 67
characters.

## Parameterization

Parameterization enables you to customize parts of a message for each recipient.

This is done by defining a *parameter key* and placing it in the message body. For each *parameter key*, a recipient and parameter value must be provided. The position of a parameter in a message is defined by specifying placeholders in the format `${parameter_key}` in the message body, where `parameter_key` is the name of the parameter to replace with its corresponding value.

For example the message body `Hi ${name}! How are you?` contains the parameter `name` and will be replaced according to the rules specified in the `parameters` field in the [Send a batch message](#send-sms-messages) operation.

A default parameter value can be specified that will be used when an MSISDN is not listed for a particular parameter. To set this, identify a recipient as default for each *parameter key*.

If a target MSISDN is missing in the parameters object and no default value has been defined for that parameter the message will fail for that MSISDN but not for other recipients.

Parameter keys are case sensitive.

## Delivery Reports

The REST API uses message statuses and error codes in delivery reports, which refer to the state of the SMS batch and can be present in either [Retrieve a delivery report](#retrieve-a-delivery-report) or sent to a [callback](#callbacks).

This section describes the statuses and codes returned in those delivery reports.

### Delivery Report Statuses

The status field describes which state a particular message is in. Please note that statuses of type Intermediate will only be reported if you request a status per recipient ([Retrieve a recipient delivery report](#retrieve-a-recipient-delivery-report)), no callback will be made to report them. The following statuses are available when using the REST API:

| Status     | Type         | Description                                                                                                                                   |
|-- -        | ---          | ---                                                                                                                                         --|
| Queued     | Intermediate | Message is queued within REST API system and will be dispatched according to the rate of the account.                                         |
| Dispatched | Intermediate | Message has been dispatched and accepted for delivery by the SMSC.                                                                            |
| Aborted    | Final        | Message was aborted before reaching the SMSC.                                                                                                 |
| Rejected   | Final        | Message was rejected by the SMSC.                                                                                                             |
| Delivered  | Final        | Message has been delivered.                                                                                                                   |
| Failed     | Final        | Message failed to be delivered.                                                                                                               |
| Expired    | Final        | Message expired before delivery to the SMSC. This may happen if the expiry time for the message was very short.                               |
| Unknown    | Final        | Message was delivered to the SMSC but no Delivery Receipt has been received or a Delivery Receipt that could not be interpreted was received. |

### Delivery Report Error Codes

The delivery report status code provides a more detailed view of what happened with a message. The REST API shares most of its error codes with other SMS services.

These are defined [here](doc:sms-other-cloud-smpp#error-codes).

In addition to these standard error codes, the REST API provides an additional set of error codes, all within the 4xx range (vendor specific errors in the range of 0x400 to 0x4FF as referenced in the SMPP specification). These are listed below:

| Status Code (Hex) | Name                         | Status     | Description                                                                                                                         |
| ----------------- | ---------------------------- | ---------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| 400               | Queued                       | Queued     | Message is queued within REST API system and will be dispatched according to the rate of the account.                               |
| 401               | Dispatched                   | Dispatched | Message has been dispatched to SMSC.                                                                                                |
| 402               | Message unroutable           | Aborted    | SMSC rejected message. Retrying is likely to cause the same error.                                                                  |
| 403               | Internal error               | Aborted    | An unexpected error caused the message to fail.                                                                                     |
| 404               | Temporary delivery failure   | Aborted    | Message failed because of temporary delivery failure. Message can be retried.                                                       |
| 405               | Unmatched Parameter          | Aborted    | One or more parameters in the message body has no mapping for this recipient. See [Parameterization](#parameterization) |
| 406               | Internal Expiry              | Aborted    | Message was expired before reaching SMSC. This may happen if the expiry time for the message was very short.                        |
| 407               | Canceled                     | Aborted    | Message was canceled by user before reaching SMSC.                                                                                  |
| 408               | Internal Reject              | Aborted    | SMSC rejected the message. Retrying is likely to cause the same error.                                                              |
| 410               | Unmatched default originator | Aborted    | No default originator exists/configured for this recipient when sending message without originator.                                 |
| 411               | Exceeded parts limit         | Aborted    | Message failed as the number of message parts exceeds the defined max number of message parts.                                      |
| 412               | Unprovisioned region         | Aborted    | SMSC rejected the message. The account has not been provisioned for this region.                                                    |

> **Note**    
>
> New error codes may be added over time.


## Inbounds Endpoint

Inbounds, or Mobile Originated messages, are incoming messages. Inbound messages can be listed and retrieved like batch messages and they can also be delivered by callback requests like delivery reports.

| Name         | Description                                                                           | JSON Type       |
| ------------ | ------------------------------------------------------------------------------------- | --------------- |
| type         | The object type. Either *mo\_text* or *mo\_binary*.                                   | String          |
| id           | The ID of this inbound message.                                                       | String          |
| from         | The originator MSISDN.                                                                | String          |
| to           | The destination MSISDN or short code.                                                 | String          |
| body         | Message body, Base64 encoded if type is *mo\_binary*.                                 | String          |
| operator\_id | The MCCMNC of the sender's operator if known.                                         | String          |
| sent\_at     | When the message left the originating device. Only available if provided by operator. | ISO-8601 String |
| received\_at | When the system received the message.                                                 | ISO-8601 String |

NB: The operator is only available for MOs sent to short codes.

### List inbound messages

**Retrieve the first 30 inbound messages from the last 24 hours.**
```shell
curl -H "Authorization: Bearer {token}" \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/inbounds"
```




**Retrieve the third page of inbound messages with a page size of 50 from the last 24 hours**
```shell
curl -H "Authorization: Bearer {token}" \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/inbounds?page=2&page_size=50"
```




**Retrieve inbound messages received on June 23rd, 2014 UTC**
```shell
curl -H "Authorization: Bearer {token}" \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/inbounds?start_date=20140623TZ&end_date=20140624TZ"
```




**Retrieve the batches sent to 12345 or 54321**
```shell
curl -H "Authorization: Bearer {token}" \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/inbounds?to=12345,54321"
```


With the list operation you can list all inbound messages that you have received. This operation supports pagination.

Inbounds are returned in reverse chronological order.

#### Request

**GET /xms/v1/{service\_plan\_id}/inbounds**

Query parameters:

| Name        | Description                                                                               | Type                 | Default | Constraints                   | Required |
| ----------- | ----------------------------------------------------------------------------------------- | -------------------- | ------- | ----------------------------- | -------- |
| page        | The page number starting from 0                                                           | Integer              | 0       | 0 or larger                   | No       |
| page\_size  | Determines the size of a page                                                             | Integer              | 30      | Max 100                       | No       |
| to          | Only list messages set to this destination. Multiple destinations can be comma separated. | MSISDN or short code | No      | No                            | No       |
| start\_date | Only list messages received at or after this date time.                                   | ISO-8601             | Now-24  | Must be valid ISO-8601 string | No       |
| end\_date   | Only list messages received before this date time.                                        | ISO-8601             | No      | Must be valid ISO-8601 string | No       |

#### Response

**200 OK**

| Name       | Description                                             | JSON Type                                                                         |
| ---------- | ------------------------------------------------------- | ------------------------------------------------------------------------          |
| page       | The requested page                                      | Integer                                                                           |
| page\_size | The number of inbounds returned in this request         | Integer                                                                           |
| count      | The total number of inbounds matching the given filters | Integer                                                                           |
| inbounds   | The page of inbounds matching the given filters         | Array of objects described in [Inbounds endpoint](#inbounds-endpoint) |

### Retrieve inbound message

Retrieve an inbound message

This operation retrieves a specific inbound message with the provided inbound ID.

#### Request

**GET /xms/v1/{service\_plan\_id}/inbounds/{inbound\_id}**

#### Response

**200 OK**

The response is a JSON object described in `inbounds_endpoint` response.

**404 Not Found**

**If the inbound ID is unknown to the system**
```shell
curl -H "Authorization: Bearer {token}" \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/inbounds/{inbound_id}"
```

## Groups endpoint

A group is a set of MSISDNs that can be used as a target in the `send_batch_msg` operation. An MSISDN can only occur once in a group and any attempts to add a duplicate would be ignored but not rejected.

### Create a group

#### Request

`POST /xms/v1/{service_plan_id}/groups`

JSON object parameters:

| Name                                                                         | Description                                                                                                                                                                                                                                                          | JSON Type            | Default     | Constraints                                                             | Required   |
|-- -------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------: | :---------: | ----------------------------------------------------------------------- | :------: --|
| name                                                                         | Name of the group.                                                                                                                                                                                                                                                   | String               | N/A         | Max 20 characters                                                       | No         |
| members                                                                      | Initial list of MSISDNs for the group.                                                                                                                                                                                                                               | String array         | N/A         | Elements must be MSISDNs. Max 10,000 elements                           | No         |
| child_groups auto_update                                                     | MSISDNs of child group will be included in this group. If present then this group will be auto populated.                                                                                                                                                            | String array Object  | N/A N/A     | Elements must be group IDs. Max 10 elements                             | No No      |
| auto_update.to auto_update.add auto_update.add.first_word                    | Short code or long number addressed in MO. Keyword to be sent in MO to add MSISDN to a group Opt-in keyword like "JOIN" If auto_update.to is dedicated long/short number or unique brand keyword like "Sinch" if it is a shared short code.                          | String Object String | N/A N/A N/A | Must be valid MSISDN or short code Must be one word. Max 15 characters  | No No No   |
| auto_update.add.second_word auto_update.remove auto_update.remove.first_word | Opt-in keyword like "JOIN" if auto_update.to is shared short code. Keyword to be sent in MO to remove from a group. Opt-out keyword like "LEAVE" If auto_update.to is dedicated long/short number or unique brand keyword like "Sinch" if it is a shared short code. | String Object String | N/A N/A N/A | Must be one word. Max 15 characters Must be one word. Max 15 characters | No No No   |
| auto_update.remove.second_word                                               | Opt-out keyword like "LEAVE" if auto_update.to is shared short code.                                                                                                                                                                                                 | String               | N/A         | Must be one word. Max 15 characters                                     | No         |

#### Response

`201 Created`

The response body is a JSON object with the following
parameters:

| Name         | Description                                     | JSON Type       |
| ------------ | ----------------------------------------------- | --------------- |
| id           | The ID used to reference this group             | String          |
| name         | Name of group if set                            | String          |
| size         | The number of members currently in the group    | Integer         |
| created\_at  | Timestamp for when the group was created.       | ISO-8601 String |
| modified\_at | Timestamp for when the group was last modified. | ISO-8601 String |

`400 Bad Request`

There was an error with your request. The body is a JSON object described in `rest_http_errors`. Possible error codes include **syntax\_invalid\_json**, **syntax\_invalid\_parameter\_format** and **syntax\_constraint\_violation**.

`403 Forbidden`

The system was not able to fulfill your request. The body is a JSON object described in `rest_http_errors`. Possible error codes include **conflict\_group\_name**.

Create a group named *My group* with two members.

**Create Group**
```shell
curl -X POST \
     -H "Authorization: Bearer {token}" \
     -H "Content-Type: application/json"  -d '
      {
          "members": [
              "123456789",
              "987654321"
          ],
          "name": "My group"
}' \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/groups"
```


Create auto update group for members sending MOs to \`443456789012\`: keyword *JOIN* will add them to the group, keyword *STOP* will remove them from the group.

**Create Auto Update Group**
```shell
curl -X POST \
     -H "Authorization: Bearer {token}" \
     -H "Content-Type: application/json"  -d '
      {
          "name": "My auto group",
          "auto_update": {
                "to": "443456789012",
                    "add": {
                        "first_word": "join"
                    },
                    "remove": {
                        "first_word": "stop"
                    }
                }
}' \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/groups"
```


Create auto update group for shared short code *54321* with keyword `SINCH`: if followed by *JOIN* will add them to the group, if followed by *LEAVE* will remove them from the group. Please note that keywords are not case sensitive so *Sinch* , *SInCh* , *SINCH* and other permutations will all be treated the same.

**Create Auto Update Group For Shared Short Code**
```shell
curl -X POST \
     -H "Authorization: Bearer {token}" \
     -H "Content-Type: application/json"  -d '
      {
          "name": "My auto group 2",
          "auto_update": {
                "to": "54321",
                    "add": {
                        "first_word": "Sinch",
                        "second_word": "Join"
                    },
                    "remove": {
                        "first_word": "SINCH",
                        "second_word": "leave"
                    }
                }
}' \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/groups"
```


Create a parent group that includes all members of groups with ID *dxCJTlfb1UsF* and *yiinTKVNAEAu*.

**Create Parent Group**
```shell
curl -X POST \
     -H "Authorization: Bearer {token}" \
     -H "Content-Type: application/json"  -d '
      {
          "name": "My groups collection",
          "child_groups": [
                "dxCJTlfb1UsF",
                "yiinTKVNAEAu"
          ]
}' \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/groups"
```


### List groups

With the list operation you can list all groups that you have created.  This operation supports pagination.

Groups are returned in reverse chronological order.

#### Request

`GET /xms/v1/{service_plan_id}/groups`

Query parameters:

| Name       | Description                     | Type    | Default | Constraints | Required |
| ---------- | ------------------------------- | ------- | ------- | ----------- | -------- |
| page       | The page number starting from 0 | Integer | 0       | 0 or larger | No       |
| page\_size | Determines the size of a page   | Integer | 30      | Max 100     | No       |

#### Response

`200 OK`

| Name       | Description                                   | JSON Type                                                                 |
| ---------- | --------------------------------------------- | ------------------------------------------------------------------        |
| page       | The requested page                            | Integer                                                                   |
| page\_size | The number of groups returned in this request | Integer                                                                   |
| count      | The total number of groups                    | Integer                                                                   |
| groups     | The page of groups matching                   | Array of objects described in [Create a group](#create-a-group) |

**Retrieve the first 30 groups**
```shell
curl -H "Authorization: Bearer {token}" \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/groups"
```




**Retrieve the third page of groups with a page size of 50**
```shell
curl -H "Authorization: Bearer {token}" \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/groups?page=3&page_size=50"
```


### Retrieve a group

This operation retrieves a specific group with the provided group ID.

#### Request

`GET /xms/v1/{service_plan_id}/groups/{group_id}`

#### Response

`200 OK`

The response is a JSON object described in `create_group` response.

`404 Not Found`

If the group ID is unknown to the system.

**Retrieve a group**
```shell
curl -H "Authorization: Bearer {token}" \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/groups/{group_id}"
```


### Retrieve the members of a group

This operation retrieves the members of the group with the provided group ID.

#### Request

`GET /xms/v1/{service_plan_id}/groups/{group_id}/members`

#### Response

`200 OK`

The response is a JSON array with MSISDNs.

`404 Not Found`

If the group ID is unknown to the system.

**Retrieve group members**
```shell
curl -H "Authorization: Bearer {token}" \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/groups/{group_id}/members"
```


### Update a group

With the update group operation you can add and remove members to an existing group as well as rename the group.

The request will **not** be rejected for duplicate adds or unknown removes.

The adds will be done before the deletes so if an MSISDN is in both lists then it will not be part of the resulting group.

To remove an existing name set name explicitly to the JSON value **null**. Omitting **name** from the JSON body will leave the name unchanged.

Updating a group targeted by a batch message scheduled in the future is allowed and changes will be reflected until the batch is sent.

#### Request

`POST /xms/v1/{service_plan_id}/groups/{group_id}`

The request is a JSON object with the following fields:

| Name                | Description                                            | JSON Type    | Default | Constraints                                    | Required |
| ------------------- | ------------------------------------------------------ | ------------ | ------- | ---------------------------------------------- | -------- |
| add                 | MSISDNs to add as members.                             | String Array | N/A     | Elements must be MSISDNs. Max 10 000 elements. | No       |
| remove              | MSISDNs to remove from group.                          | String Array | N/A     | Elements must be MSISDNs. Max 10 000 elements. | No       |
| name                | Name of group.                                         | String       | N/A     | Max 20 characters.                             | No       |
| add\_from\_group    | Copy the members from the given group into this group. | String       | N/A     | Must be valid group ID                         | No       |
| remove\_from\_group | Remove members in the given group from group.          | String       | N/A     | Must be valid group ID                         | No       |

#### Response

`200 OK`

The response is a JSON object described in `create_group` response.

`400 Bad Request`

There was an error with your request. The body is a JSON object described in `rest_http_errors`. Possible error codes include **syntax\_invalid\_json**, **syntax\_invalid\_parameter\_format** and **syntax\_constraint\_violation**.

`403 Forbidden`

One or more groups referenced in your request could not be found.

`404 Not Found`

If the group ID is unknown to the system.

**Update a group by adding two new members and removing one existing**
```shell
curl -X POST \
     -H "Authorization: Bearer {token}" \
     -H "Content-Type: application/json"  -d '
      {
          "add": [
              "123456789",
              "987654321"
          ],
          "remove": [
              "432156789"
          ]
}' \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/groups/{group_id}"
```




**Rename a group without changing its members**
```shell
curl -X POST \
     -H "Authorization: Bearer {token}" \
     -H "Content-Type: application/json"  -d '
      {
          "name": "New group name"
}' \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/groups/{group_id}"
```




**Remove the current name of a group without changing its members**
```shell
curl -X POST \
     -H "Authorization: Bearer {token}" \
     -H "Content-Type: application/json"  -d '
      {
          "name": null
}' \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/groups/{group_id}"
```


### Replace a group

The replace operation will replace all parameters, including members, of an existing group with new values.

Replacing a group targeted by a batch message scheduled in the future is allowed and changes will be reflected when the batch is sent.

#### Request

`PUT /xms/v1/{service_plan_id}/groups/{group_id}`

JSON object
parameters:

| Name    | Description                      | JSON Type    | Default | Constraints                                    | Required |
| ------- | -------------------------------- | ------------ | ------- | ---------------------------------------------- | -------- |
| name    | Name of group.                   | String       | N/A     | Max 20 characters.                             | No       |
| members | The initial members of the group | String array | N/A     | Elements must be MSISDNs. Max 10 000 elements. | Yes      |

#### Response

`200 OK`

The response is a JSON object described in `create_group` response.

`400 Bad Request`

There was an error with your request. The body is a JSON object described in `rest_http_errors`. Possible error codes include **syntax\_invalid\_json**, **syntax\_invalid\_parameter\_format** and **syntax\_constraint\_violation**.

`403 Forbidden`

The system was not able to fulfill your request. The body is a JSON object described in `rest_http_errors`. Possible error codes include **conflict\_group\_name**.

`404 Not Found`

If the group ID is unknown to the system.

**Replace a group**
```shell
curl -X PUT \
     -H "Authorization: Bearer {token}" \
     -H "Content-Type: application/json"  -d '
      {
          "members": [
              "123456789",
              "987654321"
          ],
      "name": "New name"
}' \
  "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/groups/{group_id}"
```


### Delete a group

Deletes a group from the system. Any message created for a deleted group will be rejected. If a group is deleted that's part of the to-array of a message scheduled for the future then the group will be considered empty at the time of sending.

#### Request

`DELETE /xms/v1/{service_plan_id}/groups/{group_id}`

#### Response

`200 OK`

The group was successfully deleted.

`404 Not Found`

If the group ID is unknown to the system.

**Delete a group**
```shell
curl -X DELETE \
     -H "Authorization: Bearer {token}" \
     "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/groups/{group_id}"
```

## Automatic Default Originator

Default originator pool of an account is the set of originators for different countries configured by the account manager, if requested it will be used when batch message has no originator specified (missing "from" parameter).

For each MSISDN the originator will be auto selected from its default originator pool based on the country/region of the MSISDN when submitting a batch with missing originator as in `send_batch_msg` operation, if a default originator pool configured for your account. A batch with multiple recipients of different countries can get different originators based on the configured originator pool of that account.

If no default originator exists for the target MSISDN, then the message will be failed only for that MSISDN and not for the rest of the batch.

## Limiting Message Parts

With max number of message parts you can specify whether the message should be dispatched. If a message is split into more parts than `max_number_of_message_parts` then it will not be delivered and status code will be `411`.

A default `max_number_of_message_parts` can be configured at the account level by your account manager. If the `max_number_of_message_parts` parameter is specified when creating a batch, it will override the default value configured by your account manager.

When `max_number_of_message_parts` parameter is set or the default value is configured, `number_of_message_parts` parameter will be included in recipient delivery report.

## URL Link Previews

Some mobile devices are capable of displaying previews for links contained in SMS or MMS messages. When messages that contain a URL are received on these devices, they may be rendered in the messaging application with a preview of the linked page. This behaviour is controlled from the phone or its software, and can not be affected by Sinch or the carrier.

### Behaviour on iOS

There are multiple factors that affect whether an iPhone shows a preview for a link received in a message, and what the preview looks like:

#### Is the sender saved in the recipient's contacts list?

According to our testing, iOS will only render a preview for links sent in SMS or MMS if the sender is saved in the recipient's contact list. Otherwise, the URL will be displayed in the body of the message.

#### Is the URL at the very beginning or the very end of the message?

iPhones will only display a link preview if the URL is at the beginning or the end of the message. iOS will not render a preview for a URL in the middle of a message, or even just surrounded by periods or quotation marks.

### Behaviour on Android

Depending on the device but if the recipient has googles Messages app or Samsung you should be able to see previews if the phone has been updated after June 2018
