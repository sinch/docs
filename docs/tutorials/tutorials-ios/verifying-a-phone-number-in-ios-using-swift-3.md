---
title: Verifying a Phone Number in iOS using Swift 3
excerpt: >-
  In this tutorial you’ll learn how to verify a user’s phone number using our
  Swift framework.
---
In this tutorial you’ll learn how to verify a user’s phone number using our Swift framework. We support two methods of verifying on iOS:

>   - Standard [SMS PIN Verification](https://www.sinch.com/products/verification/sms/)
>   - [Callout Verification](https://www.sinch.com/products/verification/) where we place a call to the number and the user then presses 1 to confirm that they wanted the call

We also offer [Flash Call Verification](https://www.sinch.com/products/verification/flash-call/), but this is available for Android only.

At the end of this tutorial we will have a basic app that looks like this:



![screen2.png](images\3f17669-screen2.png)

## Getting Started

### Sinch Setup

> 1.  [Create an account](https://portal.sinch.com/#/signup)
> 2.  Create an app and change set authorization to public (for now) 
![configureapp.png](images\c8676b2-configureapp.png)

> 3.  Take note of the application key

Head over to Xcode to get some basic setup.

### Xcode Setup

> 1.  Create a new single view project
> 2.  Add a cocoapods file and install pods
> 3.  A physical device with SIM card

```text
platform :ios, '8.0'
use_frameworks!
pod 'SinchVerification-Swift'
```

## Building the first screen UI

Open up your workspace and go to **main.storyboard** file. Then open up assistant view so also see **ViewController.swift**:

> 1.  Add a textfield and add an outlet called **phoneNumber**. Set the keyboard type of the field to phone number
> 2.  Add an SMS verification button and create an action called **smsVerification**
> 3.  Add a callout verification button and create an action called **calloutVerification**
> 4.  Add a label and call an outlet called **status**
> 5.  Add an activity indicator and an outlet called **spinner**, and then check the box to hide when no animated
> 6.  Embed the ViewController in a navigation controller editor

Add your constraints and the first screen is done. The next thing we are going to do is to add some code to do a callout verification. I want to start with this because the callout verification does not require any additional screens.

## Callout Verification 
The verification flow for a callout is pretty neat. Just imitate a callout verification and when the callback comes to the client you are actually done.

How does it work? Sinch will place a call to the given phone number and when the user picks up we prompt the user to press 1. If they do it’s a success, but if they don’t then it is treated as a fail (or if they don’t pick up etc).

Open up **ViewController.swift** and add an import for
**SinchVerification**:

```swift
import SinchVerification;
```

At the top of your class, add two variables to hold the verification and one to hold the application key.

```swift
var verification:Verification!;
var applicationKey = "your key";
```

Great\! Now we want to start a callout verification once the user clicks on the callout verification button.

```swift
@IBAction func calloutVerification(_ sender: AnyObject) {
       disableUI(true);
       verification = CalloutVerification(applicationKey,
           phoneNumber: phoneNumber.text!);
       verification.initiate { (result: InitiationResult, error: NSError?) -> Void in
           self.disableUI(false);
           self.status.text = (success.success ? "Verified" : error?.localizedDescription);
       }
   }
```

As you can see that’s not a lot of code to make this roll. You might have noticed that I have a **disbleUI(Bool)** call in there, and that’s a small method to disable the UI while waiting for the call. This is important to do because if the user starts multiple verification requests they might get stuck in a loop where they never get verified and the phone just keeps ringing. I implemented a timeout for 30 seconds before I consider it to be a fail and the user can try again.

```swift
func disableUI(_ disable: Bool){
        var alpha:CGFloat = 1.0;
        if (disable) {
            alpha = 0.5;
            phoneNumber.resignFirstResponder();
            spinner.startAnimating();
            self.status.text="";
            let delayTime =
                        DispatchTime.now() +
                        Double(Int64(30 * Double(NSEC_PER_SEC)))
                        / Double(NSEC_PER_SEC)
            DispatchQueue.main.asyncAfter(
            deadline: delayTime, execute:
            { () -> Void in
                self.disableUI(false);
            });
        }
        else{
            self.phoneNumber.becomeFirstResponder();
            self.spinner.stopAnimating();

        }
        self.phoneNumber.isEnabled = !disable;
        self.smsButton.isEnabled = !disable;
        self.calloutButton.isEnabled = !disable;
        self.calloutButton.alpha = alpha;
        self.smsButton.alpha = alpha;
    }
```

Time to add some nicer UI. Add a **viewWillAppear** and set the phone number to first responder.

```swift
override func viewWillAppear(_ animated: Bool) {
        phoneNumber.becomeFirstResponder();
        disableUI(false);

}
```

Nothing too fancy as you can see. Run the app and try it out. Pretty sweet right?

## Adding SMS Verification

Another way of adding verification is the classic SMS PIN method, which I’m sure you have used. The downside of SMS, in my opinion, is that you need the user to enter a code which does add some friction to the user experience.

To accomplish an SMS verification, you will need a new view where a user can enter a code. Add a new ViewController to the solution and call in **EnterCodeViewController.swift**.

Open up **Main.Storyboard** and add a view controller to the board and set the first responder to the newly created Controller

> 1.  Add a textfield and an outlet called **code**
> 2.  Add a button and an action called **verify**
> 3.  Add label and an outlet called **status**
> 4.  Lastly a spinner and an outlet called **spinner**
> 5.  Add a segue from **ViewController.swift** to **EnterCodeViewController.swift** and call it **enterPin**.

Add your constraints and make it look how you want, but it should look something like this:
![screen2.png](images\96c697e-screen2.png)

### Initiating an SMS verification

Initiating an SMS verification is very similar to Callout. The big difference here is when you get the success callback, it doesn’t mean its verified, it just means that we have sent an SMS. What we need to do after that is to send in a code that we get from user input to verify the code. In this case we do that in a separate view controller. So once we have the success, we perform the segue to show the entertain controller.

```swift
@IBAction func smsVerification(_ sender: AnyObject) {
    self.disableUI(true);
    verification = SMSVerification(applicationKey, phoneNumber: phoneNumber.text!)

    verification.initiate { (result: InitiationResult, error:Error?) -> Void in
        self.disableUI(false);
        if (success.success){
            self.performSegue(withIdentifier: "enterPin", sender: sender)

        } else {
            self.status.text = error?.localizedDescription
        }
    }
}
```

To verify a verification, you need to keep the current verification object, so in in **prepareForSegue** we want to pass on the current verification object so we can call verify on it.

```swift
override func prepare(for segue: UIStoryboardSegue, sender: Any!) {
    if (segue.identifier == "enterPin") {
        let enterCodeVC = segue.destination as! EnterCodeViewController;
        enterCodeVC.verification = self.verification;
    }

}
```

Now that’s out of the way, open up **EnterCodeViewController.swift** and go to the action **verify** and set up the UI for making a verification request, and call verify on your verification object.

```swift
@IBAction func verify(sender: AnyObject) {
    spinner.startAnimating();
    verifyButton.isEnabled = false;
    status.text  = "";
    pinCode.isEnabled = false;
    verification.verify(
        pinCode.text!, completion:
        { (success:Bool, error:Error?) -> Void in
            self.spinner.stopAnimating();
            self.verifyButton.isEnabled = true;
            self.pinCode.isEnabled = true;
            if (success) {
                self.status.text = "Verified";
            } else {
                self.status.text = error?.localizedDescription;
            }
    });
}
```

You now have a verified number for your user. With this implementation you only know on the client side that the number is verified. In a real world app, you would need to tell your backend that the number is verified. You could accomplish that in two ways. Either calling that update on the success flow from the client. Or your own callbacks that we have for verification (recommended).

For more details, check out our [verification documentation](doc:verification-rest-callback-api).

