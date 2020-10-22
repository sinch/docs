---
title: Quick Start Guide
excerpt: >-
  Getting started with a quick guide for Conversation API.
hidden: false
---

In this guide we’ll go through the necessary steps of creating your first App.

## 1. Creating and configuring a new App

Go to the [Apps page](https://dashboard.sinch.com/convapi/apps) to get started on creating your App.
 
Once you’ve started to create a new App you are prompted to specify an internal display name as well as in which [**region**](doc:conversation#regions) the App should be stored. 

> 📘 Note
>
> Please note that once an App is created it cannot be migrated to another region.

![New App](images/dashboard/dashboard_new_app.png)

Once a display name is created, and a region is selected, a unique App ID will be generated for you.

## 2. Selecting and adding channels

Once step 1 is completed, start setting up the [**channels**](doc:conversation#supported-channels) you want to include in the conversation App.

Select the channel you wish to set up. Keep in mind that you need to register each channel before you can include it in a conversation App.

![New Channel](images/dashboard/dashboard_add_channels.png)

Depending on the channel, select your channel sender identity to configure your channel to work with your Conversation API App. Note that you must first set up a Service plan ID/Service ID/Sender Identity at corresponding channel portals.

The callback URL of the underlying channel should be updated to point to the channel adapter of the Conversation API. Note: the callback URL for most channels will be updated automatically which will overwrite the current callback URL.

> 📘 Note
>
> Some channels require an access token or more, like Messenger or Viber. Please visit the Messenger or Viber documentation on how to set up these channels and acquire the token.

## 3. Reordering and prioritizing channels

Conversation API will try to send messages on the channels based on the priority preference you set. If there is no preference set, the API will try the order priority based on the following criteria: 

1. If a conversation with the contact exists: the active channel of the conversation is tried first.

2. The existing channels for the contact, that are ordered by contact channel preferences (if given).

3. Lastly the existing channels for the contact are ordered by the App priority.

You can define the App channel priority by reordering the channels that you have set up.

![Channels](images/dashboard/dashboard_quick_guide_channel_prio.png)

## 4. Add and configure Webhooks

[**Webhooks**](doc:conversation#webhook) are callbacks triggered by specific events. When adding a Webhook you are prompted to specify a Target URL and events that should trigger a call to the specified URL.

> 📘 Note
>
> The Secret Token can be used to sign the content of Conversation API callbacks. This way you can verify the integrity of the callbacks coming from Conversation API.

![Webhooks](images/dashboard/dashboard_quick_guide_webhooks.png)

## 5. Project ID and Access keys

Your **Project ID** is used to group your Conversation API resources. You can find it on the Apps page under *"Project"*.

**Access keys** are required for authentication purposes when using the Conversation API.

You can generate an access key under Settings -> Access Keys page by clicking on *"New Key"*.

![Access Keys](images/dashboard/dashboard_access_keys.png)

A **client_id** and **client_secret** will be provided when creating an Access Key in the portal. The secret is only shown after generation, copy and store it in a safe place. 

You can obtain an Access Token to Conversation API by using your client_id and client_secret in the appropriate region:

EU region:

```
curl https://us.auth.sinch.com/oauth2/token -d grant_type=client_credentials --user <client_id>:<client_secret>
```

US region:

```
curl https://eu.auth.sinch.com/oauth2/token -d grant_type=client_credentials --user <client_id>:<client_secret>
```

The access token can be used in conjunction with your project ID to interact with the Conversation API. Read more about possible authentication methods at [**Authentication**](doc:conversation#authentication).

