---
title: Getting started
excerpt: >-
  This guide will show you how to search, buy, list and update numbers through the self-serve portal and in the same way also through the numbers API.
hidden: false
---

## Get your first Number

In this guide, we will show you how to:

1. Create an account and buy your first number in the portal
2. Use the portal to create API credentials and how to use these to generate a token [go to Numbers API](doc:numbers-using-the-numbers-api)
3. Use the numbers API to search, buy, list and update numbers [go to Numbers API](doc:numbers-using-the-numbers-api)

### Prerequisites to use the numbers API

The numbers API are currently run as alpha testing only selected customers will have access to this feature. If you like to participate in alpha testing please reach out to Tobias.Sellberg@sinch.com.


### Authentication

#### Finding your credentials

To get the project id, client id and key id you will need to log into your self-serve account (dashboard.sinch.com) and visit https://dashboard.sinch.com/settings/project-management.

![Project management](images/project_management.png)

Then go Access Keys section https://dashboard.sinch.com/settings/access-keys. Here you need to click the “New Key” to generate an client id and key id.

![Access Keys](images/access_keys.png)

Make sure to write down the key since it will not be possible to retrieve after creation

#### Basic

Our basic security works with any project, its the client_id and client_secret that you can find in the dasbhoard.
Learn more about Sinch authentication and authorization, Security Scheme Type HTTP, HTTP Authorization Scheme basic

#### Oauth

Our basic security works with any project, its the client_id and client_secret that you can find in the dasbhoard This is the recomended way of to access our apis.

Security Scheme Type OAuth2
clientCredentials OAuth Flow Token URL: https://eu.auth.sinch.com/oauth2/token

Revoke URL: https://eu.auth.sinch.com/oauth2/revoke


