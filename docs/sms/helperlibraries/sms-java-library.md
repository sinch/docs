---
title: Install Sinch SMS Java Library 
excerpt: >-
  The Sinch SMS Java SDK help you interact with the SMS API from your Java Application. This guide helps you set up SMS SDK in your application.
hidden: false
---

## Installing with Gradle 
In **Gradle**, please put the lines below in your **build.gradle**

```javascript
implementation 'com.sinch:sdk-sms:1.0.3'
```

Because Sinch SDK Library is hosted on Maven Central Repository, please make sure you have **mavenCentral()** in your **build.gradle**.

```javascript
repositories {
    mavenCentral()
}
```

## Installing with Maven

Using Maven/Gradle is the recommended way to install the SDK. You can add this sdk to your existing project.
In **Maven**, please put the lines below in your **pom.xml**

```javascript
<dependency>
  <dependency>
      <groupId>com.sinch</groupId>
      <artifactId>sdk-sms</artifactId>
      <version>1.0.3</version>
  </dependency>
```

## Using without a build automation tool

While we recommend using a package manager to track the dependencies in your application, it is possible to download and use the Java SDK by [downloading a pre-built jar file](https://repo1.maven.org/maven2/com/sinch/sdk-sms/). Select the directory for the latest version and download one of these jar files:

- sdk-sms-{version}-jar-with-dependencies.jar  
- sdk-sms-{version}.jar


## Importing jar with Intellij

Follow this step

```
File -> Project Structure -> Modules -> Plus Sign -> Browse the SDK SMS Jar.
```

## Importing jar with Eclipse

Follow this step

```
Project -> Build Path -> Configure Build Path -> Libraries -> Add Jar.
```
[Send and recieve SMS with Java](https://developers.sinch.com/docs/sms-java-library)
