---
title: Handle incoming messages
excerpt: |
  In this tutorial, you will learn how to handle incoming messages.
next:
  pages:
    - conversation-send-rich-messages-with-fb-messenger
hidden: false
---

In this tutorial, we use [Facebook Messenger](doc:conversation-send-a-message-with-fb-messenger). You can also use the [SMS channel](doc:conversation-send-sms) or any [other channel](doc:conversation-channel-support)

## Create a simple webhook using Node.js

To create a simple webhook using Node.js, take these steps.

  1. Create a new node app and then run the following on the command line to create the needed dependency.

**Note**: You need to use the right region api endpoint where your Conversation App resides. In this tutorial, we assume our app resides in the EU region.

  2. Install the [Express](https://www.npmjs.com/package/express) http server framework module.`npm install express body-parser --save`
  3. Add the following code to your index.js

```javascript
const express = require("express"),
  bodyParser = require("body-parser"),
  app = express().use(bodyParser.json());
port = 3000;

app.post("/webhook", (req, res) => {
  let body = req.body;
  let {
    message: {
      contact_message: {
        text_message: { text },
      },
    },
  } = body;
  console.log(text);
  res.sendStatus(200);
});

app.listen(port, () => console.log(`Listening to ${port}..`));
```

This code will allow you to listen for incoming messages and will parse out the text message content. If you want to see the payload not parsed, add `console.log(JSON.stringify(body, null, 2));`

  4. Use [ngrok](https://ngrok.com/) to open up a tunnel to your local server to handle incoming traffic. 
  5. Open a terminal/command prompt and type: `ngrok http 3000` 

The Node app and Ngrok should be running at the same time at port 3000.

![ngrok screenshot](https://i.imgur.com/HHpIHIp.png)

## Configure a webhook

To configure a webhook, take these steps.

  1. Go to your Conversation App dashboard and select **Conversations**.
  2. Select **Apps** from the drop-down menu.
  3. Select the app you want to add a webhook too. For this example, we will use **Test** as the app.

![LeftMenu](images/dashboard/dashboard_leftMenu.jpg)

  4. Select your App.
  5. Scroll down to the **Webhook** section and select **Add Webhook**. There will be a pop-up with empty required fields.

![Webhook](images/dashboard/dashboard_configPage.jpg)

  6. Select HTTP for **Target Type**.
  7. Use your ngrok url as the **Target URL**.
  8. Select `MESSAGE_INBOUND`, `MESSAGE_DELIVERY`, `EVENT_DELIVERY`, `MESSAGE_INBOUND`, `EVENT_INBOUND`, `CONVERSATION_START`, `CONVERSATION_STOP`, `UNSUPPORTED` as **Trigger**

![WebhookPopup](images/dashboard/dashboard_webhookPopup.png)

Now your webhook is setup with the Conversation App.

---

## Start a Conversation and test the webhook

Now that your webhook is setup with the Conversation App, and ngrok along with your node app is running and listening to port 3000, it's time to test the webhook. For this tutorial, we will be using Facebook Messenger channel as our example.

To start a conversation and test the webhook, take these steps.

  1. Open up Facebook Messenger and send a message to your test account. If you do not have an account, please visit [Send a message with Facebook Messenger](doc:conversation-send-a-message-with-fb-messenger) before proceeding.

![SendingMessage](images/channel-support/messenger/fb_message_firstmsg.png)

If you did everything correctly, you will receive a `status 200 OK` on ngrok and the text message on your console log.

![Logged](images/channel-support/messenger/fb_message_log.jpg)

---

## Sending a reply via Webhook

To send a reply via webhook, take these steps.
  1. Copy the following code on top of your current webhook.
  2. Re-run your node application.

```javascript
const express = require("express");
const request = require("request-promise");
const bodyParser = require("body-parser");
app = express().use(bodyParser.json());
port = 3000;

const APP_ID = "APP_ID_HERE",
  PROJECT_ID = "PROJECT_ID_HERE",
  client_id = "CLIENT_ID_HERE",
  client_secret = "CLIENT_SECRET_HERE";

/* Please note that client id and secret are essentially equivalent to a username and password. This code is for example purposes and is not meant for production.*/

const getAuthToken = () => {
  return request({
    url: "https://eu.auth.sinch.com/oauth2/token",
    method: "POST",
    auth: { user: client_id, pass: client_secret },
    form: { grant_type: "client_credentials" },
    json: true,
  });
};

const sendMessage = async (contact_id, text) => {
  const res = await getAuthToken();
  const token_type = res.token_type;
  const token = res.access_token;
  return request({
    url: `https://eu.conversation.api.sinch.com/v1beta/projects/${PROJECT_ID}/messages:send`,
    method: "POST",
    headers: {
      "Content-Type": "application/json text/plain",
      Authorization: `${token_type} ${token}`,
    },
    body: {
      app_id: APP_ID,
      recipient: {
        contact_id,
      },
      message: {
        text_message: {
          text,
        },
      },
      channel_priority_order: ["MESSENGER"],
    },
    json: true,
  });
};

app.post("/webhook", (req, res) => {
  let body = req.body;
  let {
    message: {
      contact_message: {
        text_message: { text },
      },
      contact_id,
    },
  } = body;
  if (text) {
    sendMessage(contact_id, "Hello World").then((res) => console.log(res));
  }
  console.log(`MESSAGE RECEIVED: ${text}`);
  res.sendStatus(200);
});

app.listen(port, () => console.log(`Listening to ${port}..`));
```

If everything is done correctly, you will receive a message back from your webhook on Messenger. On your terminal, you will receive data regarding the `message id` and `accepted time`

![Received](images/channel-support/messenger/fb_message_replied.png)

Now your webhook is ready to receive and send messages!

If you want to send something other than a text message on Messenger, visit our
[Sending a Rich message with Facebook Messenger](doc:conversation-send-rich-messages-with-fb-messenger) page.
