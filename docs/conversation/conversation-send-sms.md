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

In this section, you will learn how to add an SMS channel to your Sinch Conversations API Application.  You can add your SMS channel one of two ways: either programmatically via Sinch Conversation API or through the [Sinch online portal](https://dashboard.sinch.com).  Before we begin here are a few items you should already have:

1. A text enabled *long code* or a *short code* registered with Sinch.
2. Access to the Sinch dashboard where you manage your long code or short code.
3. An SMS *Service Plan ID* and *Secret* to authorize SMS text message requests.
4. A Conversations *App ID*.
5. A Sinch *Project ID* and associated Key ID and Ket Secret.

If you are missing any of items 1-3 above you should begin by registering online at [*Sinch.com*](https://sinch.com).  We'll show you how to create a *New Conversations App*  and get authentication credentials for items 4 and 5.

### Create a New Conversations App

To create a new Conversations App, simply sign in to your [*Sinch Dashboard account*](https://dashboard.sinch.com) and use the menu on the left to access Conversations > Apps.

Click on the *New App* button on the right, "Name" your App, choose an appropriate region for it, and click *Create*.

![dashboard image](images/channel-support/sms/sinch_conversations_apps.png)

### Add an SMS Channel to your Conversations App

In your Sinch Dashboard, navigate to Conversations > Apps.  Click on the *"App Name"* you wish to add the SMS Channel to.

![app added](images/channel-support/sms/sinch_conversations_apps_added.png)

Under *Set up channels* find SMS channel and click on *"Set Up Channel"*. 

![new sms channel](images/channel-support/sms/sinch_conversations_new_app_add_channels.png)

For setting up the SMS channel you need to choose your SMS *Service Plan ID* from the drop-down and click *"Save"*.

![new sms channel](images/channel-support/sms/sinch_conversations_new_app_add_sms_channel_form.png)

You have added an SMS Channel to your App.  Just a few more steps to go.

![new sms channel](images/channel-support/sms/sinch_conversations_sms_channel_done.png)

### Fetch Oauth2 Token needed for authentication

Go to the Access Keys page under Settings and create new key by pressing the "New Key" button:

![access keys](images/channel-support/sms/sinch_conversation_access_keys.png)

Be sure to copy and store in a safe place the Key Secret you will get. You will not be able to retrieve it again once you’ve created the key.

Then use the key id and key secret to obtain an access token:

```shell Curl
curl https://eu.auth.sinch.com/oauth2/token -d grant_type=client_credentials --user <key_id>:<key_secret>
```

Copy the token and use it in the Authorization header of your calls to Sinch Conversations API.

### Send an SMS Message to a Contact

To send an SMS message to a Contact via the Sinch Conversations API App, send an HTTP POST with the following JSON:

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
