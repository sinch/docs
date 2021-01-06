---
title: MM7_DeliveryReport
excerpt: >-
  Receive delivery reports for sent MT. Get delivery report for previously sent
  MMS messages. Read more.
next:
  pages:
    - mm7-service-sinch-operator-ids
---
**Receive Delivery Reports for previously submitted MT**

Sinch sends delivery reports using the MM7 Delivery Report message type i.e., "DeliveryReportReq". The delivery report indicates the current state of the original submit request message. We will send a delivery report to your platform only when the appropriate information is available. If the delivery report message is accepted or rejected then respond with an "DeliveryReportRsp", including a status that indicates why the delivery report was accepted/rejected. For information about the status codes returned for Delivery Reports, please see `mm7_delivery_report_statuses`.

## MM7\_DeliveryReport.REQ

**MM7 delivery report request elements**

|    **Element**    |                **Description**                                                                                                                                                                |      **Returned**                     |
| ----------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------- |
| TransactionID     | This identifies the DeliveryReportReq/DeliveryReportRsp pair. It is Sinch generated ID.                                                                                                       | Always                                |
| DeliveryReportReq | Identifies the message as an MM7 Delivery Report.                                                                                                                                              | Always                                   |
| MM7Version        | Identifies the MM7 Version. (Supported versions are ver-5.3.0 and ver-6.8.0)                                                                                                                  | Always                                |
| Recipient         | The mobile phone number of the end user. This must be a valid mobile number in international format without a leading + symbol; for <example:,12515550123> (US) and 447700900750 (UK).        | Always                                |
| Sender            | Your shortcode. This should match the same information that is linked in the MT configuration, and generally to the service you are providing.                                                | Always                                |
| MessageID         | Sinch generated ID linked to the submitted message. This ID was returned to your system in the initial response (SubmitRsp) to your MT MMS request.                                           | Always                                |
| Date              | The date and time of the submission of the multimedia message (timestamp). Value is in UTC.                                                                                                   | Always                                |
| MMStatus          | A code that indicates whether the MT message was delivered successful or failed. For information about the status codes returned for Delivery,Reports, please see `delivery_report_statuses`. | Always                                |
| UACapabilities    | Also known as MMS User Agent capabilities. This describes the capabilities of the MMS User agent of the mobile handset.                                                                       | Only when provided by mobile operator |

See `unsupported_mm7_soap_elements_DeliveryReport`.

**Example Request**
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<soap-env:Envelope xmlns:soap-env="http://schemas.xmlsoap.org/soap/envelope/" xmlns=
"http://www.3gpp.org/ftp/Specs/archive/23_series/23.140/schema/REL-6-MM7-1-4">
<soap-env:Header>
   <TransactionID soap-env:mustUnderstand="1">10000001</TransactionID>
</soap-env:Header>
<soap-env:Body>
   <DeliveryReportReq>
      <MM7Version>6.8.0</MM7Version>
      <MessageID>369500617770864640</MessageID>
      <Recipient>
         <Number>16175550123</Number>
      </Recipient>
      <Sender>
         <Number>111122</Number>
      </Sender>
      <Date>2015-03-16T14:03:51.749Z</Date>
      <MMStatus>Retrieved</MMStatus>
      <StatusText>Success</StatusText>
      <UACapabilities UAProf="Samsung Galaxy" />
   </DeliveryReportReq>
</soap-env:Body>
</soap-env:Envelope>
```


## MM7\_DeliveryReport.RES

**MM7 delivery report response elements**

Your system should respond to the delivery report request with a delivery report response containing the elements described in the following table.

|      **Element**  |                  **Description**                                                                                                                                         |
| ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| TransactionID     | Identifies the DeliveryReportReq/DeliveryReportRsp pair. It is part of the SOAP header. The value that was provided with the deliveryReportReq is returned.              |
| DeliveryReportRsp | Identifies the message as an MM7 Delivery Report Response.                                                                                                               |
| MM7Version        | Identifies the MM7 Version.(Supported versions are ver-5.3.0 and ver-6.8.0)                                                                                              |
| StatusCode        | A code that indicates whether you received the MO message request successfully. The status code for successful deliver is 1000. See all Status Codes: `mms_status_codes` |
| StatusText        | Description of the status code.                                                                                                                                          |

**Example Response**
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<soap-env:Envelope xmlns:soap-env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi=
"http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<soap-env:Header>
   <TransactionID xmlns="http://www.3gpp.org/ftp/Specs/archive/23_series/23.140/schema/REL-6-MM7-1-4" soap-env:mustUnderstand="1">1000001</TransactionID>
</soap-env:Header>
<soap-env:Body>
   <DeliveryReportRsp xmlns="http://www.3gpp.org/ftp/Specs/archive/23_series/23.140/schema/REL-6-MM7-1-4">
      <MM7Version>6.8.0</MM7Version>
      <Status>
         <StatusCode>1000</StatusCode>
         <StatusText>Successfully Received MMS.</StatusText>
      </Status>
   </DeliveryReportRsp>
</soap-env:Body>
</soap-env:Envelope>
```


## Delivery Report Full Example

**Request**

```text
POST / HTTP/1.1
SOAPAction: "http://www.3gpp.org/ftp/Specs/archive/23_series/23.140/schema/REL-6-MM7-1-4"
Content-Type: multipart/related; type="text/xml";
Host: api.Mblox.com
Content-Length: 2546
X-Mblox-Carrier-Id: 0001890
Connection: Keep-Alive

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<soap-env:Envelope xmlns:soap-env="http://schemas.xmlsoap.org/soap/envelope/" xmlns=
"http://www.3gpp.org/ftp/Specs/archive/23_series/23.140/schema/REL-6-MM7-1-4">
<soap-env:Header>
   <TransactionID soap-env:mustUnderstand="1">10000001</TransactionID>
</soap-env:Header>
<soap-env:Body>
   <DeliveryReportReq>
      <MM7Version>6.8.0</MM7Version>
      <MessageID>369500617770864640</MessageID>
      <Recipient>
         <Number>16175550123</Number>
      </Recipient>
      <Sender>
         <Number>111122</Number>
      </Sender>
      <Date>2015-03-16T14:03:51.749Z</Date>
      <MMStatus>Retrieved</MMStatus>
      <StatusText>Success</StatusText>
      <UACapabilities UAProf="Samsung Galaxy" />
   </DeliveryReportReq>
</soap-env:Body>
</soap-env:Envelope>
```


**Response**
```text
HTTP/1.1 200 OK
Server: Apache
Content-Type: text/xml; charset=utf-8
Content-Length: 539
Date: Mon, 16 Mar 2015 14:03:32 GMT

<?xml version="1.0" encoding="UTF-8" ?>
<soap-env:Envelope xmlns:soap-env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi=
"http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<soap-env:Header>
   <TransactionID xmlns="http://www.3gpp.org/ftp/Specs/archive/23_series/23.140/schema/REL-6-MM7-1-4" soap-env:mustUnderstand="1">1000001</TransactionID>
</soap-env:Header>
<soap-env:Body>
   <DeliveryReportRsp xmlns="http://www.3gpp.org/ftp/Specs/archive/23_series/23.140/schema/REL-6-MM7-1-4">
      <MM7Version>6.8.0</MM7Version>
      <Status>
         <StatusCode>1000</StatusCode>
         <StatusText>Successfully Received MMS.</StatusText>
      </Status>
   </DeliveryReportRsp>
</soap-env:Body>
</soap-env:Envelope>
```
