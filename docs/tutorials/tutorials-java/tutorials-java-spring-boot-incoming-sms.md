---
title: Receive SMS in a Spring Boot application
excerpt: >-
  In this more advanced installment of our Java tutorial series, you'll learn
  how you can keep track of the delivery of SMS messages you send via the Sinch
  REST API.
---
The Sinch REST API keeps track of the delivery status of each message you send and makes that status available to your application in two ways:

 1) Via [polling / pull](doc:sms-guide#inbounds-endpoint): Your application can get all the SMS messages sent to your Sinch number.
 2) Via [callbacks / push](doc:sms-guide#inbound-message-callback): The Sinch REST API will make an HTTP POST request to your application with the message received.
 
This tutorial will show you how to setup an endpoint in your Spring Boot application to respond for the SMS callbacks.

## Prerequisites

Before starting, please make sure that:

 - You have [created your Sinch account].(https://www.sinch.com/sign-up/)
 - The right version of the JDK is installed on your computer.
 - You have added the Sinch Java SDK JAR to your project [Java Getting Started page](doc:sms-java-library).


## Create a Spring Boot Project

> **Note**
> 
> You can [skip these steps](#add-a-delivery-report-callback-controller) if you already have a functional Spring Boot project. This tutorial assumes the use of Spring Boot 2.1+, although it might work with an earlier version.

### Bootstrap An Empty Application Using Spring Initializr

The Spring project provides the [_Initializr_](https://start.spring.io/) tool to quickly get up and running with a Spring Boot application. [Fill the form](https://start.spring.io/) and download the resulting ZIP archive, extract it where you want to work and you're good to go!

Once you've extracted the ZIP in a suitable location, open a terminal and run this command in the project directory:

    $ ./gradlew bootRun
    
This command should install Gradle and then compile, package and run your new Spring Boot application.

### Add Dependencies to the Gradle Build File

In the `build.gradle` file, make sure your dependencies includes both `spring-boot-starter-web` and the Sinch Java SDK:

```java
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    implementation 'com.sinch:sdk-sms:1.0.3'

}
```

## Add a Delivery Report Callback Controller

To notify our application of incoming SMS events, the Sinch REST API will send a POST HTTP to your server. Create a Sinch callback endpoint in our Spring application with a `MoSms` parameter extracted from the body: 

```java
@RestController
public class IncomingController {

    @PostMapping(path = "/sms/incoming")
    public ResponseEntity receiveReport(@RequestBody MoSms incomingSMS) {

        System.out.println("Received sms: " + incomingSMS);

        // ... process the sms ...

        return ResponseEntity.ok().build();
    }
}
```

## Allow Sinch REST API to Reach Your Application

To test, you need to make your server reachable from the outside. One way of doing that is to use [Ngrok](https://ngrok.com/). Ngrok is a free lightweight application you install on your computer that automatically creates a tunnel to expose one of your local network ports to the Internet without the need to configure anything on your router. 

[Follow the instructions to install Ngrok](https://ngrok.com/download) on your computer, and once the `ngrok` binary is available in your path, fire up a tunnel to expose the default Spring Boot port on the Internet:

    $ ngrok http 8080
    
This should give you the following output:
    
```
ngrok by @inconshreveable                                                                                      
                                                                                                                                       
Session Status                online                                                                                               
Session Expires               7 hours, 59 minutes                                                                                  
Version                       2.3.34                                                                                               
Region                        United States (us)                                                                                   
Web Interface                 http://127.0.0.1:4040                                                                                
Forwarding                    http://9d62bf5a.ngrok.io -> http://localhost:8080                                                    
Forwarding                    https://9d62bf5a.ngrok.io -> http://localhost:8080                                                   
                                                                                                                                   
Connections                   ttl     opn     rt1     rt5     p50     p90                                                          
                              0       0       0.00    0.00    0.00    0.00
```       

Take note of the `https://...` public URL that Ngrok has assigned to your tunnel in the second line starting with `Forwarding`. In the above example, this URL is `https://9d62bf5a.ngrok.io`, but Ngrok assigns a different URL for **each tunnel** so you'll need to take note of the one specific for your tunnel. This is the URL you'll give to Sinch as the callback URL for your test message.

To test that the tunnel works, start your Spring Boot application:

     $ ./gradlew bootRun
     
In another terminal window, run this cURL command, _replacing the URL with the forwarding URL that Ngrok has assigned to your tunnel:

     $ curl https://9d62bf5a.ngrok.io/sms/deliveryReport

If everything is working, you should see a similar response from your application:

```json
{"timestamp":"2019-08-14T15:03:28.156+0000","status":405,"error":"Method Not Allowed","message":"Request method 'GET' not supported","path":"/sms/deliveryReport"}
```

Receiving a `405 Method Not Allowed` response is normal, you've attempted to make a `GET` request with cURL, whereas our application expects a `POST`. The important part is that you've reached our application via the public URL (`https://`) via Ngrok. 

Your locally-running application is now exposed to the Internet! 

## Send a Test SMS With Your Publicly-Exposed Callback

Go to your Sinch dashboard and configure the SMS callback on your service plan with the above id [serviceplan] 
(https://developers.sinch.com/docs/sms#configure-callback-url-for-your-sms-service).  


With both **Ngrok** and your **Spring Boot application** running, send an SMS to your Sinch number. 

After a few seconds, if everything works, you will see a similar line output to the console of your Spring Boot application:

```shell
Received sms report:MOSMS{}]}
```

This confirms that our application can successfully accept incoming SMS that are sent to you. 

