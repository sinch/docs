---
title: Receive SMS delivery reports in a Spring Boot application
excerpt: >-
  In this more advanced installment of our Java tutorial series, you'll learn
  how to keep track of the delivery of SMS messages you send via the Sinch
  REST API.
---
The Sinch REST API keeps track of the delivery status of each message you send and makes that status available to your application in two ways:

 1) Via [polling / pull](doc:sms-guide#retrieve-a-delivery-report): Your application can fetch the delivery notifications at regular intervals with GET HTTP requests.
 2) Via [callbacks / push](doc:sms-guide#delivery-report-callback): The Sinch REST API will make an HTTP POST request to your application with details of the delivery report included in the request body.
 
## How to setup an endpoint in your Spring Boot application to respond to the delivery reports callbacks

## Prerequisites

Before starting, please make sure that:

 - You have [created your Sinch account].(https://www.sinch.com/sign-up/)
 - The right version of the JDK is installed on your computer.
 - You have added the Sinch Java SDK JAR to your project [Java Getting Started page](doc:sms-java-library).



## Create a Spring Boot Project

> **Note**
> 
> You can [skip these steps](#add-a-delivery-report-callback-controller) if you already have a functional Spring Boot project. This tutorial assumes the use of Spring Boot 2.1+, although it might work with earlier version.

### Bootstrap An Empty Application Using Spring Initializr

The Spring project provides the [_Initializr_](https://start.spring.io/) tool to quickly get up and running with a Spring Boot application. [Fill out the form](https://start.spring.io/) and download the resulting ZIP archive, extract it where you want to work, and you're good to go!

Once you've extracted the ZIP in a suitable location, open a terminal and run this command in the project directory:

    $ ./gradlew bootRun
    
This command should install Gradle and then compile, package and run your new Spring Boot application.

### Add Dependencies to the Gradle Build File

In the `build.gradle` file, make sure your dependencies include both `spring-boot-starter-web` and the Sinch Java SDK:

```java
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'

    implementation 'com.sinch:sdk-sms:1.0.3'
}
```

## Add a Delivery Report Callback Controller

To notify your application of delivery events, the Sinch REST API will send you POST HTTP requests with the details of each delivery report. You need to add a new endpoint to your application that will parse the HTTP POST body and make it available to your application for processing. 

The Sinch Java SDK already contains classes with Jackson annotations that support unmarshalling from JSON payloads out-of-the-box! Creating a Sinch callback endpoint in your Spring application is just a matter of declaring a new controller method with a `BatchDeliveryReport` parameter extracted from the body: 

```java
@RestController
public class DeliveryReportController {

    @PostMapping(path = "/sms/deliveryReport")
    public ResponseEntity receiveReport(@RequestBody BatchDeliveryReport report) {

        System.out.println("Received delivery report: " + report);

        // ... process the delivery report ...

        return ResponseEntity.ok().build();
    }
}
```

## Allow the Sinch REST API to Reach Your Application

Now that you have a POST endpoint to respond to Sinch callbacks, how can you test that it works? You could deploy your application to a publicly-exposed Web server (i.e. on AWS, Google Cloud, Heroku, Digital Ocean, etc.), but that would be too much work and completely out of scope for this tutorial. 

[Ngrok](https://ngrok.com/) to the rescue! Ngrok is a free lightweight application you install on your computer that automatically creates a tunnel to expose one of your local network ports to the Internet without the need to configure anything on your router. 

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
     
In another terminal window, run this cURL command, _replacing the URL with the forwarding URL that Ngrok had assigned to your tunnel earlier_:

     $ curl https://9d62bf5a.ngrok.io/sms/deliveryReport

If everything is working, you should see a similar response from your application:

```json
{"timestamp":"2019-08-14T15:03:28.156+0000","status":405,"error":"Method Not Allowed","message":"Request method 'GET' not supported","path":"/sms/deliveryReport"}
```

Receiving a `405 Method Not Allowed` response is normal. We've attempted to make a `GET` request with cURL, whereas your application expects a `POST`. The important part is that you reached your application via the public URL (`https://`) via Ngrok. 

Your locally-running application is now exposed to the Internet! 

## Send a Test SMS With Your Publicly-Exposed Callback

To complete your experiment, you can now send a test SMS requesting a delivery report while specifying your publicly-exposed URL endpoint as the callback. 

With both **Ngrok** and your **Spring Boot application** running, run the following cURL command.

```shell
curl -X POST \
           -H "Authorization: Bearer {API_TOKEN}" \
           -H "Content-Type: application/json"  -d '
            {
                    "from": "ignored",
                    "to": [
                            "{PHONE_NUMBER}"
                    ],
                    "delivery_report": "summary",
                    "callback_url": "https://9d62bf5a.ngrok.io/sms/deliveryReport",
                    "body": "Hello from Sinch!"
            }' \
    "https://api.clxcommunications.com/xms/v1/{SERVICE_PLAN_ID}/batches"
```

Don't forget to put the correct values in place of the `{API_TOKEN}`, `{SERVICE_PLAN_ID}`, `{PHONE_NUMBER}` placeholders. The `callback_url` should match the URL that has been assigned to your tunnel by Ngrok earlier. 

After a few seconds, if everything works, you should see a similar line output to the console of your Spring Boot application:

```
Received delivery report: BatchDeliveryReport{batchId=lLyJpEI8W8hjFH9L, totalMessageCount=1, statuses=[Status{code=0, status=DeliveryStatus{status=Delivered}, count=1, recipients=[]}]}
```

This confirms that your application can successfully accept delivery reports sent by the Sinch REST API. 

## Wrap Up

This tutorial has shown you how to create a POST endpoint that can parse delivery report callbacks sent by the Sinch REST API. In addition, you've learned how to use Ngrok to expose a locally-running Web application and use that to your advantage to do a full round-trip test of delivery reports.

A fully functional Ngrok + Spring Boot project has been [published to GitHub](https://github.com/sinch/tutorial-java-sms-delivery-reports) to quickly get you up and running testing Sinch delivery reports with your REST API account. You're free to try it out!

