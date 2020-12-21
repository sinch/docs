---
title: Send an SMS to one or more ad-hoc recipients
excerpt: >-
  In this tutorial, you'll learn the basics of sending an SMS from a Java
  application.
---
This tutorial will walk you through the steps to send an SMS using Sinch's Java SDK from a server or command line application.

## Prerequisites

Before starting, please make sure that:

 - You have created your Sinch account
 - The right version of the JDK is installed on your computer
 - You have added the Sinch Java SDK JAR to your project [Java Getting Started page](doc:sms-java-library).



## Send an SMS with Java

### Create a connection to the REST API

The SDK requires you to first create an `ApiConnection` object that you will re-use for interacting with the API for the entire duration your application is running. This `ApiConnection` instance should be created once and closed when your application is shut down. It is not recommended and not efficient to create an `ApiConnection` instance for every interaction with the API.

An instance of the `ApiConnection` object can only be created using the `ApiConnection.builder()` method and its associated builder:

```java
import com.clxcommunications.xms.ApiConnection;

ApiConnection connection = ApiConnection.builder()
        .servicePlanId("{YOUR_SERVICE_PLAN_ID}")
        .token("{YOUR_API_TOKEN}")
        .start();
```

### Send an SMS to one or multiple recipients

Once an `ApiConnection` object is created and started, you can use it to interact with the API, such as sending an SMS message to one recipient:

```java
import com.sinch.xms.api;
String SERVICE_PLAN_ID = "{YOUR_SERVICE_PLAN_ID}";
String TOKEN = "{YOUR_TOKEN}";
String SENDER = "{yourSinchNumber}";
String [] RECIPIENTS = {"{yourphonenumber}"}; //your number in international format example 15551231212
ApiConnection conn =
  ApiConnection.builder()
    .servicePlanId(SERVICE_PLAN_ID)
    .token(TOKEN)
MtBatchTextSmsResult batch = 
 conn.createBatch(
    SinchSMSApi.batchTextSms()
      .sender(SENDER)
      .addRecipient(RECIPIENTS)
      .body("This is a test message from your Sinch account")
      .build());
```
> note you need to enter yoru phonenumber in international format.

### Close the connection to the REST API when your application shuts down

When you're done using the `ApiConnection` or when your application is shutting down, you should stop the `ApiConnection` that was created to interact with the REST API:

```java
connection.stop();
```

This is to ensure all resources created by the SDK such as the thread pool are stopped and freed. 

There are multiple ways you can ensure the `ApiConnection` is closed:

 - **_try/finally_ statement**
   
   If you're building a simple command line application, wrapping the code that interacts with the API in a _try/finally_ block will ensure the connection is always closed:
   
``` java 
ApiConnection connection = ApiConnection.builder()
        .servicePlanId("{YOUR_SERVICE_PLAN_ID}")
        .token("{YOUR_API_TOKEN}")
        .start();

try {
    // interact with the `connection` instance
} finally {
    connection.close();
}
```

 - **_try-with-resources_ statement (Java 8+)**
  
   Because the `ApiConnection` implements the `Closeable` interface, it can be used in a try-with-resources statement as a simpler alternative to try/finally:
   
``` java 
ApiConnection connection = ApiConnection.builder()
        .servicePlanId("{YOUR_SERVICE_PLAN_ID}")
        .token("{YOUR_API_TOKEN}")
        .start();

try (connection) {
    // interact with the `connection` instance
}
```  
 
 - **JVM Shutdown Hook**
 
   Shutdown hooks allow you to execute code when the JVM is shutting down. [Learn more about shutdown hooks](https://docs.oracle.com/javase/8/docs/api/java/lang/Runtime.html#addShutdownHook-java.lang.Thread-))
 
 - **JSR-250/330 `@PreDestroy` hooks**
 
   Dependency Injection frameworks such as Spring, Guice or Picocontainer offer specific tools to execute code when the JVM is shutting down, such as the [`@PreDestroy` annotation](https://docs.oracle.com/javase/8/docs/api/javax/annotation/PreDestroy.html)
  
## Wrap up

To wrap up, here's the complete sources of a minimal Java application that starts a connection to the Sinch REST API, sends a message then closes the connection.

```java
import com.sinch.sms.ApiConnection;
import com.sinch.sms.ApiException;
import com.sinch.sms.api;
import com.sinch.sms.api.MtBatchTextSmsResult;

import java.io.IOException;
import java.util.logging.Level;
import java.util.logging.Logger;

public class SendMessage {
    private static final Logger log = Logger.getLogger(SendMessage.class.getName());

    private static final String SERVICE_PLAN_ID = "{YOUR_SERVICE_PLAN_ID}";
    private static final String API_TOKEN = "{YOUR_API_TOKEN}";
    private static final String RECIPIENT_PHONE_NUMBER = "{YOUR_PHONE_NUMBER}";

    public static void main(String[] args) throws IOException {

        ApiConnection conn = ApiConnection.builder()
                .servicePlanId(SERVICE_PLAN_ID)
                .token(API_TOKEN)
                .start();

        try {
            MtBatchTextSmsResult result =
                    conn.createBatch(SinchSMSApi.batchTextSms()
                            .sender("ignored")
                            .addRecipient(RECIPIENT_PHONE_NUMBER)
                            .body("Hello from Sinch!")
                            .build());

            log.log(Level.INFO, "SMS sent with batch ID " + result.id());

        } catch (InterruptedException e) {
            log.log(Level.SEVERE, "Send interrupted.", e);
        } catch (ApiException e) {
            log.log(Level.SEVERE, "SMS could not be sent.", e);
        } finally {
            conn.close();  
        }
    }                
}
```

