---
title: Channel Support
excerpt: >-
  Sinch Conversation API SMS specific configurations and message transcoding.
hidden: false
---

### Conversation API Sinch SMS Support <span class="betabadge">Beta</span>

With Sinch SMS channel you get the largest possible coverage in expense of
limited rich content features.

#### Channel Configuration

##### Sinch SMS service configuration

Sending and receiving SMS messages through Conversation API requires a Sinch SMS service
plan. To ensure the best experience for your end user, we recommend that you set default sender IDs
for your service plan for each country that you have an active user base in.
By default most telecom operators allow end users to send internationally, but to be able to respond to the end user,
often a national registered sender ID must be used. For example, an inbound message from US phone number to
a Swedish long number for the service plan will be passed successfully to the Conversation API **app**
and further to the registered MESSAGE_INBOUND webhooks. However, any outbound messages (replies) to the same US phone number from the
same Conversation API **app** will need to be sent over the US long number registered for the service plan
and not through the Swedish long number. To request domestic long numbers in all relevant countries, please visit Numbers section in [Sinch Portal](https://dashboard.sinch.com/numbers).
Once numbers have been requested, please open a support ticket to request these numbers to be assigned as default originators in their respective countries.

##### Conversation API SMS Integration

Once you have your SMS service plan configured according to the above recommendations
you can enable the SMS integration for you Conversation API **app**.
You can either do that in the [Sinch Portal](https://dashboard.sinch.com/convapi/overview)
(recommended) or through the management API. For enabling the SMS channel
through the portal just select your **app** and click on "SET UP CHANNEL" beside the SMS channel.
Then from the drop-down box select your ready configured service plan and
confirm the integration.

###### SMS Integration Using Management API

To setup the SMS integration programmatically you use the management API to
update your app with SMS channel credentials.
The following snippet shows the channel
credentials configuration for **app** with SMS channel:

```json
{
  "channel_credentials": [
    {
      "channel": "SMS",
      "static_bearer": {
        "claimed_identity": "{{SERVICE_PLAN_ID}}",
        "token": "{{API_TOKEN}}"
      }
    }
  ]
}
```

You need to replace `{{SERVICE_PLAN_ID}}` and `{{API_TOKEN}}` with your
Sinch SMS service plan ID and API token.

You also need to configure the callback URL of your service plan to
point to your Conversation API **app**. This step is otherwise done automatically
if the integration is done through the [Sinch Portal](https://dashboard.sinch.com/convapi/overview).
The callback URL is the following:

```
https://whatsapp-adapter.{{REGION}}.conversation-api.int.prod.sinch.com/adapter/v1/{{CONVERSATION_APP_ID}}/callback
```

You also need to configure at least one Conversation API webhook which
will trigger POST callbacks to the given URL.
