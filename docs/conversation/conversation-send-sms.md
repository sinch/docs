---
title: Send SMS  
excerpt: Learn how to set up and send a message with SMS and Conversation API.
hidden: false
next:
 pages:
  - conversation-receive-a-message
  - conversation-send-a-message-with-fb-messenger
---


## Add an SMS Channel to your Conversations API App <span class="betabadge">Beta</span>

In this tutorial, you will learn how to add an SMS channel to your Sinch Conversations API Application.  You can add your SMS channel one of two ways: either programmatically via the Sinch Conversation API or through the [Sinch online portal](https://dashboard.sinch.com).

### You will need

- A text enabled **long code** or a **short code** registered with Sinch.
- Access to the [Sinch dashboard](https://dashboard.sinch.com) where you manage your long code or short code.
- An SMS **Service Plan ID** and **Secret** to authorize SMS text message requests.
- A Conversations **App ID**.
- A Sinch **Project ID** and associated Key ID and Key Secret.

If you are missing any of the items above, you should begin by registering online at [Sinch.com](https://sinch.com). There we'll show you how to create a **New Conversations App**  and get the necessary authentication credentials.

### Create a New Conversations app

To create a new Conversations App, follow these steps.
  1. Sign in to your [Sinch Dashboard account](https://dashboard.sinch.com).
  2. Go to **Conversation API** > **Apps**.
  3. Click **NEW APP**. 
  4. Give your app a name, select the region and click **CREATE**. Once a region has been selected, it cannot be changed.

![dashboard image](images/dashboard/dashboard_new_app.png)

### Add an SMS channel to your Conversations app

To add an SMS channel to your Sinch Conversation app, follow these steps.
  1. In your Sinch Dashboard, go to **Conversation API** > **Apps**.  
  2. Click your **App Name** to add the SMS Channel.

![app added](images/channel-support/sms/sinch_conversations_apps_added.png)

  3. On the **set up channels** screen, find the SMS channel and click **SET UP CHANNEL**.

![new sms channel](images/dashboard/dashboard_add_channels.png)

  4. Choose your SMS **Service Plan ID** from the drop-down and click **SAVE**.

![new sms channel](images/channel-support/sms/sinch_conversations_new_app_add_sms_channel_form.png)

You have added an SMS Channel to your app.

![new sms channel](images/channel-support/sms/sinch_conversations_sms_channel_done.png)

### Fetch Oauth2 Token needed for authentication

To get your OAuth2 token that is needed for authentication, take these steps.

  1. In your Sinch dashboard, go to **Settings** > **Access Keys**.
  2. Click **NEW KEY**.
  3. Name your new access key.

![access keys](images/dashboard/dashboard_access_keys.png)

  4. Copy and save your **Key Secret** in a safe place. You will not be able to retrieve it again once you click CONFIRM on the **Key ID & Key Secret** dialog box.
  5. Use the key id and key secret to get an access token as shown below:

Then use the key id and key secret to obtain an access token:

```shell Curl
curl https://eu.auth.sinch.com/oauth2/token -d grant_type=client_credentials --user <key_id>:<key_secret>
```

  6. Copy the generated token and use it in the authorization header of your calls to the Sinch Conversation API.

### Send an SMS Message to a Contact

Now that you have all the necessary pieces of information, you can an SMS message to a Contact via the Sinch Conversations API app. To do so, send an HTTP POST with the following JSON. 

**Note:** - You can send SMS messages using a channel specific property called SMS_FLASH_MESSAGE. For more information, check out our [Channel Specific Properties](https://developers.sinch.com/docs/conversation-channel-properties) page.

```shell Curl
curl --location --request POST 'https://eu.conversation.api.sinch.com/v1beta/projects/{project_id}/messages:send' \
-H 'Content-Type: application/json' \
-H "Authorization: Bearer <access token>"
-d '{
    "app_id": "{{YOUR_APP_ID}}",
    "recipient": {
        "identified_by": {
            channel_identities: [
                {
                    channel:"SMS",
                    identity:"+15551231212"
                }
            ]
        }
    },
    "message": {
        "text_message": {
            "text": "Text message from Sinch Conversation API."
        }
    },
}'
```
