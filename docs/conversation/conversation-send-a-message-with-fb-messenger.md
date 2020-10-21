---
title: Send a Facebook Messenger message
excerpt: >-
  In this section you will learn how to create and configure a basic Facebook Business Page with Messenger chat feature. Once you complete the steps below you will have a Facebook App, Facebook Business Page with Messenger Chat button, a Messenger Token, and a configured Messenger Webhook to use with the Sinch Conversation API.
hidden: false
---

### Set up a Facebook Page <span class="betabadge">Beta</span>

A Facebook Page is required to use the Facebook Messenger API. It is used as the identity of your Messenger experience. When people chat with your app, they will see the Page name and the Page profile picture. To create a new Page, visit https://www.facebook.com/pages/create. If you already have one, you can skip to (#create-your-facebook-app).

![Create New Page](images/channel-support/messenger/fb_create_new_page.png)

Next, choose to create a **"Business or Brand"** and fill out the ensuing page name and contact form.

![Create Business Page](images/channel-support/messenger/fb_create_business_page.png)

You can choose to **"Skip"** adding 'Profile' and 'Background' pictures as well as **"Not Now"** for the option to 'Add online booking'. You have now completed creating your Facebook Business Page. Remember while your FaceBook App is 'In development' the FaceBook Business Page is not visible to anyone except you and other developers you invite to your FaceBook developer account.

Make sure to bookmark your FaceBook Business Page. You will head back there to send messages later.

![Business Page](images/channel-support/messenger/fb_business_page.png)

#### Add a Messenger Chat Button to your FaceBook Business Page

Click on **"+ Add Button"**.

![Facebook Page Add Button](images/channel-support/messenger/fb_page_add_button.png)

Choose **Send Message** and click **"Next"**.

![Fb Add Send Message Button](images/channel-support/messenger/fb_add_send_message_button.png)

Click on **"Messenger"** and then click **"Finish"**.

![Fb Add Button Messenger](images/channel-support/messenger/fb_add_button_messenger.png)

### Create your Facebook App

If you have an existing FaceBook Developer Account and a FaceBook App, you can skip to [Add Messenger Product to your FB App](#add-messenger-to-your-app).
To register for a FaceBook Developer account, go to **[Facebook Developer Account](https://developers.facebook.com)** and click **"Get Started"** on the upper right menu.

[](images/channel-support/messenger/fb_for_developers.png)

Once you have created your Facebook developer account, you can choose to **"Create First App"**.

![Create App Form](images/channel-support/messenger/fb_create_first_app.png)

Your new _APP ID_ will be displayed at the top left of your FaceBook App Dashboard.

![Facebook App Dashboard](images/channel-support/messenger/fb_app_dashboard.png)

#### Add Messenger to your app

From your FaceBook Developer Dashboard, under _Add Product_, click on the Messenger **"Setup"** button.

#### Generate your Messenger API Token

To generate your Messenger API Token, add the FaceBook Page you created earler. Scroll down to **Access Tokens** and click **"Add or Remove Pages"**.

![Add Remove Page](images/channel-support/messenger/fb_add_remove_page.png)

Next, follow the prompts and choose the new FaceBook Page you just created. Make sure that you leave the default setting **Manage and access Page conversations in Messenger** set to YES.

![Manage and Access Conversations](images/channel-support/messenger/fb_manage_and_access_conversations.png)

You should see your FaceBook Page listed under **Access Tokens**. Click on the **"Generate Token"** button.

Copy and store your Messenger Token somewhere safe, we will need it to add the Messenger Channel to your **Sinch Conversations App**.

![Generate Messenger Token](images/channel-support/messenger/fb_generate_messenger_token.png)

#### Configure your FaceBook Messenger Channel on Sinch Conversation API

The easiest way to configure your channel is through the App Details page in the [Sinch Portal](https://dashboard.sinch.com/convapi/apps):

![Generate Messenger Token](images/channel-support/messenger/fb_channel_config.png)

Another way is to use the **app** management API  to **Patch** your Sinch Conversations App with the newly created **Messenger Token**, this will allow the Sinch Conversations App to send messages to visitors of your FaceBook Page.

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

The Messenger Webhook Settings configuration forwards message events posted on your **FaceBook Page** to your **Sinch Conversations App**. To set the configuration, click on **"Add Callback URL"** in the FaceBook App Dashboard > Products > Messenger > Settings **Webhooks**.

![Facebook Messenger Webhooks](images/channel-support/messenger/fb_messenger_webhooks.png)

Then add the following **Callback URL** and **Verify Token**:

```Curl  Callback URL:
https://messenger-adapter.conversation-api.prod.sinch.com/adapter/v1/{{YOUR_SINCH_CONVERSATION_APP_ID}}/callback

Verify Token: 5651d9fd-5c33-4d7a-aa37-5e3e151c2a92
```

![Facebook Messenger Edit Webhook](images/channel-support/messenger/fb_messenger_edit_webhook.png)

To complete your **Webhooks** configuration, click on **"Add Subscriptions"**. Select **"messages"** and **"message_deliveries"** fields and click on **"Save"**.

![Facebook Webhook Subscription](images/channel-support/messenger/fp_messenger_webhook_subscriptions.png)

Great! You're almost there. Just a couple more steps.

### Start a conversation in Messenger and Respond with Sinch Conversation API

OK, you are ready for some action! Visit your FB Page, click on **"Send Message"** and choose **"Test Button"**.

![Facebook test send message button](images/channel-support/messenger/fb_page_test_send_message_button.png)

Enter a message into the **Messenger** chat window and **Send**.

![Facebook Messenger Pop up](images/channel-support/messenger/fb_page_messenger_pop_up.png)

Use **Sinch Conversation API** to **List Contacts**, you should now see a new contact entry generated when the **Messenger Message** was posted from your FaceBook Page.

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

Use your newly created Sinch **Contact** to send a **Text Message** response using the **message:send** function.

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

**ALRIGHT!! CONGRATULATIONS**, you have just sent your first Sinch Conversations Messenger Message!

1. Learn how to receive messages
2. Learn how to [send rich messages with Facebook messenger](doc:conversation-send-rich-messages-with-fb-messenger).
