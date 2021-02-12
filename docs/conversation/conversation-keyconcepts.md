---
title: Key concepts and terms
excerpt: Key concepts and terms for the Sinch Conversation API
hidden: false
---

#### Project

The **project** entity belongs to a Sinch account, acts as a container and is the root entity from the Conversation API's point-of-view. All Conversation API resources are grouped under a project.

#### App

The **app** entity, which is created and configured through the [Sinch Portal](https://dashboard.sinch.com/convapi/apps), is tied to the API user and comes with a set of **channel credentials** for each underlying connected channel. The app has a list of **conversations** between itself and different **contacts** which share the same **project**.

Webhooks, which the app is attached to, defines the destination for various events coming from the Conversation API. An **app** has the following configurable properties:

| Field                             | Description                                                                                                                        |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| Display name                      | The name visible in the [Sinch Portal](https://dashboard.sinch.com/convapi/apps).                                                       |
| Conversation metadata report | Specifies the amount of **conversation**metadata that is returned as part of each callback. |
| Retention Policy                  | The retention policy specifies how long messages, sent to or from an **app**, are stored by the Conversation API.         |

##### Retention policy

Each **App** has a retention policy that specifies how long messages, sent to or from the **App**, are stored. The **retention policy** can be changed via the API by updating the corresponding **app**, or via the [Sinch Portal](https://dashboard.sinch.com/convapi/apps) by editing the corresponding **app**. A **retention policy** is defined by the following properties:

| Field                             | Description                                                                                                                           |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| Retention Policy Types             |  `MESSAGE_EXPIRE_POLICY` - This is the default policy. This option will remove all messages, sent or received by the app, that are older than the TTL days specified in the policy.                                              |
| TTL days                          | The days before a message or conversation is eligible for deletion. The allowed values are 1,3650 and the default value is 180 days.|

###### Retention policy types

* `MESSAGE_EXPIRE_POLICY` - this option will remove all messages, sent or received by the **app**, older than the TTL days specified in the policy.

* `CONVERSATION_EXPIRE_POLICY` - this option only takes the last message in a **conversation** into consideration when deciding if a **conversation** should be removed or not. The entire **conversation** will be removed if the last message is older than the TTL days specified in the policy. The entire **conversation** will be kept otherwise. 

* `PERSIST_RETENTION_POLICY` -  this option persists all messages, and **conversations** until they are explicitly deleted. Note that this option will be subject to additional charges in the future.

**Note:** This retention policy **does not** apply to [Contacts](https://developers.sinch.com/docs/conversation-keyconcepts#contact). Data management of contacts is provided through the CRUD function described here: https://developers.sinch.com/reference#contact_getcontact

Changes from the default retention policy require legal justification.  If the change is made via the dashboard, Sinch will collect the justification.  If the change is made programmatically, then the obligation is upon the client to notify Sinch of the legal justification via email to DPO@Sinch.com.

This is for your own administration, and it’s your responsibility to set legally compliant retention times. Sinch does not perform a legal review of your internal processes and personal data processing.

#### Channel credential

A **channel credential** is the authentication means used to authenticate against an underlying connected channel. A **channel credential** is tied to one **app**.

The order of the channel credentials registered for an app is significant. It defines the channel priority order on app level when defining which channels to send first.

The app channel priority is overridden by contact channel priority order and by message specific channel priority order. A **channel credential** has the following configurable properties:

| Field           | Description                                                                                                           |
| --------------- | --------------------------------------------------------------------------------------------------------------------- |
| Channel         | Which channel these credentials are used with.                                                                        |
| Credential      | Specifies the type and values for the credentials used for a channel.                                                 |
| Callback Secret | Optional. A secret for certain channels where Conversation API can validate callbacks coming from the channels.       |

#### Webhook

A **webhook** is a POST capable URL destination for receiving callbacks from the Conversation API.
Beside URL, each **webhook** includes a set of triggers which dictates which events are to be sent to the **webhook**. A **webhook** has the following configurable properties:

| Field       | Description                                                                                                                                                                                                                  |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Target      | The target URL where events should be sent to                                                                                                                                                                                |
| Target type | Type of the target URL. Currently only DISMISS and HTTP are supported values. DISMISS indicates the events will not be sent                                                                                                  |
| Secret      | Optional secret to be used to sign the content of Conversation API callbacks. Can be used to verify the integrity of the callbacks. See [Validating Callbacks](https://developers.sinch.com/docs/conversation-callbacks#validating-callbacks) for details. |
| Triggers    | A set of triggers that this webhook is listening to. Example triggers include MESSAGE_DELIVERY for message delivery receipts and MESSAGE_INBOUND for inbound contact messages                                                |

[Conversation API Callbacks](https://developers.sinch.com/docs/conversation-callbacks) provides more information about managing webhooks and the format of the callbacks.

#### Contact

The **contact** entity is a collection entity that groups together underlying connected **channel recipient identities**. It is tied to a specific **project** and is therefore considered public to all **apps** sharing the same **project**. A **contact** has the following configurable properties:

| Field              | Description                                                                                                                                         |
| ------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| Channel identities | List of channel identities specifying how the contact is identified on underlying channels                                                          |
| Channel priority   | Specifies the channel priority order used when sending messages to this contact. This can be overridden by message specific channel priority order. |
| Display name       | Optional display name used in chat windows and other UIs                                                                                            |
| Email              | Optional Email of the contact                                                                                                                       |
| External id        | Optional identifier of the contact in external systems                                                                                              |
| Metadata           | Optional metadata associated with the contact.                                                                                                      |

#### Channel recipient identity

A **channel recipient identity** is an identifier for the **contact** for a specific channel. E.g. an international phone number is used as identifier for _SMS_ and _RCS_ while a PSID (Page-Scoped ID) is used as the identifier for _Facebook Messenger_.

Some channels use app-scoped channel identity. Currently, Facebook Messenger and Viber Bot are using app-scoped channel identities, which means contacts will have different channel identities for different **apps**.

For Facebook Messenger this means that the contact channel identity is associated with the **app** linked to the Facebook page for which this PSID is issued. A **channel recipient identity** has the following configurable properties:

| Field                      | Description                                                               |
| -------------------------- | ------------------------------------------------------------------------- |
| Channel                    | The channel that this identity is used on                                 |
| Channel recipient identity | The actual identity, e.g. an international phone number or email address. |
| App id                     | The Conversation API's **app** ID if this is app-scoped channel identity. |

#### Conversation

A collection entity that groups several **conversation messages**. It is tied to one specific **app** and one specific **contact**.

#### Conversation message

An individual message, part of a specific **conversation**.

#### Metadata

There are currently three entities which can hold metadata: **message**, **conversation** and **contact**. 

The metadata is an opaque field for the Conversation API and can be used by the API clients to retrieve a context when receiving a callback from the API. Metadata fields are currently restricted to 1024 characters.
