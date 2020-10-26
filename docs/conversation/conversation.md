---
title: Introduction
excerpt: >-
  High level presentation of Sinch Conversation API and overview of its key concepts.
hidden: false
---

## Introduction <span class="betabadge">Beta</span>

The Sinch Conversation API:

* Provides a single endpoint to send and receive messages across most popular channels using one unified format.

* Offers advanced messaging use cases for messaging application developers that include:

  * Conversations
  * Contacts
  * Switching between bot and human chats

* Provides various message channel (bearer) features and functions supported through built-in transcoding. 


### Benefits

Using the Sinch Conversation API, you are always in control of your customer conversations over SMS, RCS, WhatsApp, or Facebook Messenger. The API is scheduled to support additional channels in the future.

Developers can use a global message template that is automatically transcoded to each message channel format, or specify an exact layout for the desired channel. The advantages of this approach are:

* Developers only have to become familiar with one Messaging API.

* Can enable conversations across all supported channels.

* Maintain full control over channel-specific features as needed. 

* Regardless of the conversation channel the customer uses (or switches between), a single callback contains all aspects of the conversation for easy integration into the Sinch portfolio of services, or any third-party platform.

<Note>

**Note:** The Sinch Conversation API is in closed beta. If you are interested in the early access program, please contact a [Sinch representative](https://www.sinch.com/contact-us/).

</Note>


### Key concepts

This diagram represents key concepts of the Sinch Conversation API.

![ER Diagram](images/convapi-er-diagram.png)


#### Project

The **project** entity:

* Is the root from the Conversation API's point-of-view.

* Groups all of the Conversation API resources and acts as a container for those resources.

* Is associated with a valid Sinch account. Refer to the [Sinch Portal](https://dashboard.sinch.com) for information about creating an account.


#### App

The **app** entity:

* Corresponds to the API user.

* Is associated with a set of [**channel credentials**](doc:conversation#channel-credential) for each underlying connected channel. 

* Includes a list of [**conversations**](doc:conversation#conversation) between itself and different [**contacts**](doc:conversation#contact) which share the same [**project**](doc:conversation#project).

* Tied to a set of webhooks that define the destination for various events sent from the Conversation API.

* Is created and configured through the [Sinch Portal](https://dashboard.sinch.com/convapi/apps).

* Contains the following configurable properties:

| Field                             | Description                                                                                                                        |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| Display name                      | The name visible in [Sinch Portal](https://dashboard.sinch.com/convapi/apps).                                                       |
| Conversation metadata report view | Specifies the amount of [**conversation**](doc:conversation#conversation) metadata that will be returned as part of each callback. |


#### Channel credential

A **channel credential**:

* Is used to authenticate against an underlying connected channel. 

* Is tied to one [**app**](doc:conversation#app).

* _Must_ be in a specific order to register an app. 

* Defines the channel priority order on the app level. For example, which channels to send first.

  <Note>
  
  **Note:** The app channel priority is overridden by contact channel priority order and by message specific channel priority order.
  
  </Note>

* Contains the following configurable properties:

| Field           | Description                                                                                          |
| --------------- | ---------------------------------------------------------------------------------------------------- |
| Channel         | Channel the specified credentials use.                                                       |
| Credential      | Specifies the type and values for the credentials used for a channel.                                |
| Channel senders | List of sender identities used when a message is sent. For example, identities used with the `SMS` channel.|


#### Webhook

A **webhook**:

* Is a POST-capable URL destination for receiving callbacks from the Conversation API.

* Includes a set of triggers that dictate what events are sent to the webhook.

* Contains the following configurable properties:

| Field | Description |
| --- | --- |
| Target | Destination (target) URL where events are sent. 
| Target type | Type of the target URL. Supported values are HTTP and DISMISS (indicates events will not be sent). |
| Secret | Optional information (secret) used to sign the content of Conversation API callbacks. Can be used to verify the integrity of the callbacks. |
| Triggers | A set of triggers the webhook is listening to. Example triggers include MESSAGE_DELIVERY for message delivery receipts and MESSAGE_INBOUND for inbound contact messages. |


##### Validating callbacks

Callbacks triggered by a registered webhook with a secret set contain the following headers:

| Field       | Description                                                                                                                                                                   |
| --- | --- |
| x-sinch-webhook-signature-timestamp | UTC timestamp of when the signature was computed. |
| x-sinch-webhook-signature-nonce | Unique nonce used to protect against reply attacks. |  
| x-sinch-webhook-signature-algorithm | HMAC algorithm used to compute the signature. |
| x-sinch-webhook-signature | Signature computed by the Conversation API. The raw HTTP body, timestamp, nonce are signed by the signature. Specifically, the signature is computed as HMAC(secret, raw callback body \|\| . \|\| x-sinch-webhook-signature-nonce \|\| . \|\| x-sinch-webhook-signature-timestamp). The inputs to the HMAC function need to be encoded in UTF-8. |                                                      |

The receiver of signed callbacks needs to perform the following steps:

1. Compute a signature (as described above).
2. Compare the computed signature with the signature included in the headers. Discard the callback if the signatures do not match.
3. Verify the timestamp is within an acceptable time window, which is a small difference between the current UTC time and the timestamp in the headers.
4. Verify the nonce is unique. To accomplish this, the best practice is to keep track of previously-received nonces.


#### Contact

The **contact** entity is:

* A collection entity that groups together underlying connected **channel recipient identities**. 

* Tied to a specific [**project**](doc:conversation#project).

* Considered public to all [**apps**](doc:conversation#app) sharing the same [**project**](doc:conversation#project).

* Contains the following configurable properties:

| Field              | Description                                                                                                                                         |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| Channel identities | List of channel identities specifying how the contact is identified on underlying channels.                                                          |
| Channel priority   | Specifies the channel priority order used when sending messages to this contact. This can be overridden by message-specific channel priority order. |
| Display name       | Optional display name used in chat windows and other UIs.                                                                                            |
| Email              | Optional Email of the contact.                                                                                                                       |
| External id        | Optional identifier of the contact in external systems.                                                                                              |
| Metadata           | Optional metadata associated with the contact.                                                                                                      |


#### Channel recipient identity

A **channel recipient identity**:

* Is an identifier for the [**contact**](doc:conversation#contact) for a specific channel. Examples include:
    * An international phone number, which is used as identifier for _SMS_ and _RCS_.
    * A Page-Scoped ID (PSID), which is used as the identifier for _Facebook Messenger_.
    
    <Note>
  
    **Note:** Some channels use app-scoped channel identity. Currently, FaceBook Messenger and Viber are using app-scoped channel identities, which means contacts will have different channel identities for different [**apps**](doc:conversation#app). For Facebook Messenger, the contact channel identity is associated with the [**app**](doc:conversation#app) linked to the FaceBook page for which this PSID is issued.

* Contains the following configurable properties:

| Field | Description |
| --- | --- |
| Channel | Channel on which this identity is used. |
| Channel recipient identity | Actual identity. For example, an international phone number or email address. |
| App id | Identifier of an app-scoped channel for the Conversation API. |


#### Conversation

A conversation is:

* A collection entity that groups several **conversation messages**. 

* Tied to one specific [**app**](doc:conversation#app) and one specific [**contact**](doc:conversation#contact).


#### Conversation message

An individual message that is part of a specific [**conversation**](doc:conversation#conversation).


#### Metadata

Metadata is an opaque field for the Conversation API and can be used by the API clients to retrieve a context when receiving a callback from the API. Metadata fields are currently restricted to 1024 characters.

The following entities hold metadata:

* [**message**](doc:conversation#conversation-message)

* [**conversation**](doc:conversation#conversation)

* [**contact**](doc:conversation#contact)


### Supported channels

The following channels are supported:

* <img src="https://files.readme.io/d0223ff-messages-chat-keynote-icon.svg" width="20" height="20" /> SMS

* <img src="https://files.readme.io/7474132-whatsapp.svg" width="20" height="20" /> WhatsApp

* <img src="https://files.readme.io/d0223ff-messages-chat-keynote-icon.svg" width="20" height="20" /> RCS

* <img src="https://files.readme.io/41a20d1-messenger.svg" width="20" height="20" /> Facebook messenger

* <img src="https://files.readme.io/8d98aa3-Viber-02.svg" width="20" height="20" /> Viber Business Messages

* <img src="https://files.readme.io/8d98aa3-Viber-02.svg" width="20" height="20" /> Viber Bot

<Note>
  
**Note:** Additional channels are scheduled for future releases.

</Note>


### Regions

The Sinch Conversation API is available in the following regions:

| Region | Host                                  |
| ------ | ------------------------------------- |
| US     | https://us.conversation.api.sinch.com |
| EU     | https://eu.conversation.api.sinch.com |


### Authentication

The Conversation API uses OAuth2 **Access Tokens** to authenticate API calls. 

To obtain an access token, access the [Sinch portal](https://dashboard.sinch.com/settings/access-keys) and select **Settings > Access Keys > Create an Access Key**. The application generates a **client_id** and **client_secret**. 

<Note>
 
**Note:** The project ID is also displayed on the Access Key portal page.

</Note>

The **Access Key** generated can be used in the authentication flows in both regions. The following snippet illustrates how to obtain an **Access Token** that can be used to authenticate towards the Conversation API in the US. 

<Note>

**Note:** You cannot use the **Access Token** obtained in this example to authenticate the Conversation API in the EU. You must obtain a valid **Access Token** for the corresponding EU endpoint.

</Note>

<CodeBlock>

```console
curl https://us.auth.sinch.com/oauth2/token -d grant_type=client_credentials --user <client_id>:<client_secret>
```

</CodeBlock>


To perform a call to the Conversation API in the US, include the **Access Token** obtained for the US in the request header. For example:

<CodeBlock>
  
```console
curl -H "Authorization: Bearer <access token>" https://us.conversation.api.sinch.com/v1beta/projects/<Project ID>/apps
```

</CodeBlock>


#### Support for Basic Authentication

Basic Authentication can also be used for the Conversation API. However, it is strongly recommended that OAuth2 (described above) be used for increased security and throughput. 

In basic authentication, the **username** and **password** correspond to the **client_id** and **client_secret** obtained when creating an **Access Key**. The following snippet illustrates how to obtain use basic authentication to obtain an **Access Token** that can be used to authenticate towards the Conversation API in the US. 

<Note>

**Note:** You _can_ use the **Access Token** obtained in this example to authenticate the Conversation API in the EU. 

</Note>

<CodeBlock>

```console
curl https://us.conversation.api.sinch.com/v1beta/projects/<Project ID>/apps --user <client_id>:<client_secret>
```

</CodeBlock>


### Postman collection

Sinch offers a [Postman collection](https://www.getpostman.com/collections/79a07a7d299afe46658b) for easy setup and testing during development.

1. Import the collection and enter values in the following variables:

  * PROJECT - your PROJECT ID
  * APP - app id
  * CLIENT_ID - your CLIENT_ID
  * CLIENT_SECRET - your client secret
  * WEBHOOK_URL - access [the webhook site](https://webhook.site/) and enter the generated link in the **Your unique URL** section.
  
2. Obtain the values for the other variables by calling corresponding requests:

  * CONTACT - ID of contact created by calling 'Create contact' request.
  * WEBHOOK_ID - ID of webhook created by calling 'Create webhook' request.
  * CONVERSATION - a Conversation is created automatically when sending a new message. For example, with a 'Text Message' request. Send a message, then call 'List conversations of App/Contact' to get the ID of conversation for this variable.


### Errors

When requests are erroneous, the Sinch Conversation API responds with standard HTTP status codes, such as `4xx` for client errors and `5xx` for server errors. All responses include a JSON body of the form:

<CodeBlock>
  
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

</CodeBlock>


The following table describes the fields of the error object:

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
