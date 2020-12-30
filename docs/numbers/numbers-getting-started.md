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


## Authenticate the API

[Log in](https://dashboard.sinch.com/login) to your self-service account, go to **Settings** > **Access Keys** and click **NEW KEY**. This will generate the Key ID (client_id) & Key Secret (client_secret). Then give your key a Display Name.

![access_keys](https://user-images.githubusercontent.com/76005934/103384649-2a158c00-4ac5-11eb-8fd5-adda1a1ca50e.png)

Make sure you copy or remember your Secret Key as this information will not be retrievable once you click **CONFIRM**.

Now that you have your client_id and client_secret, you can authenticate the Numbers API for any project using either Basic or OAuth security

**Basic security**
Our [basic security](https://developers.sinch.com/reference/#active-number) works with any project. Use the client_id and client_secret that were displayed when you created your access key through the dashboard.

**OAuth security**
[OAuth](https://developers.sinch.com/reference/#active-number) is the recommended way to access our APIs.
