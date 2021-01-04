---
title: "Building a C# Authentication System with .NET - Part 1"
excerpt: >-
  More and more websites and apps ask for your phone number and, in many cases,
  are using it for two-factor authentication. In this tutorial, you will learn
  how to build your own C# two factor authentication system
hidden: true
---

> **Update**
>
> To verify numbers even easier, check out our [Verification SDK](https://www.sinch.com/products/verification/sms/)

More and more websites and apps ask for your phone number and, in many cases, are using it for two-factor authentication. ([Read more about 2FA here](https://www.sinch.com/opinion/what-is-two-factor-authentication/).) In this tutorial, you will learn how to build your own C# two factor authentication system in about 30 minutes, using a classic SMS verification system and using .NET for the backend. In [Part 2](doc:building-an-ios-client-for-number-verification-part-2), we will implement it in one of our clients.

The full sample code can be downloaded [here](https://github.com/sinch/net-two-factor-auth).

## Prerequisites

> 1.  A solid understanding of C\# and REST APIs
> 2.  Visual Studio 2013 or later
> 3.  A [Sinch account](https://portal.sinch.com/#/signup)
> 4.  A free [Azure](http://azure.com) account, if you want to host it there (optional)

## Create a project

Create a new WebAPI project with no user authentication and if you have an Azure account, make sure the Host in Cloud box is checked.

## Install Sinch helper library

When the project is created, we'll want to add the `Sinch.SMS` nuget package with which we need to send out the one-time password (OTP) codes. Open up Package Manager Console:

```csharp
pm>install-package Sinch.SMS
```

## Request OTP

When creating a one-time password, we will need somewhere to store them. Create a new class in the Models directory and call it **OTPCode**:

```csharp
public class OTPCode
{
    public string PhoneNumber { get; set; }
    public string Code { get; set; }
}
```

This class will contain all OTP requests. In this tutorial, we are only going to store them in memory, but in production, you would probably have them in a memcache or database connected with your user.

Create a new Empty WebAPI controller called **OTP** and add the below code:

```csharp
public class OTPController : ApiController
{
    private static List<OTPCode> OTPCodes = new List<OTPCode>();
    [HttpGet]
    public async Task<HttpResponseMessage> RequestOTP(string phoneNumber)
    {
        // this method will issue new OTP codes
    }

    public HttpResponseMessage VerifyOTP(string phoneNumber, string code)
    {
        //this method will verify code and number
    }
}
```

## Generating a random code

In this scenario, since it doesn't really matter that it's totally random and I don't care about threads, I am just going to use a static random. (If you are interested in using a safer thread-safe random in .NET, [here is an example](http://csharpindepth.com/Articles/Chapter12/Random.aspx).)

In **OTPController**, add the below declaration in the top of you class:

```csharp
private static Random rng = new Random();
```

In the **RequestOTP** method, we now need to:

> 1.  Seed a code
> 2.  Create new OPTCode object
> 3.  Add the OTP code to the OTPCodes list
> 4.  Send an SMS to the number submitted

```csharp
[HttpGet]
public async Task<HttpResponseMessage> RequestOTP(string phoneNumber)
{
    int value = rng.Next(100,9999); //1
    string code = value.ToString("0000");
    OTPCodes.Add(new OTPCode {PhoneNumber = phoneNumber, Code = code});//2 and 3

    //4 send an SMS with the code
    try
    {
        // SMS client will throw an error if something goes wrong
        var message = string.Format("Your code:{0} for verifying your number with me", code);
        var number = phoneNumber.Trim();
        Client smsClient = new Client("key", "secret");
        await smsClient.SendSMS(number,message);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
    catch (Exception ex)
    {
        // handle error here, see :ref:[here](doc:sms-guide) for possible errors
        return new HttpResponseMessage(HttpStatusCode.InternalServerError);
    }
}
```

In a production application, you will most likely use Sinch to verify the format of a number before sending.

Also, in a production app, you might want to wait to return until Sinch knows the message has been delivered to the operator by using:

```csharp
smsClient.CheckStatus(messageid);
```

## Verifying codes

To verify the code, the user simply enters the code in the client app and the app submits the code and phone number. The system checks if they match, and if they do, success\!

```csharp
[HttpGet]
public HttpResponseMessage VerifyOTP(string phoneNumber, string code)
{
    if (OTPCodes.Any(otp => otp.PhoneNumber == phoneNumber && otp.Code == code))
    {
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
    else
    {
        return new HttpResponseMessage(HttpStatusCode.NotFound);
    }
}
```

## Testing it out with Postman

I like to use Postman for Chrome to test out my REST APIs. You can get it [here](https://www.google.se/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0CCAQFjAA&url=https%3A%2F%2Fchrome.google.com%2Fwebstore%2Fdetail%2Fpostman-rest client%2Ffdmmgilgnpjigdojojpjoooidkmcomcm%3Fhl%3Den&ei=1nbCVPyzBoXuyQOG-4K4DA&usg=AFQjCNHaecLwAKk91gpdCY_y1x_ViIrHwQ&bvm=bv.84349003,d.ZWU).

Hit F5 to start the app locally and make a note of the port. In my case, it was 2945. Then head over to `http://localhost:yourport/Help` and you should see how to call our new API.

In Postman, generate an SMS:
![postman_generate.png](images\012ac31-postman_generate.png)

And to verify code:
![postman_verify.png](images\c045b11-postman_verify.png)
Testing a C# authentication system
