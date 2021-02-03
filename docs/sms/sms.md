---
title: Getting started - node
excerpt: Learn how to quickly send SMS messages with the Sinch API
---
In this guide, we show you how to:

1. Create an account and get your free test number (US only).
2. Send your first SMS.

### Sign up for a Sinch account

Before you can send your first SMS, you need a [Sinch
account](https://dashboard.sinch.com/signup). If you are in the United States, you also need a [free test phone number](https://dashboard.sinch.com/numbers/your-numbers/numbers).

### Send SMS

If you havent already, create a new node app with npm and accept the defaults, and add the request package.

```javascript

```shell
npm init 
npm install request
```

Create index.js and paste below:

```Javascript

var request = require("request");
var messageData = {
  from: "{your free test number}",
  to: ["{To number}"],
  body: "This is a test message from your Sinch account",
};
var options = {
  method: "POST",
  url:
    "https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches",
  headers: {
    accept: "application/json",
    "content-type": "application/json",
    Authorization: "Bearer {your token}",
  },
  body: JSON.stringify(messageData),
};
request(options, function (error, response, body) {
  console.log(response.body);
  if (error) throw new Error(error);
  console.log(body);
});
```
Before you can execute the code that sends an SMS message, you need to modify it in a few places.

#### Replace the token values

Before you can execute the code that sends an SMS message, you need to replace the following values with your values:

`{service_plan_id}`
`{your token}`
`{your free test number}`
`{To number}`

To find the service plan and token, go to https://dashboard.sinch.com/sms/api/rest, log in and click “Show” to reveal your API token.

To find the From-number, click the service plan id link and scroll to the bottom of the page and then change the `{To number}` to your phone number.

Click [here](https://developers.sinch.com/reference/#sendsms) to read more about the batches endpoint.

## Receive SMS

We use webhooks to notify your application when someone sends a text to your sinch number.
To handle these you will learn how create a webserver and make it reachable on the Internet below.


### Create a HTTP server SMS with Node.js

Paste below into at bottom of your index.js:

```javascript

const http = require("http");
const server = http.createServer((req, res) => {
  let data = [];
  req.on("data", (chunk) => {
    data.push(chunk);
  });
  req.on("end", () => {
    console.log(JSON.parse(data));
  });
  res.end();
});
server.listen(3000);
```
Start the server

'''
node index.js
'''

### Open up a tunnel to your server

- Configure your callbacks on  https://dashboard.sinch.com/sms/api/rest, click your service and you fill in the Callback URL field with teh ngrok.io domain from above.

Use  to your local server, you need to open up a tunnel to your local server. For that, you can use [ngrok](https://ngrok.com/) tunnel. Open a terminal/command prompt and type: `ngrok http 3000`

- Copy the https address that ends with .ngrok.io, t
- Send a reply to sms.

You can read more about all the different endpoints in the [API reference guide](https://developers.sinch.com/reference)
