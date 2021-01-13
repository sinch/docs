---
title: Getting started - Node
excerpt: Learn how to quickly send SMS messages with the Sinch API
---
In this guide, we show you how to:

1. Create an account and get your free test number (US only).
2. Send your first SMS.

### Sign up for a Sinch account

Before you can send your first SMS, you need a [Sinch
account](https://dashboard.sinch.com/signup). If you are in the United States, you also need a [free test phone number](https://dashboard.sinch.com/numbers/your-numbers/numbers).

### Send SMS

```nodejs NodeJS
//Create a new node app and copy this into app.js
var request = require("request");
var options = {
  method: 'POST',
  url: 'https://us.sms.api.sinch.com/xms/v1/{service_plan_id}/batches',
  headers: {accept: 'application/json', 'content-type': 'application/json',
      "Authorization": "Bearer {your token}"},
  body: '{"to":"[{To Number}]","from":"{your free test number}","body":"This is a test message"}'
};

request(options, function (error, response, body) {
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


![Screen shot of dashboard](images\sms-callback-url.png)

To find the From-number, click the service plan id link and scroll to the bottom of the page and then change the `{To number}` to your phone number.

Click [here](https://developers.sinch.com/reference/#sendsms) to read more about the batches endpoint.

## Receive SMS via web-hook  

The next step shows how to handle the sending of an SMS to your Sinch number.

### Configure the Callback URL for your SMS service

Before you implement the server to handle incoming SMS, you need to configure your SMS service to handle callbacks. Head over to https://dashboard.sinch.com/sms/api/rest, click your service and you will see a section like the below.

![Screen shot of dashboard](images\sms-callback-url.png)

If you just want to look at what we post you can use http://requestbin.net/

Click create and you will see:

![Screen shot of request bin](images\requestbin.png)

Copy the bin URL to the callback URL info and click Save.

![Screen shot of callback configured](images\callbackurlconfigured.png)

That's it! You're now ready to send an SMS to your Sinch [number](https://dashboard.sinch.com/numbers/your-numbers/numbers)

To see the data we send on incoming SMS, refresh your request bin page.

![requestbin request](images\requestbin-request.png)

### How to handle incoming SMS with Node.js

Create a new node app and paste this into app.js

```javascript
const url = require("url");
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

Before you can handle incoming traffic to your local server, you need to open up a tunnel to your local server. For that, you can use [ngrok](https://ngrok.com/) tunnel. Open a terminal/command prompt and type: `ngrok http 3000`

Copy the https address in your window, then run app.js in the command prompt 'node app.js'

![requestbin request](images\ngrok.png)

Go back to your dashboard and change the callback URL for your SMS service.

1. In the terminal windows, start the app.js `node app.js`
2. Send an SMS to your Sinch Number.
3. You will now see the request come in.

![requestbin request](images\noderesponse.png)

You can read more about all the different endpoints in the [API reference guide](https://developers.sinch.com/reference)
