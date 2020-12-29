---
title: Getting started
excerpt: ''
hidden: false
next:
  pages:
    - numbers-getting-started-rent-a-number
---

To get started with the Sinch Numbers API, you will need to go through the following steps:
 1. Authenticating the API
 2. Search, rent and configure numbers in the US

## Authenticating the API

[Log in](https://dashboard.sinch.com/login) to your self-service account, go to **Settings** > **Projects** and click **ADD NEW PROJECT**. Name your project and click **CREATE**.
![image](https://user-images.githubusercontent.com/76005934/103315442-9922b000-49f3-11eb-9e97-f9e903567821.png)

Next, under **IDENTITY & ACCESS**, click **Access Keys** and click **NEW KEY**. Enter a required Display name and click **CONFIRM**. This will generate the Key ID & Key Secret.
![image](https://user-images.githubusercontent.com/76005934/103316043-421dda80-49f5-11eb-9a0d-b3950e55dcad.png)

Make sure you copy or remember your Secret Key as this information will not be retrievable once you click **CONFIRM**.

**Note**: client_id is the Key ID and client_secret is the Key Secret.

Now that you have your client_id and client_secret, you can authenticate the Numbers API for any project using our Basic security or by using our OAuth security which is the recommended way to access our APIs.

**OAuth security information**:

Security Scheme Type OAuth2

clientCredentials OAuth Flow Token URL: https://eu.auth.sinch.com/oauth2/token

Revoke URL: https://eu.auth.sinch.com/oauth2/revoke
