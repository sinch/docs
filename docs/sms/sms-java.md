---
title: Implementing SMS messaging in Java
excerpt: Learn how to quickly send and receive SMS messages in Java applications with the Sinch API
---

## Overview

This guide will walk you through the process of setting up a Java application to send and receieve SMS messages using the Sinch API.

### Prerequisites

- You must have JDK 11 or later installed.
- You must have [Gradle](https://gradle.org/install/) installed.
- Before you can send or receive SMS messages using the Sinch API, you first must [create a Sinch account](#CreateSinchAccount) to get a free test number. If you already have a Sinch account and free test number, skip to the section about [implementing SMS messaging](#ImplementingSMSMessaging).
- You must [install the Java SMS helper library](doc:sms-java-library).

### <a id="CreateSinchAccount"></a> Sign up for a Sinch account

Before you can send your first SMS, you need a [Sinch
account](https://dashboard.sinch.com/signup). If you are in the United States, you also need a [free test phone number](https://dashboard.sinch.com/numbers/your-numbers/numbers).

![Image of configure number](images\new-number\activateyournumber.png)
Click activate.

![Image of configure number](images\new-number\select-rest.png)
To use the number with the rest API select REST and click **GET FREE TEST NUMBER**.

## <a id="ImplementingSMSMessaging"></a> Implementing SMS Messaging

A complete sample Java application can be found on GitHub at [Send and Recieve SMS with Java and Spring Boot](https://github.com/sinch/sms-java-sample).

### Sending SMS Messages

Executing the following code in your application will send an SMS message:

```java Java
 private static final String[] RECIPIENTS = {"To number"};
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

#### Replacing the token values

Before you can execute the code that sends an SMS message, you need to replace the following values with your values:

- `{SERVICE_PLAN_ID}`
- `{TOKEN}`
- `{your free test number}`
- `{To number}`

To find the service plan ID and token, go to [Dashboard](https://dashboard.sinch.com/sms/api/rest), log in and click “Show” to reveal your API token.

![Screen shot of dashboard](images\sms-callback-url.png)

To find your free test number, click the service plan ID link and scroll to the bottom of the page. Then, change the `{To number}` to the phone number which will receive the SMS.

For more information about the batches endpoint, click [here](https://developers.sinch.com/reference/#sendsms).

### Handle incoming SMS with Java Spring Boot

When a customer sends an SMS to your number you can configure your application to receive a post to a webhook. You can use any framework you want that can expose an API endpoint.

For this guide we will use [Spring Boot](https://spring.io/projects/spring-boot). If you are new to Spring Boot and want to try it out here is a good [Quickstart](https://spring.io/quickstart).

If you are using the [Quickstart repo](https://github.com/sinch/sms-java-sample), below is the Spring controller, located in DemoApplication.java:

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

Before you can handle incoming traffic to your local server, you need to open up a tunnel to your local server. For that, you can use an [ngrok](https://ngrok.com/) tunnel. If you haven't already, install ngrok, and then open a terminal/command prompt and type: `ngrok http 3000`.

![ngrok request](images\ngrok.png)

### Configure the Callback URL for your SMS service

Before you implement the server to handle incoming SMS, you need to configure your SMS service to handle callbacks.

1. Head over to [Dashboard](https://dashboard.sinch.com/sms/api/rest), click your service and you will see a section like the image below.

![Screen shot of dashboard](images\sms-callback-url.png)

2. Under **Callback URL**, click Edit and configure a callback URL.

3. In the terminal windows, start running the SpringBoot Application with `./gradlew bootRun` if you are using Gradle or `./mvnw spring-boot:run` if you are using Maven.
4. Send an SMS to your Sinch number.

5. You will now see the request come in.

![requestbin request](images\noderesponse.png)

Read more about all the different endpoints in the [API reference guide](https://developers.sinch.com/reference).
