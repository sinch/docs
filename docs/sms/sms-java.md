---
title: Getting started - Java
excerpt: Learn how to quickly send SMS messages with the Sinch API
---

In this guide, we show you how to:

1. Create an account and get your free test number (US only).
2. Send your first SMS.
3. Receive SMS.

A complete sample can be found on GitHub at [Send and Recieve SMS with Java and Spring Boot](https://github.com/sinch/sms-java-sample)

### Sign up for a Sinch account

Before you can send your first SMS, you need a [Sinch
account](https://dashboard.sinch.com/signup). If you are in the United States, you also need a [free test phone number](https://dashboard.sinch.com/numbers/your-numbers/numbers).

![Image of configure number](images\new-number\activateyournumber.png)
Click activate.

![Image of configure number](images\new-number\select-rest.png)
To use the number with the rest API select REST and click **GET FREE TEST NUMBER**.

### Installing Java helper library

To use java, [install our Java library](doc:sms-java-library)

### Send SMS

```java Java
 private static final String[] RECIPIENTS = {"1232323131", "3213123"};
 try (ApiConnection conn =
        ApiConnection.builder().servicePlanId(SERVICE_PLAN_ID).token(TOKEN).start()) {
      // Sending a simple Text Message
      MtBatchTextSmsResult batch =
          conn.createBatch(
              SinchSMSApi.batchTextSms()
                  .sender("{your free test number}")
                  .addRecipient(RECIPIENTS)
                  .body("This is a test message!")
                  .build());

      System.out.println("Successfully sent batch " + batch.id());
    } catch (Exception e) {
      System.out.println("Batch send failed: " + e.getMessage());
    }
```

#### Replace the token values

Before you can execute the code that sends an SMS message, you need to replace the following values with your values:

`{service_plan_id}`
`{your token}`
`{your free test number}`
`{To number}`

To find the service plan and token, go to [Dashboard](https://dashboard.sinch.com/sms/api/rest), log in and click “Show” to reveal your API token.

![Screen shot of dashboard](images\sms-callback-url.png)

To find the From-number, click the service plan id link and scroll to the bottom of the page. Then, change the `{To number}` to your phone number.

Click [here](https://developers.sinch.com/reference/#sendsms) to read more about the batches endpoint.

### Handle incoming SMS with Java Spring boot

When a customer sends an sms to your number you can configure your application to recieve a post to a webhoo. For this guide you will use [Spring Boot](https://spring.io/projects/spring-boot). You can use any framework you want that can expose an API endpoint you wish. If you are new to spring boot and want to try it out here is a good [Quickstart](https://spring.io/quickstart).

Below is the spring controller, in DemoApplication.java if you are using the [Quickstart repo](https://github.com/sinch/sms-java-sample)

```java
@RestController
public class InboundController {
    @PostMapping("/sms/incoming")
    public ResponseEntity<Object> receiveInbound(@RequestBody Object body) {
        System.out.println(body);
        return new ResponseEntity<>("Accepted", HttpStatus.OK);
    }
}
```

Before you can handle incoming traffic to your local server, you need to open up a tunnel to your local server. For that, you can use an [ngrok](https://ngrok.com/) tunnel. Open a terminal/command prompt and type: `ngrok http 3000`.

![ngrok request](images\ngrok.png)

### Configure the Callback URL for your SMS service

Before you implement the server to handle incoming SMS, you need to configure your SMS service to handle callbacks. Head over to [Dashboard](https://dashboard.sinch.com/sms/api/rest), click your service and you will see a section like the below.

![Screen shot of dashboard](images\sms-callback-url.png)

1. In the terminal windows, start running the SpringBoot Application with `./gradlew bootRun` if gradle or `./mvnw spring-boot:run` if Maven
2. Send an SMS to your Sinch Number.

You will now see the request come in

![requestbin request](images\noderesponse.png)

Read more about all the different endpoints in the [API reference guide](https://developers.sinch.com/reference)
