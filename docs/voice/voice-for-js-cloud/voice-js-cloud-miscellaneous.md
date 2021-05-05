---
title: Miscellaneous
excerpt: ''
hidden: false
---

## Minimum Requirements

For desktop use of the Javascript SDK, the latest version of a browser that supports Javascript is recommended, for example  Chrome, Firefox, Safari or Edge.  Chrome and Safari browsers are recommended on mobile platforms.


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
