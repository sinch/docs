---
title: Introduction
excerpt: >-
  Get information about a phone number (MSISDN) using the Sinch Number Lookup
  Service.
next:
  pages:
    - number-lookup-enum-interface
    - number-lookup-http-interface
---
The core functionality of the Number Lookup Service is to provide information about the MSISDNs queried. MSISDN in - information about the MSISDN out.

In some countries information is accessible via national MNP databases and in some markets the only way to get information about a certain MSISDN is to query the HLR of the network the subscriber is connected to.

To provide data of highest quality we have created a Number Lookup service that combines data sourced directly from local number portability databases with an extensive global HLR coverage powered by our MNO partners. The service is built on a true carrier grade platform developed by our Operator division, ensuring excellent performance in terms of availability, throughput and latency. Just like all Sinch’s services the Number Lookup service is fully redundant. Our Number Lookup service is accessible via two interfaces:
> - ENUM
> - HTTP/HTTPS

The use cases for Number Lookup services are many but most common is to use Number Lookup for Call- and SMS routing optimizing, authentication services and number database cleaning. Knowing which network a subscriber is connected to is key to create a high quality messaging service and information about in which country a subscriber is located in, has proven very useful in authentication services.

## Number Portability Lookup and Real-Time Lookup

Depending on the use case different data about the MSISDN is needed - that’s why we have packaged our Number Lookup services in to two different products: Number Portability Lookup and Real-Time Lookup. The Number Portability Lookup service is optimized for Call and SMS routing and provides you with MCC+MNC of the operator the subscriber belongs to. Data sources used for the service are national MNP databases and HLR queries. The Real-Time Lookup service is based on real-time HLR queries only, and is commonly used for authentication services where additional information in addition to MCC+MNC is needed.

Input for a Number Lookup query is always an MSISDN - the table below illustrates which data is returned when using our Number Lookup services via the two interfaces.

<div class="magic-block-html">
    <div class="marked-table">
        <table>
        <thead>
          <tr>
          <th></th>
            <th>ENUM</th>
            <th>HTTP/HTTPS</th>
          </tr>
        </thead>
            <tbody>
            <tr class="even">
                <td><strong>Number Portability Lookup</strong></td>
                <td>MCC + MNC</td>
                <td>MCC + MNC</td>
            </tr>
            <tr class="odd">
                <td><strong>Real-Time Lookup</strong></td>
                <td>MCC + MNC</td>
                <td>MCC + MNCCountry Code
                Handset Status Code</td>
            </tr>
            </tbody>
        </table>
    </div>
</div>
