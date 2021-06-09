---
title: Introduction
excerpt: >-
  Get introduced to the features and capabilities that the Sinch MM7 API offers.
  Send and receive rich multimedia content. 
next:
  pages:
    - mm7-service-mm7_submit
---
**Finding your VASPID (API Key)**

You must first request access to the API from your account manager. You can reset the Key at any time to revoke access. Your username and password for your account are seperate from your API key so you do not have to worry about a account password reset affecting your services.

**Finding your VASID**

Your account manager will provide you with a VASID for each shortcode. If you use a dedicated shortcode you can pick your own VASID or we can configure it empty.

**Receiving Delivery Reports and MO's**

Your Delivery Report URL and MO URL is configured by your account manager. You can update it at any time. SOAP requests will be forwarded to your server every second and require an HTTP STATUS 200 and a proper MM7 SOAP Response or else we will retry.

*We expect your server to accept our postback within 10 seconds by responding with a standard HTTP STATUS 200 header (success) and proper SOAP Response with matching MM7 TransactionID and Status 1000. If establishing a connection to your Postback URL takes longer than 10 seconds, the connection will time out and be dropped. If the connection times out or the HTTP code is not 200 we will retry the notification again five minutes later for a maximum of 5 retries per notification.*

**API Limitations**

You may have a throughput limit on your account. If your API requests exceed the throughput on your account then you may have some latency in the delivery of your messages. There may also be limits on the number of API calls allowed per second/minute/day. These limits will be published in your API Settings page. If you exceed this limit then your message will be rejected and you will be required to retry the request.

**Authentication**

We authenticate your account with your VASPID and we authenticate your short code, service and carrier reach with the VASID. We can optionally whitelist your IP Address or apply a "basic authorization" username and password requirement to your account which adds an additional level of security to your request.

**Special Considerations**

*Always Use International Number Format:*
You must use international format when submitting an MM7 message to Sinch. International format includes both the country code with the phone number. We use the country code to determine routing of the message. There should be no dialing prefixes (eg 00 or 001) or special characters such as the plus symbol when submitting messages. (example: '642111111' not '+642111111'). If you submit a message without a country code the message will likely get routed to the wrong country and you may end up paying for the delivery there.

*For example the US number (555)-319-9144 in international number format would be 15553199144 because the USA country code is 1.*
