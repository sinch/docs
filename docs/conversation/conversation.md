---
title: Introduction
excerpt: The Sinch Conversation API is currently in closed beta and additional channels will be supported as they become popular. If you are interested in the early access program, contact a [Sinch representative](https://www.sinch.com/contact-us/).

hidden: false
next:
  pages:
    - conversation-getting-started
    - conversation-channel-support
    - conversation-callbacks
    - conversation-templates
    - conversation-optin
    - conversation-capability
---

Send and receive messages across the US and Europe using SMS, RCS, WhatsApp, Facebook messenger and other popular channels with ease and confidence when you use the Sinch Conversation API.

With built-in transcoding, the Sinch Conversation API endpoint gives you the power of conversation across all supported channels and, if required, full control over channel specific features. Additionally, a single callback contains all aspects of the conversation for easy integration into the Sinch portfolio of services, or any third-party platform.

### Authentication

The Conversation API supports OAuth, which is recommended, and Basic authentication. Both methods use client_id and client_secret to authenticate requests. You can view and manage your API keys [here](https://dashboard.sinch.com/settings/access-keys).

To get an OAuth token, you will post your client_id and secret to the Sinch endpoint. The example below shows an authentication towards the Conversation API in the US. To obtain a valid Access Token from the corresponding EU endpoint, you should use https://eu.auth.sinch.com/oauth2/token.

```console
curl https://us.auth.sinch.com/oauth2/token -d grant_type=client_credentials --user <client_id>:<client_secret>
```

A call to the Conversation API, in the US, can then be done by including the obtained **Access Token**, valid for US, in the request header. See below for an example:

```console
curl -H "Authorization: Bearer <access token>" https://us.conversation.api.sinch.com/v1beta/projects/<Project ID>/apps
```
To use Basic authentication, use your client_id as the basic auth username and your secret as the password. Basic authentication towards the Conversation API in the EU is also possible since the created Access Key is valid for the EU region as well.

```console
curl https://us.conversation.api.sinch.com/v1beta/projects/<Project ID>/apps --user <client_id>:<client_secret>
```
---

### Supported channels

- <img src="https://files.readme.io/d0223ff-messages-chat-keynote-icon.svg" width="20" height="20" /> SMS
- <img src="https://files.readme.io/7474132-whatsapp.svg" width="20" height="20" /> WhatsApp
- <img src="https://files.readme.io/d0223ff-messages-chat-keynote-icon.svg" width="20" height="20" /> RCS
- <img src="https://files.readme.io/41a20d1-messenger.svg" width="20" height="20" /> Facebook messenger
- <img src="https://files.readme.io/8d98aa3-Viber-02.svg" width="20" height="20" /> Viber Business Messages
- <img src="https://files.readme.io/8d98aa3-Viber-02.svg" width="20" height="20" /> Viber Bot

Read more about them at [**Channel Support**](doc:conversation-channel-support).

And more on the roadmap for 2021!

---

### Regions

Currently Sinch Conversation API is available in:

| Region | Host                                  |
| ------ | ------------------------------------- |
| US     | https://us.conversation.api.sinch.com |
| EU     | https://eu.conversation.api.sinch.com |

---

### Authentication

The Conversation API uses OAuth2 **Access Tokens** to authenticate API calls. The first step to obtaining an **Access Token** is to create an **Access Key** in the [Sinch portal](https://dashboard.sinch.com/settings/access-keys) under Settings -> Access Keys. A **client_id** and **client_secret** will be provided when creating an **Access Key** in the portal. The **project** ID will also be visible on the **Access Key** page in the portal. The created **Access Key** can be used in the different authentication flows in both regions. The following snippet illustrates how to obtain an **Access Token** that can be used to authenticate towards the Conversation API in the US.

```console
curl https://us.auth.sinch.com/oauth2/token -d grant_type=client_credentials --user <client_id>:<client_secret>
```

> 📘 Note
>
> It is not possible to use the **Access Token** obtained from the US endpoint to authenticate towards the Conversation API in the EU. One should instead obtain a valid **Access Token** from the corresponding EU endpoint:
> https://eu.auth.sinch.com/oauth2/token

A call to the Conversation API, in the US, can then be done by including the obtained **Access Token**, valid for US, in the request header. See below for an example:

```console
curl -H "Authorization: Bearer <access token>" https://us.conversation.api.sinch.com/v1beta/projects/<Project ID>/apps
```

#### Support for Basic Authentication

It is also possible to use Basic Authentication to authenticate towards the Conversation API. The recommendation is to use the OAuth2 flow, as described above, for increased security and throughput. The **username** and **password** correspond to the **client_id** and **client_secret** obtained when creating an **Access Key**. See below for an example of how to authenticate towards the Conversation API, in the US, using Basic Authentication. It is possible to authenticate towards the Conversation API, in the EU, in the same way since the created **Access Key** is valid for the EU region as well.

```console
curl https://us.conversation.api.sinch.com/v1beta/projects/<Project ID>/apps --user <client_id>:<client_secret>
```

---

### Postman collection

Sinch offers a Postman collection for easy setup and testing during development:

https://www.getpostman.com/collections/e45df225fff72b386813

After importing the collection, fill in the following variables:

* `PROJECT` with your Project ID

* `APP` with your App ID

* `CLIENT_ID` with your client ID

* `CLIENT_SECRET` with your client secret.  

> For testing purposes fill WEBHOOK_URL by simply visiting https://webhook.site/  
> and use the generated link - the one under the 'Your unique URL' label.

Values for other variables can be obtained by calling corresponding requests:

* `CONTACT` - ID of contact created by calling 'Create contact' request

* `WEBHOOK_ID` - ID of webhook created by calling 'Create webhook' request

* `CONVERSATION` - a Conversation is created automatically when sending a new message (for example with 'Text Message' request). Send a message, then call 'List conversations of App/Contact' to get the ID of conversation for this variable.

---

### Errors

When requests are erroneous, the Sinch Conversation API will respond with standard HTTP status codes, such as `4xx` for client errors and `5xx` for server errors. All responses include a JSON body of the form:

```json
{
  "error": {
    "code": 401,
    "message": "Request had invalid credentials.",
    "status": "UNAUTHENTICATED",
    "details": [{
      "@type": "type.googleapis.com/google.rpc.RetryInfo",
      ...
    }]
  }
}
```

The table below describes the fields of the error object:

| Name    | Description                         | JSON Type        |
| ------- | ----------------------------------- | ---------------- |
| Code    | HTTP status code                    | Number           |
| Message | A developer-facing error message    | String           |
| Status  | Response status name                | String           |
| Details | List of detailed error descriptions | Array of objects |

#### Common error responses

| Status | Description                                                            |
| ------ | ---------------------------------------------------------------------- |
| 400    | Malformed request                                                      |
| 401    | Incorrect credentials                                                  |
| 403    | Correct credentials but you dont have access to the requested resource |
| 500    | Something went wrong on our end, try again with exponential back-off   |
| 503    | Something went wrong on our end, try again with exponential back-off   |
