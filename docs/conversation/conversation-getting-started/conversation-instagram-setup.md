---
title: Setting up Instagram and Facebook to allow sending messages 
excerpt: >- 
  In this guide, you will learn how to set up everything on Facebook and Instagram before sending messages 
hidden: false
---

Before you start sending messages using hte Instagram Messaging API, you will need access to the following:

* An Instagram business account, that will receive and send messages
* A Facebook page connected with that account
* A Facebook developer account that can perform tasks on that page
* A registered Facebook App with basic settings configured
* An Instagram Authentication Token

### Creating an Instagram Business Account

On Instagram, you can convert your personal profile to a business account or create a new one following
this [guide](https://www.facebook.com/business/help/502981923235522).

Only business accounts can send and receive messages using the Conversation API Instagram integration.

> 📘 If you already have an Instagram business Account your can skip this step.

### Creating a Facebook Page

In order to send and receive message using the Instagram Message API, you need a Facebook Page that should be connected
to your Instagram business account. You can learn how to create a Facebook
page [here](https://www.facebook.com/business/help/104002523024878).

> 📘 If you already have a Facebook Page your can skip this step.

### Creating a Facebook App

In order to set up the Instagram Messaging API, you should create a Facebook App, following
this [guide](https://developers.facebook.com/docs/development/create-an-app/).

> 📘 If you already have a Facebook App your can skip this step.

> 🚧 By now, Instagram Messaging Product is allowed as a Closed Beta integration, so, in order to add
> the Instagram Messaging product to your Facebook App, you should contact us.

### Connecting the Facebook Page to the Instagram Business Account

* Access the settings sections of your Facebook Page

![Facebook Page](../conversation-channel-support/images/channel-support/instagram/fb_page.jpg)

* Access the Instagram section

![Facebook Page Settings](../conversation-channel-support/images/channel-support/instagram/fb_page_settings.jpg)

* Click "Connect button" and login to you Instagram business Account

![Facebook Page Settings Instagram](../conversation-channel-support/images/channel-support/instagram/fb_page_instagram.jpg)

* Now your Instagram account is connected to your Facebook Page.

![Facebook Page Settings Instagram Connected](../conversation-channel-support/images/channel-support/instagram/fb_page_instagram_connected.jpg)

### Enabling Connected Tools in Instagram Business Account

In order to send messages using the Instagram API you should enable connected tools in the Instagram business account,
just follow these steps:

![Instagram Connected Tools](../conversation-channel-support/images/channel-support/instagram/ig_connected_tools.png)

### Generating the Instagram Access Token

* Access "developers.facebook.com" and select your App.

![Facebook App - Instagram Settings](../conversation-channel-support/images/channel-support/instagram/fb_gen_token.png)

* Click "Add or Remove Pages" and select your Instagram Business Account and your Facebook Page.

![Facebook App - Select IG](../conversation-channel-support/images/channel-support/instagram/fb_gen_token_add_ig.png)

![Facebook App - Select Page](../conversation-channel-support/images/channel-support/instagram/fb_gen_token_add_page.png)

* Now you can visualize all the Facebook Pages that are connected to your Facebook App.

![Facebook App - Pages](../conversation-channel-support/images/channel-support/instagram/fb_gen_token_pages.png)

* Click "Generate Token" to generate an Instagram Access Token to the Facebook Page connected to a Instagram Business
  Account that you want to send and receive messages using an API.

![Facebook App - Token Popup](../conversation-channel-support/images/channel-support/instagram/fb_gen_token_popup.png)

Now you can use the generated Instagram Access Token
to [set up your Conversation API Instagram integration](doc:conversation-instagram).