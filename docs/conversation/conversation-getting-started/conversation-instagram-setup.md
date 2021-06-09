---
title: Setting up Instagram and Facebook Platforms to allow sending messages 
excerpt: >- 
  In this guide, you will learn how to set up everything on Facebook and Instagram Platforms before sending/receiving messages using Sinch Conversation API. 
hidden: false
---

Before you start sending test messages to your Instagram business account, you will need access to the following:

* [An Instagram business account that will receive and send messages](#create-an-instagram-business-account)
* [A Facebook page connected with that account](#create-a-facebook-page)
* A Facebook developer account that can perform tasks on that page
* [A registered Facebook App with basic settings configured](#create-a-facebook-app)
* [An Instagram authentication token](#generate-the-instagram-access-token)

> 🚧 Warning
>
> Sinch Conversation API for Instagram messaging service is only available as a Closed Beta integration. To add the Instagram messaging service to your App, you should contact Sinch Account Manager.
> 
> For testing purposes, you will need one test Instagram business account. You can create a new Instagram business account by creating a new or renaming an account with "test_" as a prefix to your username. For example:
> 
> @test_Sinch
> 
> Test Instagram business accounts will only receive Instagram messages from users that are added as a tester, developer or admin of the Facebook App linked with the Instagram Account.
> 

### Create an Instagram Business Account

> 📘 Note
>
> If you already have an Instagram business account you can skip this step.

On Instagram platform, you can convert your personal profile to a business account or create a new one using this [guide](https://www.facebook.com/business/help/502981923235522).

Only business accounts can send and receive messages using the Conversation API Instagram integration.

### Create a Facebook Page

> 📘 Note
>
> If you already have a Facebook Page you can skip this step.

To send and receive message using the Instagram messaging service, you need a Facebook Page connected to your Instagram business account. You can learn how to create a Facebook Page [here](https://www.facebook.com/business/help/104002523024878).

### Create a Facebook App

> 📘 Note
>
> If you already have a Facebook App you can skip this step.

To set up the Instagram messaging service, you need a Facebook App. If you need to create one, follow this [guide](https://developers.facebook.com/docs/development/create-an-app/).

### Connect the Facebook Page to the Instagram Business Account

1. Access the settings section of your Facebook Page.

   ![Facebook Page](../conversation-channel-support/images/channel-support/instagram/fb_page.png)

2. Navigate to the Instagram section and click the **Connect Account** button and login to your Instagram business account. 

   ![Facebook Page Settings Instagram](../conversation-channel-support/images/channel-support/instagram/fb_page_instagram.png)

3. Your Instagram account is now connected to your Facebook Page.

   ![Facebook Page Settings Instagram Connected](../conversation-channel-support/images/channel-support/instagram/fb_page_instagram_connected.png)

### Enable Connected Tools in Instagram Business Account

In order to send messages using the Instagram messaging service you must enable **Connected Tools** in the Instagram business account.

1. In the Instagram app Settings menu, navigate to the **Privacy** section.
2. In the **Privacy** menu, navigate to the **Messages** section.
3. In the **Message Controls** menu, enable **Allow Access to Messages** under the **Connected Tools** section.

![Instagram Connected Tools](../conversation-channel-support/images/channel-support/instagram/ig_connected_tools.png)

### Generate the Instagram Access Token

1. Log in to the developers.facebook.com and select your App.

   ![Facebook App - Instagram Settings](../conversation-channel-support/images/channel-support/instagram/fb_gen_token.png)

2. Click **Add or Remove Pages** and select your Instagram business account and your Facebook Page.

   ![Facebook App - Select IG](../conversation-channel-support/images/channel-support/instagram/fb_gen_token_add_ig.png)

3. On this page, you can see all Facebook Pages that are connected to your Facebook App.

   ![Facebook App - Pages](../conversation-channel-support/images/channel-support/instagram/fb_gen_token_pages.png)

4. Click the **Generate Token** button to generate an Instagram Access Token.

> 📘 Note
>
> In order to generate a token, the Facebook Page must be connected to an Instagram Business Account.

   ![Facebook App - Token Popup](../conversation-channel-support/images/channel-support/instagram/fb_gen_token_popup.png)

Now you can use the generated Instagram Access Token to [set up your Conversation API Instagram integration](doc:conversation-instagram).
