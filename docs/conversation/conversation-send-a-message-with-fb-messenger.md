---
title: Send a Facebook Messenger message
excerpt: >-
  In this tutorial, you will learn how to create and configure a basic Facebook Business Page with Messenger chat feature. Once you complete the steps below, you will have a Facebook App, Facebook Business Page with Messenger Chat button, a Messenger Token, and a configured Messenger Webhook to use with the Sinch Conversation API.
hidden: false
---
### You will need
An active Business Facebook page. This will allow you to use the Facebook Messenger API and, for people who chat with your app, to see the page name and profile picture. 
To create a new page, visit https://www.facebook.com/pages/create. If you already have brand or business page, you can skip to (#create-your-facebook-app).

### Set up a Facebook Page <span class="betabadge">Beta</span>
To set up a Facebook Business page, take these steps.
1.	Go to https://www.facebook.com/pages/create
2.	Click **Get Started** on the **Business or Brand** image.
3.	Name your page, select a Category and create a description.
4.	Click **Create Page**.
5.	Click **Save**.
Remember the name of your business page or bookmark it, as you will need it later in this tutorial.
![Business Page](images/channel-support/messenger/fb_business_page.png)

#### Add a Messenger Chat Button to your FaceBook Business Page
To add a Messenger chat button to your Facebook Business page, take these steps.
1.	Click **+ Add a Button**.
2.	Choose **Send Message**.



### Create your Facebook App
If you have an existing FaceBook Developer Account and a FaceBook App, you can skip to [Add Messenger Product to your FB App](#add-messenger-to-your-app).
To register for a FaceBook Developer account, take these steps.
1.	Go to **[Facebook Developer Account](https://developers.facebook.com)** and click **Get Started**.
2.	Click **Next**
3.	Select the description that best describes you.
4.	Click **Create First App**. Your new APP ID will be displayed at the top left of your Facebook App Dashboard.

Your new _APP ID_ will be displayed at the top left of your FaceBook App Dashboard.

![Facebook App Dashboard](images/channel-support/messenger/fb_app_dashboard.png)

#### Add Messenger to your app
To add Messenger to your app, take this step.
1.	From your FaceBook Developer Dashboard, under **Add Product**, click the Messenger **"Setup"** button.

#### Generate your Messenger API Token
To generate your Messenger API token, take these steps.
1.	Scroll down to **Access Tokens**.
2.	Click **Add or Remove Pages**.

![Add Remove Page](images/channel-support/messenger/fb_add_remove_page.png)
3.	Follow the prompts and choose the Business Facebook page you previously created.
4.	Leave the default setting **Manage and access Page conversations in Messenger** set to **YES**.
5.	Click **Generate Token**.
6.	Copy and store your Messenger Token. You will need it to add the Messenger Channel to your Sinch Conversation App.
![Generate Messenger Token](images/channel-support/messenger/fb_generate_messenger_token.png)

#### Configure your FaceBook Messenger Channel on Sinch Conversation API
To configure your channel, take these steps.
1.	Configure your channel through the App Details page in the [Sinch Portal](https://dashboard.sinch.com/convapi/apps)
![Generate Messenger Token](images/channel-support/messenger/fb_channel_config.png)

OR
2.	Use the **app** management API  to **patch** your Sinch Conversations App with the newly created **Messenger Token**, this will allow the Sinch Conversations App to send messages to visitors of your FaceBook Business page.

```shell Curl
curl --location --request PATCH 'https://eu.conversation.api.sinch.com/v1beta/projects/{project_id}/apps' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer <access token>' \
--data-raw '{
    "channel_credentials": [
        {
            "channel": "MESSENGER",
            "static_token": {
                "token": "{{YOUR_FB_PAGE_MESSENGER_TOKEN}}"
            }
        }
    ],
    "display_name": "Messages"
}'
```

### Configure the Messenger Webhook
To configure the Messenger Webook, which forwards message events posted on your **FaceBook Page** to your **Sinch Conversations App**, take these steps.
1.	Go to Products > Messenger > Settings >  **Webhooks** in the Facebook App dashboard.
![Facebook Messenger Webhooks](images/channel-support/messenger/fb_messenger_webhooks.png)
2.	Add the below **Callback URL** and **Verify Token**:

```Curl  Callback URL:
https://messenger-adapter.conversation-api.prod.sinch.com/adapter/v1/{{YOUR_SINCH_CONVERSATION_APP_ID}}/callback

Verify Token: 5651d9fd-5c33-4d7a-aa37-5e3e151c2a92
```

![Facebook Messenger Edit Webhook](images/channel-support/messenger/fb_messenger_edit_webhook.png)
3.	To complete your **Webhooks** configuration, click  **Add Subscriptions**.
4.	 Select the **messages** and **message_deliveries** fields.
5.	 Click  **Save**.

![Facebook Webhook Subscription](images/channel-support/messenger/fp_messenger_webhook_subscriptions.png)

Great! You're almost there. Just a couple more steps.

### Start a conversation in Messenger and Respond with Sinch Conversation API
Now you’re ready for business! 
To start a conversation in Messenger and respond with the Sinch Conversation API, visit your Facebook business page and take these steps.

1.	Click **Send Message** and choose **Test Button**.

![Facebook test send message button](images/channel-support/messenger/fb_page_test_send_message_button.png)

2.	Enter a message into the **Messenger** chat window. 
3.	Click **Send Message**.
**WARNING** - Note that there is a standard messaging window of 24h on Messenger. To be able to send messages outside this response window check out Channel Specific Properties for more info.

![Facebook Messenger Pop up](images/channel-support/messenger/fb_page_messenger_pop_up.png)

Now, you can use **Sinch Conversation API** to **List Contacts** and send them a *Text message** response by taking these steps.
1.	Using the information below to and the Sinch Conversation API to list contacts, you will see a new contact entry generated when the Messenger message was posted from your Facebook business page.
```
{
    "contacts": [
        {
            "id": "J69H07BDS8G11RDF01E96CW660",
            "channel_identities": [
                {
                    "channel": "MESSENGER",
                    "identity": "7746490198930851",
                    "app_id": "3FDS0PWWERGN1QX101E75WGS3Y"
                }
            ],
            "channel_priority": [
                "MESSENGER",
            ],
            "display_name": "",
            "email": "",
            "external_id": "",
            "metadata": ""
        }
    ],
    "next_page_token": ""

```

2.	Then, use your newly created Sinch **Contact** to send a **Text Message** response using the **message:send** function.

```shell Curl
curl --location --request POST 'https://eu.conversation.api.sinch.com/v1beta/projects/{project_id}/messages:send' \
--header 'Content-Type: application/json' \
--header 'Authorization: Bearer <access token>' \
--data-raw '{
    "app_id": "{{YOUR_SINCH_APP_ID}}",
    "recipient": {
        "contact_id": "{{YOUR_SINCH_CONTACT_ID}}"
    },
    "message": {
        "text_message": {
            "text": "Greetings from Sinch Conversation API!"
        }
    },
    "channel_priority_order": [
        "MESSENGER"
    ]
}'
```

![Facebook Message Text](images/channel-support/messenger/fb_message_text.jpg)

** CONGRATULATIONS**, you have just sent your first Sinch Conversations Messenger Message!

To learn how to receive messages or see example of rich messages sent with Facebook Messenger, visit our [Handle incoming messages]( https://developers.sinch.com/docs/conversation-receive-a-message) page and {Send rich messages with Facebook Messenger]( https://developers.sinch.com/docs/conversation-send-rich-messages-with-fb-messenger) page.

