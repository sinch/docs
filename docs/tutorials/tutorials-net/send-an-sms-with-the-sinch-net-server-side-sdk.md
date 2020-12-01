---
title: Send an SMS with the Sinch .Net server-side SDK
excerpt: >-
  In this tutorial we’ll be creating an application to send an SMS. We’ll be
  using a clientapi environment, and in the clientapi, you can only send
  messages to verified numbers on your account.
hidden: 'true'
---
If you thought SMS was old fashioned, think again. 96% of smartphone users still use SMS. It has a 90% read rate in minutes and higher engagement rates than emails and OTT apps, which makes it very reach effective in terms of timeliness. SMS is the one solution, not requiring app installations or friend requests, not relying on tech heavy solutions or the operator at hand.

’Nuff said about the good old short message service for now. In this tutorial we’ll be creating an application to send an SMS. We’ll be using the clientapi environment, and in the clientapi, you can only send messages to verified numbers on your account.

## Setup

In [the dashboard](https://portal.sinch.com/#/login), ensure you have a phone number on the Account page. If not, click “Add a phone number”.
![manage-account.png](images\20db9b9-manage-account.png)

Enter your number and submit the PIN code you will receive in the verification SMS.
![verify-number.png](images\0a4a666-verify-number.png)

You’ll now have the number attached to your Sinch account, and will also have been credited $2 as a bonus for verifying your number\!
![account.png](images\2d986e5-account.png)

Create a new application from the “Apps” tab. Leave the environment to the default: clientapi.


Click on the key symbols and take note of your application’s key and secret, as they’ll be used in the Sinch Server SDK shortly.
![keys-and-secret.png](images\7ba3076-keys-and-secret.png)

Open Visual Studio and create a new Console Application:
![new-project.png](images\b389852-new-project.png)

Right click on References in Solution Explorer and select **“Manage NuGet Packages…”**.

If you’re using an older version of Visual Studio and don’t see this, you’ll need to add it from here: <http://docs.nuget.org/consume/installing-nuget>.

Search for `Sinch.ServerSdk` and click **“Install”** for **“Sinch.ServerSdk”**.
![nugget.png](images\80117cf-nugget.png)

## MainAsync( )
Because of the limitations of the compiler, having an asynchronous `Main()` method is not allowed. To overcome this obstacle, and to make for a more straightforward tutorial, we’ll create this method:

```csharp
private static async Task MainAsync()
{
}
```

Then, we want to add the following line of code to `Main()`:

`MainAsync().GetAwaiter().GetResult();`

Add the following lines to import the SDK namespace: using `Sinch.ServerSdk;`

## Send SMS

To send an SMS, we’ll add the following to `MainAsync()`:

```csharp
var smsApi = SinchFactory.CreateApiFactory("yourkey", "yoursecret").CreateSmsApi();
var sendSmsResponse = await smsApi.Sms("+61491570156", "Hello world.  Sinch SMS here.").Send();
```

Remember to replace the application key and secret with your own, and change the phone number and message accordingly. It might be worth setting up a unique long number or an [SMS short code](https://www.sinch.com/products/messaging/numbers/short-codes/).

## Check status
Next, we want to check the status of an SMS. Add these couple of lines:

```csharp
var smsMessageStatusResponse = await smsApi.GetSmsStatus(sendSmsResponse.MessageId);
Console.WriteLine(smsMessageStatusResponse.Status);
Console.ReadLine();
```

It might be worthwhile putting a `Task.Delay()` before checking the status, as it could take a second or two for the SMS to be delivered. Checking immediately will most likely lead to a status of **“Pending”**.

## Celebrate
![command-line.png](images\99ae63b-command-line.png)

You’re done. As usual, you can download the complete, working example project on [Github](https://github.com/sinch/nuget-serversdk-tutorial). Go charm those 96%\!

## Full program code

```csharp
using System;
using System.Threading.Tasks;
using Sinch.ServerSdk;

namespace Sinch.SendSms.Example
{
    internal class Program
    {
        private static void Main()
        {
            MainAsync().GetAwaiter().GetResult();
        }

        private static async Task MainAsync()
        {
            var smsApi = SinchFactory.CreateApiFactory("00000000-0000-0000-0000-000000000000", "AAAAAAAAAAAAAAAAAAAAAA==").CreateSmsApi();
            var sendSmsResponse = await smsApi.Sms("+61491570156", "Hello world.  Sinch SMS here.").Send();

            await Task.Delay(TimeSpan.FromSeconds(10)); // May take a second or two to be delivered.

            var smsMessageStatusResponse = await smsApi.GetSmsStatus(sendSmsResponse.MessageId);
            Console.WriteLine(smsMessageStatusResponse.Status);
            Console.ReadLine();
        }
    }
}
```
