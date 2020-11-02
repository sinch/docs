---
title: MM7_Deliver
excerpt: >-
  Recieve MMS MO submitted by end users to your platform. The message from an
  end user is delivered by supplying the MMS as payload of the request message.
  Read more.
next:
  pages:
    - mm7-service-mm7_deliveryreport
---
**Receive MMS MO submitted by end users to your platform**

Sinch delivers messages from end users to your platform by supplying the MMS as the payload of the request message. The deliver request is made using MM7 SOAP "DeliverReq". Message include identification of the request that is used by your platform to correlate a response to the message. Your platform must reply with a SOAP response "DeliverRsp", indicating that the message was successfully received and will be processed. If you cannot identify the requested content or if the delivered content does not fulfill the conditions you'd expect, then your platform should indicate a failure in the "DeliverRsp" status field.

## MM7\_Deliver.REQ

**MM7 MO deliver request elements**

**1. HTTP header elements**

<div class="magic-block-html">
  <div class="marked-table">
    <table>
      <thead>
        <tr>
          <th>Header Name</th>
          <th>Description</th>
          <th>Mandatory</th>
        </tr>
      </thead>
      <tbody>
        <tr class="even">
          <td>X-Mblox-Carrier-Id</td>
          <td>
            <div class="line-block">Sinch Mobile Operator ID.
              Examples: AT&amp;T=0001470, Verizon=0001890.
              See all <code class="interpreted-text" data-role="ref">mblox_operator_ids</code></div>
          </td>
          <td>No</td>
        </tr>
      </tbody>
    </table>
  </div>
</div>

**2. Elements in the SOAP header and body**

|   **Element**  |     *Description**                                                                                                                                                                     |                                       |
| -------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------- |
| TransactionID  | It is a Sinch generated transaction ID. Identifies the DeliverReq/DeliverRsp pair.                                                                                                      | Always                                |
| DeliverReq     | Identifies the message as an MM7 deliver request.                                                                                                                                      | Always                                |
| MM7Version     | Identifies the MM7 Version. (Supported versions are ver-5.3.0 and ver-6.8.0)                                                                                                            | Always                                |
| Sender         | The mobile phone number of the end user. This must be a valid mobile number in international format without a leading + symbol; for <example:12515550123> (US) and 447700900750 (UK). | Always                                |
| Recipients     | The address of the message recipients i.e., Shortcode/Longcode                                                                                                                         | Always                                |
| LinkedID       | Identifier for the MO message. This is a Sinch generated ID.                                                                                                                           | Always                                |
| TimeStamp      | The date and time of the submission of the MO message. This value is in UTC.                                                                                                           | Always                                |
| Priority       | The priority (importance) of the message. Possible values: High, Normal, Low                                                                                                           | Only when provided by mobile operator |
| Subject        | Title of the whole multimedia message.                                                                                                                                                 | Only when provided by mobile operator |
| Content        | A reference to the content of the MM7 message. Contains an “href:cid” attribute that links to the content ID of the first attachment in the MM7 message.                               | Only when provided by mobile operator |
| UACapabilities | Information about the capabilities of the MMS user agent that originated,the multimedia message. In this context, the associated timestamp is not populated.                           | Only when provided by mobile operator |

See unsupported elements: `unsupported_MM7_SOAP_Elements_Deliver`

**Example Request**
```xml
<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<soap-env:Envelope xmlns:ns1="http://schemas.xmlsoap.org/soap/envelope/" xmlns=
"http://www.3gpp.org/ftp/Specs/archive/23_series/23.140/schema/REL-6-MM7-1-4">
<soap-env::Header>
   <TransactionID soap-env:mustUnderstand="1">1000001</TransactionID>
</soap-env::Header>
<soap-env::Body>
   <DeliverReq>
      <MM7Version>6.8.0</MM7Version>
      <LinkedID>1000001</LinkedID>
      <Sender>
         <Number>1617423433</Number>
      </Sender>
      <Recipients>
         <To>
         <Number displayOnly="false">111122</Number>
         </To>
      </Recipients>
      <TimeStamp>2014-04-14T16:15:23.414Z</TimeStamp>
      <Priority>Normal</Priority>
      <Content href="cid:default.cid" allowAdaptations="true"/>
   </DeliverReq>
</soap-env:Body>
</soap-env:Envelope>
```


## MM7\_Deliver.RES

**MM7 MO deliver response elements**

Your system should respond to the deliver request with a deliver response containing the elements described in the following table.

