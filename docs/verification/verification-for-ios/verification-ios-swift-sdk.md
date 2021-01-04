---
title: Swift SDK
excerpt: >-
  Verify users with Swift. The Sinch iOS Verification SDK is available for
  Swift.

---

The Sinch Verification iOS SDK is also available for [Swift](https://developer.apple.com/).

## Example usage

```swift
import SinchVerification;

do {

    let input = "<user input>"

    // Get user's current region by carrier info
    let defaultRegion = DeviceRegion.currentCountryCode()

    let phoneNumber = try PhoneNumberUtil().parse(input, defaultRegion:defaultRegion)
    let phoneNumberE164 = PhoneNumberUtil().format(phoneNumber, format: PhoneNumberFormat.E164)

    let verification = SMSVerification(applicationKey:"<APP KEY>", phoneNumber: phoneNumberInE164)

    verification.initiate { (result: InitiationResult, error: NSError?) -> Void in
        // handle outcome
    }

    let code = "<user input code from SMS>"

    verification.verify(code, completion: { (success: Bool, error:NSError?) -> Void in
        // handle outcome
    })

} catch let error as PhoneNumberParseError {
    // Handle phone number parsing error, i.e. invalid user input.
}
```

## Importing the Sinch Verification Swift SDK

### Import as CocoaPod

If you are using [CocoaPods](http://www.cocoapods.org), add the following to your Podfile:

```objectivec
platform :ios, '8.0'

target '<your target>' do
    use_frameworks!
    pod 'SinchVerification-Swift'
end
```

> **Note**
>
> The directive \`\`use*frameworks\!\`\` is necessary to make cocoapods add the framework to '\_Embedded Binaries*'

#### Note on uploading to App Store / iTunes Connect

If you integrate the Sinch SDK via _CocoaPods_, you must not check the option "Include bitcode" when uploading the application binary. The reason is a [bug in CocoaPods](https://github.com/CocoaPods/CocoaPods/issues/4624). In the upload dialog in Xcode Organizer it should look something like this:
![swift-dont-include-bitcode-upload.png](images\ebf587e-swift-dont-include-bitcode-upload.png)

### Import SinchVerification.framework as _Embedded Binaries_

1.  In the Sinch Verification SDK package (SinchVerification-iOS-{VERSION}.tar) there is a _SinchVerification.framework_ bundle in the directory named `swift`:

![add-frameworks-package-structure.png](images\2cbde47-add-frameworks-package-structure.png)

2.  Add it to the Xcode target build settings section _Embedded Binaries_ (under _General_):

![add-frameworks-embedded-binaries.png](images\089382c-add-frameworks-embedded-binaries.png)

3.  In the Xcode target build settings, verify that the setting _Runpath Search Paths_ is set to `@executable_path/Frameworks`.

![add-frameworks-search-paths.png](images\e0bad42-add-frameworks-search-paths.png)

4.  Setup to run `strip-frameworks.sh` as a _"Run Script Phase"_
    Create a new _"Run Script Phase"_ in your application target's _"Build Phases"_ and paste the following snippet:

```shell
bash ${BUILT_PRODUCTS_DIR}/${FRAMEWORKS_FOLDER_PATH}/SinchVerification.framework/strip-frameworks.sh
```

![swift-run-script-add.png](images\d73dab6-swift-run-script-add.png)

![swift-run-script-contents.png](images\0f8e419-swift-run-script-contents.png)

(This step is required to work around an App Store / iTunes Connect bug http://www.openradar.me/radar?id=6409498411401216)

5.  Make sure that _libc++_, _libz_ and _libicucore_ are linked (See [here](doc:verification-ios-first-time-setup))

![link-binary-with-libraries-xcode.png](images\9b2867b-link-binary-with-libraries-xcode.png)

**That's it\!**

Now the SDK can be imported as follows (from any of your .swift files)

```swift
import SinchVerification
```

> **Note**
>
> This import method requires iOS Deployment Target iOS 8.0 or above

       "https://files.readme.io/0f8e419-swift-run-script-contents.png",
        "swift-run-script-contents.png",
        732,
        151,
        "#cecece"
      ]
    }

]
}

(This step is required to work around an App Store / iTunes Connect bug http://www.openradar.me/radar?id=6409498411401216)

5.  Make sure that _libc++_, _libz_ and _libicucore_ are linked (See [here](doc:verification-ios-first-time-setup))

![link-binary-with-libraries-xcode.png](images\9b2867b-link-binary-with-libraries-xcode.png)

**That's it\!**

Now the SDK can be imported as follows (from any of your .swift files)

```swift
import SinchVerification
```

> **Note**
>
> This import method requires iOS Deployment Target iOS 8.0 or above
