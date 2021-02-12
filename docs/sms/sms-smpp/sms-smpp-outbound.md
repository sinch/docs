---
title: Outbound SMS SMPP
excerpt: ''
next:
  pages:
    - sms-smpp-inbound
    - sms-smpp-error-specification
    - sms-smpp-encoding
---
### SMPP account details

|           |                                     |
| --------- | ----------------------------------- |
| Host      | \<host\>.smpp.api.sinch.com |
| Port      | 3600 (use 3601 for SSL)             |
| System-ID | \<system-id\>                       |
| Password  | \<password\>                        |

Maximum instances (binds): Default 2 per SMPP account. For more connections, contact your account manager.

### IP Access

To access the Sinch SMPP server, the client IP range must be announced and added to the client account.

### Supported SMPP PDUs

|    **SMPP PDU types**                       |
| ------------------------------------------- |
| bind\_transmitter / bind\_transmitter\_resp |
| bind\_receiver/ bind\_receiver\_resp        |
| bind\_transceiver / bind\_transceiver\_resp |
| enquire\_link / enquire\_link \_resp        |
| unbind / unbind\_resp                       |
| submit\_sm / submit\_sm\_resp               |
| data\_sm / data\_sm\_resp                   |
| deliver\_sm / deliver\_sm\_resp             |
| generic\_nack                               |
| query\_sm / query\_sm\_resp                 |
| cancel\_sm / cancel\_sm\_resp               |
| replace\_sm / replace\_sm\_resp             |

### Source Address

<div class="magic-block-html">
    <div class="marked-table">
        <table class="docutils">
            <thead valign="bottom">
            <tr class="row-odd">
                <th class="head" colspan="5">Source Address</th>
                <th class="head" rowspan="2">Arrive on handset</th>
            </tr>
            <tr class="row-even">
                <th class="head">Sent to Sinch</th>
                <th class="head" colspan="2">Type Of Number(TON)</th>
                <th class="head" colspan="2">Number Plan Indicator(NPI)</th>
                <th colspan="1"></th>
            </tr>
            </thead>
            <tbody valign="top">
            <tr class="row-odd">
                <td>00461234567889</td>
                <td>0x00</td>
                <td>Unknown</td>
                <td>0x01</td>
                <td>MSISDN</td>
                <td>046123456789</td>
            </tr>
            <tr class="row-even">
                <td>01234567889</td>
                <td>0x00</td>
                <td>Unknown</td>
                <td>0x01</td>
                <td>MSISDN</td>
                <td>123456789</td>
            </tr>
            <tr class="row-odd">
                <td>461234567889</td>
                <td>0x01</td>
                <td>International</td>
                <td>0x01</td>
                <td>MSISDN</td>
                <td>+46123456789</td>
            </tr>
            <tr class="row-even">
                <td>1234567889</td>
                <td>0x02</td>
                <td>National</td>
                <td>0x01</td>
                <td>MSISDN</td>
                <td>123456789</td>
            </tr>
            <tr class="row-odd">
                <td>Sinch</td>
                <td>0x05</td>
                <td>Alphanumeric</td>
                <td>0x00</td>
                <td>Unknown</td>
                <td>Sinch</td>
            </tr>
            </tbody>
        </table>
    </div>
</div>

The type of source address is defined by the source address TON and NPI parameters.

> - Alphanumeric sender is limited to a maximum length of 11 characters.
> - MSISDN sender maximum length is 18.
> - To enable a handset to respond to a short code, we recommend to use TON=0x00 and NPI=0x01.

### Destination address

<div class="magic-block-html">
    <div class="marked-table">
        <table class="docutils">
            <thead valign="bottom">
            <tr class="row-odd">
                <th class="head" colspan="5">Destination Address</th>
            </tr>
            </thead>
            <tbody valign="top">
            <tr class="row-even">
                <td>Sent to Sinch</td>
                <td colspan="2">Type Of Number (TON)</td>
                <td colspan="2">Number Plan Indicator (NPI)</td>
            </tr>
            <tr class="row-odd">
                <td>46123456789</td>
                <td colspan="2">0x01 | International</td>
                <td>0x01</td>
                <td>MSISDN</td>
            </tr>
            </tbody>
        </table>
    </div>
</div>

### MSISDN Format

In GSM, MSISDN is built up as:

| MSISDN | CC + NDC + SN             |
| ------ | ------------------------- |
| CC     | Country Code              |
| NDC    | National Destination Code |
| SN     | Subscriber Number         |

For further information on the MSISDN format, see the ITU-T specification E.164.

### Throughput and throttling

Throughput is the maximum number of MT messages per second that can be sent from the SMPP account to Sinch. The default value is 10 per bind. To increase the value, contact your account manager. 

The recommended default window size (maximum open requests) is 10. The throughput can be restricted by the capacity of the operator receiving the MT messages.

### Enquire link

It is recommended to set your enquire\_link requests to 60 seconds.

### Schedule delivery time

In order to schedule a message to be sent in the future, the optional parameter schedule\_delivery\_time may be added. Messages can be scheduled a maximum of 168 hours, one week, ahead of time. It can be specified in either absolute time format or relative time format. If this parameter is omitted, messages are sent immediately. 

For example, the following time formats:

| schedule\_delivery\_time | Would be interpreted as:                                                                     |
| ------------------------ | -------------------------------------------------------------------------------------------- |
| 100312163200004+         | An absolute time - March 12 2010 16:32:00 GMT+1h.                                              |
| 000006233429000R         | A relative period of 6 days, 23 hours, 34 minutes and 29 seconds from the current SMSC time. |

### Time Zone

Sinch SMSC uses UTC (Coordinated Universal Time)

### MCC MNC in delivery reports

MCC + MNC information in SMPP delivery receipts can be included as an optional parameter. This information is passed (configurable per SMPP account whether to enable this) in a vendor-specific TLV (0x1403).

In the value field, you will receive the MCC MNC of the destination operator.

Example:

Optional parameter: 0x1403 (0x1403):

> - Tag: 0x1403
> - Length: 6
> - Value (hex): 32 33 34 33 30 00 (Text: 23430n)

Result is MCC=234 and MNC=30