| **Element**   |         **Description**                                                                                                                                                 |
| ------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| TransactionID | Identifies the DeliverReq/DeliverRsp pair. It is part of the SOAP header. The value returned is the one provided in the request.                                        |
| DeliverRsp    | Identifies the message as a MM7 Deliver Response.                                                                                                                       |
| MM7Version    | Identifies the MM7 Version.(Supported versions are ver-5.3.0 and ver-6.8.0)                                                                                             |
| StatusCode    | A code that indicates whether you recieved the MO message request successfully. The status code for successful deliver is 1000.See all Status Codes: `mms_status_codes` |
| StatusText    | Description of the status code.                                                                                                                                         |

**Example Response**
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<soap-env:Envelope xmlns:soap-env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi=
"http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<soap-env:Header>
   <TransactionID xmlns="http://www.3gpp.org/ftp/Specs/archive/23_series/23.140/schema/REL-6-MM7-1-4" soap-env:mustUnderstand="1">1000001</TransactionID>
</soap-env:Header>
<soap-env:Body>
   <DeliverRsp xmlns="http://www.3gpp.org/ftp/Specs/archive/23_series/23.140/schema/REL-6-MM7-1-4">
      <MM7Version>6.8.0</MM7Version>
      <Status>
         <StatusCode>1000</StatusCode>
         <StatusText>Successfully received MMS</StatusText>
      </Status>
   </DeliverRsp>
</soap-env:Body>
</soap-env:Envelope>
```


## Receive MMS MO Full Example

**Request**
```text
POST / HTTP/1.1
SOAPAction: "http://www.3gpp.org/ftp/Specs/archive/23_series/23.140/schema/REL-6-MM7-1-4"
Content-Type: multipart/related; start="soap-start"; type="text/xml";
        boundary="----=_Part_139078_1411587550.1397492135426"
Host: api.Mblox.com
Content-Length: 2546
X-Mblox-Carrier-Id: 0001890
Connection: Keep-Alive

------=_Part_139078_1411587550.1397492135426
Content-Type: text/xml
Content-ID: <soap-start>

<?xml version="1.0" encoding="UTF-8" standalone="yes"?>
<soap-env:Envelope xmlns:ns1="http://schemas.xmlsoap.org/soap/envelope/" xmlns=
"http://www.3gpp.org/ftp/Specs/archive/23_series/23.140/schema/REL-6-MM7-1-4">
<soap-env::Header>
   <TransactionID soap-env:mustUnderstand="1">1000001</TransactionID>
</soap-env::Header>
<soap-env::Body>
   <DeliverReq>
      <MM7Version>6.8.0</MM7Version>
      <LinkedID>1000001</LinkedID>
      <Sender>
         <Number>1617423433</Number>
      </Sender>
      <Recipients>
         <To>
         <Number displayOnly="false">111122</Number>
         </To>
      </Recipients>
      <TimeStamp>2014-04-14T16:15:23.414Z</TimeStamp>
      <Priority>Normal</Priority>
      <Content href="cid:default.cid" allowAdaptations="true"/>
   </DeliverReq>
</soap-env:Body>
</soap-env:Envelope>

------=_Part_139078_1411587550.1397492135426
Content-Type: multipart/mixed;
        boundary="----=_Part_139079_1300104441.1397492135426"
Content-ID: <default.cid>

------=_Part_139079_1300104441.1397492135426
Content-Type: image/jpeg
Content-Transfer-Encoding: binary
Content-ID: image_0.jpg
<Binary contents>

------=_Part_139079_1300104441.1397492135426
Content-Type: text/plain
Content-Transfer-Encoding: binary
Content-ID: text_0.txt
Test MO message!

------=_Part_139079_1300104441.1397492135426--

------=_Part_139078_1411587550.1397492135426--
```


**Response**
```text
HTTP/1.1 200 OK
Server: Apache
Content-Type: application/xml; charset=utf-8
Content-Length: 715
Date: Mon, 16 Mar 2015 17:46:59 GMT


<?xml version="1.0" encoding="UTF-8" ?>
<soap-env:Envelope xmlns:soap-env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi=
"http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<soap-env:Header>
   <TransactionID xmlns="http://www.3gpp.org/ftp/Specs/archive/23_series/23.140/schema/REL-6-MM7-1-4" soap-env:mustUnderstand="1">1000001</TransactionID>
</soap-env:Header>
<soap-env:Body>
   <DeliverRsp xmlns="http://www.3gpp.org/ftp/Specs/archive/23_series/23.140/schema/REL-6-MM7-1-4">
      <MM7Version>6.8.0</MM7Version>
      <Status>
         <StatusCode>1000</StatusCode>
         <StatusText>Successfully received MMS</StatusText>
      </Status>
   </DeliverRsp>
</soap-env:Body>
</soap-env:Envelope>
```
