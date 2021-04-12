---
title: Miscellaneous
excerpt: ''
hidden: false
---

## Minimum Requirements

We officially support the 3 latest major Android versions. You can try older versions but there are no guarantees it will work as expected.

## Note on Sinch SDK Size

The _Sinch Android SDK aar_ library includes a common Java library of approximately 900 KB, and four binaries containing the architectures _armv7_, _arm64_, _x86_, and _x86_64_, which size varies from 6.2 MB for _armv7_ to 11.1 MB for _x86_64_, with the _armv64_ having 10.2 MB in size. Combined and compressed in the _aar_ it yields over 17 MB in size. To reduce the footprint, it is highly recommended to use the _Android App Bundle_ publishing format (see https://developer.android.com/platform/technology/app-bundle). In that case, only the common Java code and target architecture binary will be included in the final package.

As an example, the rough estimation of typical _arm64_ distribution will have 900 KB + 10.2 MB yielding slightly over 11 MB before compression (which is hard to estimate, but can be assumed within 1x-2x range).
You can always check your app download and install size in the Google Play Console (https://support.google.com/googleplay/android-developer/answer/9302563)

## Restrictions on User IDs

User IDs **must not** be longer than **255** bytes, **must** only contain URL-safe characters, and be restricted to the following character set:

```text
ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghjiklmnopqrstuvwxyz0123456789-_=
```

If you need to use _User IDs_ containing characters outside the allowed set above, you could consider _base64_-encoding the raw _User IDs_ using a URL-safe base64 alphabet as described in https://tools.ietf.org/html/rfc4648#section-5). Note how the allowed character set overlaps with the URL-safe base64 alphabet, but does __NOT__ allow characters in the __non__-URL-safe alphabet, e.g. `/` (forward slash) and `+` (plus sign).

## Encryption Export Regulations

Please check the Summary of U.S. Export Controls Applicable to Commercial Encryption Products and ensure that the application is registered for the Encryption Regulations, if applicable. It can be found under this [link](https://www.bis.doc.gov/index.php/policy-guidance/encryption).

## Statistics

The Sinch SDK client uploads statistics to the Sinch servers at the end of a call, a call failure, or a similar event. The statistics are used for the monitoring of network status, call quality, and other aspects regarding the general quality of the service.

Some of the information **is not** anonymous and may be associated with the User ID call participants.

The statistics upload is done by the client in the background.

## Third-Party libraries and Copyright Notices

All Third Party Libraries and Copyright notices can be found under this [link](http://www.sinch.com/legal/third-party-licenses/).
