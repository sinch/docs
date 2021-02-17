---
title: First time setup
excerpt: >-
  Setup phone number verification on any device supporting JVM for the first time with the Sinch
  Verification SDK.
next:
  pages:
    - verification-jvm-the-verification-process
---
This is a step-by-step guide about setting up the Sinch Verification SDK for the first time.

## Register an Application

1.  Register a [Sinch Developer account](https://portal.sinch.com/#/signup)
2.  Set up a new Application using the Dashboard, where you can then obtain an *Application Key*.
3.  Enable Verification for the application by selecting: *Authentication* \> *Public* under *App* \> *Settings* \> *Verification*

## Add the Sinch library

The Sinch Verification SDK is available publicly on jCenter. Depending on the build system you use include it in your build.gradle or pom file. You can also download the jar directly from [here](https://bintray.com/sinch/com.sinch.jvm.sdk.verification/verification).

build.gradle:
```text
repositories {
    //...
    jcenter()
}
dependencies {
    implementation 'com.sinch.jvm.sdk.verification:library:*.*.*'
}
```

pom.xml:
```xml
<dependency>
  <groupId>com.sinch.jvm.sdk.verification</groupId>
  <artifactId>library</artifactId>
  <version>*.*.*</version>
  <type>pom</type>
</dependency>
```

The latest version of the SDK can be checked on [Bintray](https://bintray.com/sinch/com.sinch.jvm.sdk.verification/verification).

## Samples

A repository with fully functional samples is available on [GitHub](https://github.com/sinch/verification-samples/tree/master/JVM-Verification-SDK).
