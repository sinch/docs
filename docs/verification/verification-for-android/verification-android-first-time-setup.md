---
title: First time setup
excerpt: >-
  Setup phone number verification in Android for the first time with the Sinch
  Verification SDK.
next:
  pages:
    - verification-android-the-verification-process
---
This is a step-by-step guide about setting up the Sinch Verification SDK for the first time.

## Register an Application

1.  Register a [Sinch Developer account](https://portal.sinch.com/#/signup)
2.  Set up a new Application using the Dashboard, where you can then obtain an *Application Key*.
3.  Enable Verification for the application by selecting: *Authentication* \> *Public* under *App* \> *Settings* \> *Verification*

## Add the Sinch library

The Sinch Verification SDK is available publicly on jCenter. To include it in your Android application make sure your **project** level build.gradle file contains:

```text
buildscript {
    repositories {
        google()
        jcenter()
    }
}

allprojects {
    repositories {
        google()
        jcenter()
    }
}
```

Now in your **module** level build.gradle file you can add Sinch SDK as a dependency:

```text
dependencies {
    implementation 'com.sinch.android.sdk.verification:verification-all:*.*.*'
  }
```

If your application uses all the verification methods it's easiest to add _verification-all_ package. If you intend to use only specific verification types, you may include only their dependencies.
- verification-sms
- verification-flashcall
- verification-callout
- verification-seamless

##### Example:

Your application relies only on sms verification and doesn't use any other verificaiton methods. Then you should simply add:

```text
dependencies {
    implementation 'com.sinch.android.sdk.verification:verification-sms:*.*.*'
  }
```

The latest version of the SDK can be checked on [Bintray](https://bintray.com/sinch/com.sinch.android.sdk.verification).

## Samples

A repository with fully functional samples is available on [GitHub](https://github.com/sinch/verification-samples/tree/master/Android-Verification-SDK).
